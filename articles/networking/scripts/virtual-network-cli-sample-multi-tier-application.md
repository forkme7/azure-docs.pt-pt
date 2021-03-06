---
title: "Script CLI do Azure de exemplo - criar uma rede para aplicações de várias camadas | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar uma rede virtual para aplicações de várias camadas."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: aa5fef6e8910a5b0b5fe89d5c8cfb141415d07a6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-network-for-multi-tier-applications"></a>Criar uma rede para aplicações de várias camadas

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end é limitado para HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. Depois de executar o script, tem duas máquinas virtuais, um em cada sub-rede que pode implementar MySQL software e do servidor web.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e os grupos de segurança de rede. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e a sub-rede do front-end. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria uma sub-rede de back-end. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público para aceder a VM a partir da Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Cria interfaces de rede virtual e anexa-las a sub-redes de front-end e back-end à rede virtual. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Cria grupos de segurança (NSG) que estão associados às sub-redes de front-end e back-end de rede. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [az vm create](/cli/azure/vm#az_vm_create) | Cria máquinas virtuais e anexa um NIC para cada VM. Este comando também especifica a imagem de máquina virtual para utilizar e credenciais administrativas. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos e todos os recursos que nele contidos. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos de script CLI redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../cli-samples.md)