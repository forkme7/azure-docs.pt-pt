---
title: Controlar o encaminhamento na uma rede Virtual do Azure - CLI - clássico | Microsoft Docs
description: Saber como controlar o encaminhamento na VNets utilizando a CLI do Azure no modelo de implementação clássica
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: f158270df344ebd55601b3f625aaabc284ad1323
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar o encaminhamento e utilizar aplicações virtuais (clássicas) utilizando a CLI do Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [Modelo](virtual-network-create-udr-arm-template.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [controlar o encaminhamento e utilizar aplicações virtuais no modelo de implementação Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam num ambiente simple já criado com base no cenário acima. Se pretender executar os comandos, como são apresentados neste documento, criar o ambiente mostrado na [criar uma VNet (clássica) com a CLI do Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar UDR para a sub-rede do front-end
Para criar a tabela de rota e a rota necessários para a sub-rede do front-end com base no cenário acima, siga os passos abaixo.

1. Execute o seguinte comando para mudar para modo clássico:

    ```azurecli
    azure config mode asm
    ```

    Saída:

        info:    New mode is asm

2. Execute o seguinte comando para criar uma tabela de rota para a sub-rede do front-end:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Saída:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parâmetros:
   
   * **-l (ou --location)**. Região do Azure onde será criado o NSG de novo. Para o nosso cenário *westus*.
   * **-n (ou --name)**. Nome do novo NSG. Para o nosso cenário *NSG-front-end*.
3. Execute o seguinte comando para criar uma rota na tabela de rota para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para o **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Saída:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parâmetros:
   
   * **-r (ou – nome da tabela de rota)**. Nome da tabela de rotas onde será adicionada a rota. Para o nosso cenário *UDR-front-end*.
   * **-a (or --address-prefix)**. Prefixo de endereço para a sub-rede onde os pacotes são destinados à. Para o nosso cenário *192.168.2.0/24*.
   * **-t (ou --tipo de salto seguinte)**. Tipo de objeto tráfego será enviado para. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, ou *nenhum*.
   * **-p (– seguinte-hop-endereço ip ou -**). Endereço IP de próximo salto. Para o nosso cenário *192.168.0.4*.
4. Execute o seguinte comando para associar a tabela de rotas criada com o **front-end** sub-rede:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Saída:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parâmetros:
   
   * **-t (ou -- vnet-name)**. Nome da VNet onde a sub-rede está localizada. Para o nosso cenário *TestVNet*.
   * **-n (ou - nome de sub-rede**. Nome da tabela de rota será adicionada à sub-rede. Para o nosso cenário *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede de back-end
Para criar a tabela de rotas e necessários para a sub-rede de back-end com base no cenário de rota, conclua os seguintes passos:

1. Execute o seguinte comando para criar uma tabela de rota para a sub-rede de back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Execute o seguinte comando para criar uma rota na tabela de rota para enviar todo o tráfego destinado à sub-rede front-end (192.168.1.0/24) para o **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas com o **back-end** sub-rede:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

