---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8dec00373d991a7aeaf11f1a4d95463ab71d9a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
1. No [portal](http://portal.azure.com), navegue para a rede virtual do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** da página da VNet, clique em **Sub-redes** para expandir a página **Sub-redes**.
3. Na página **Sub-redes**, clique em **+Sub-rede do gateway** para abrir a página **Adicionar sub-rede**. 

  ![Add the gateway subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Add the gateway subnet")
4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de preenchimento automático **Intervalo de endereços** para corresponder aos requisitos de configuração e, em seguida, clique em **OK** na parte inferior da página para criar a sub-rede.

  ![Adding the subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adding the subnet")