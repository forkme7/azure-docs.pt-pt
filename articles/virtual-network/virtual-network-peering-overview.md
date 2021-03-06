---
title: Peering da Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre o peering de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: jdial
ms.openlocfilehash: dcb46c7127590f1986fe1fd66c2f6ac4fd8b107d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

O peering de rede virtual permite ao utilizador ligar duas [redes virtuais](virtual-networks-overview.md) do Azure de forma totalmente integrada. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da infraestrutura principal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual através apenas de endereços IP *privados*. O Azure suporta:
* VNet Peering - Ligar VNets na mesma região do Azure
* Global VNET Peering - Ligar VNets entre regiões do Azure

As vantagens da utilização do peering de redes virtuais, sejam locais ou globais, incluem:

* O tráfego de rede entre redes virtuais em modo de peering é privado. O tráfego entre as redes virtuais é mantido na rede principal da Microsoft. Não é necessária Internet pública, gateways ou encriptação na comunicação entre as redes virtuais.
* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade dos recursos numa rede virtual comunicarem com os recursos numa rede virtual diferente, assim que as redes virtuais forem colocadas em modo de peering.
* A capacidade de transferir dados entre subscrições do Azure, modelos de implementação e entre regiões do Azure.
* A capacidade de configurar o peering entre redes virtuais criadas através do Azure Resource Manager ou o peering entre uma rede virtual criada através do Resource Manager e outra criada através do modelo de implementação clássica. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).
* Sem períodos de indisponibilidade para recursos em qualquer rede virtual durante ou após a criação do peering.

## <a name="connectivity"></a>Conectividade

Depois das redes virtuais serem colocadas em modo de peering, os recursos em cada uma destas podem ligar-se diretamente a recursos na rede virtual em modo de peering.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à latência numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

Os grupos de segurança de rede podem ser aplicados a qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes, se assim o desejar.
Ao configurar o peering de rede virtual, pode abrir ou fechar as regras do grupo de segurança de rede entre as redes virtuais. Se abrir a conectividade total entre as redes virtuais em modo de peering (que é a opção predefinida), pode aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas, para bloquear ou negar o acesso específico. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Encadeamento de serviços

Para ativar o encadeamento de serviços, pode configurar rotas definidas pelo utilizador que apontem para máquinas virtuais em redes virtuais em modo de peering como o endereço IP de *próximo salto* ou para gateways de rede virtuais. O encadeamento se serviços permite-lhe encaminhar o tráfego de uma rede virtual para um dispositivo de rede numa rede virtual, ou gateways de rede virtual, em modo de peering através de rotas definidas pelo utilizador.

Pode implementar redes "hub-and-spoke", nas quais o hub de rede virtual pode alojar componentes de infraestruturas tais como aplicações virtuais de rede ou gateways VPN. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego pode fluir dos dispositivos de rede virtual ou gateways VPN na rede virtual do concentrador. 

O peering de rede virtual permite que o próximo salto numa rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering ou um gateway VPN. No entanto, não é possível encaminhar rotas entre redes virtuais com uma rota definida pelo utiliza a especificar um gateway ExpressRoute como o tipo de salto seguinte. Para saber mais sobre as rotas definidas pelo utilizador, veja [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede hub-and-spoke, veja [Topologia de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, independentemente de estar ou não em modo de peering com outra, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar a uma rede no local. Também pode configurar [ligações de rede virtual a rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de gateways, mesmo que as redes virtuais estejam em modo de peering.

Quando ambas as opções de inter-conetividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais irá fluir através da configuração em modo de peering (ou seja, através da estrutura principal do Azure).

Quando as redes virtuais estão em modo de peering na mesma região, também pode configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter um gateway próprio. Uma rede virtual pode ter apenas um gateway. O gateway pode ser local ou remoto (na rede virtual em modo de peering), conforme mostrado na imagem seguinte:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/figure04.png)

O trânsito de gateway não é suportado na relação de peering entre redes virtuais criadas através de modelos de implementação diferentes ou em regiões diferentes. Para que o trânsito de gateway funcione, é necessário que ambas as redes virtuais na relação de peering tenham sido criadas através do Resource Manager e estejam na mesma região.

Quando as redes virtuais que partilham uma única ligação ExpressRoute do Azure estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar o trânsito para conectividade no local.

## <a name="troubleshoot"></a>Resolução de problemas

Para confirmar o peering de uma rede virtual peering, pode [verificar as rotas efetivas](virtual-network-routes-troubleshoot-portal.md) para uma interface de rede em qualquer sub-rede numa rede virtual. Se um peering de rede virtual existe, todas as sub-redes na rede virtual têm rotas com o tipo de salto seguinte *VNet peering*, para cada espaço de endereços em cada virtual rede peered.

Também pode resolver problemas relacionados com a conectividade a uma máquina virtual numa rede virtual em modo de peering através da [verificação de conectividade](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede. A verificação da conectividade permite-lhe ver como o tráfego é encaminhado da interface de rede de uma máquina de virtual de origem para a interface de rede de uma máquina de virtual de destino.

## <a name="requirements-and-constraints"></a>Requisitos e limitações

Para saber mais sobre os requisitos e as limitações, veja [Virtual network peering requirements and constraints](virtual-network-manage-peering.md#requirements-and-constraints) (Requisitos e limitações do peering de rede virtual). Para saber mais sobre os limites ao número de peerings que pode criar para uma rede virtual, veja [Azure networking limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) (Limites de rede do Azure). 

## <a name="permissions"></a>Permissões

Para saber mais sobre as permissões necessárias para criar um peering de rede virtual, veja [Virtual network peering permissions](virtual-network-manage-peering.md#permissions) (Permissões de peering de rede virtual).

## <a name="pricing"></a>Preços

Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize uma ligação de peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Passos seguintes

* É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure             | Subscrição  |
    |---------                          |---------|
    |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Saiba como criar uma [topologia de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Saiba tudo sobre [as definições de peering de rede virtual e como alterá-las](virtual-network-manage-peering.md).
