---
title: Balanceador de carga de vários Frontends do Azure | Microsoft Docs
description: Descrição geral de vários Frontends no balanceador de carga do Azure
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: ''
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: cf8fa396e0518e1c847225dfc1d8f91c3421bd11
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Vários Frontends Balanceador de carga do Azure

Balanceador de carga do Azure permite-lhe carregar saldo de serviços em várias portas, vários endereços IP ou ambos. Pode utilizar definições de Balanceador de carga interno e público para fluxos de balanceamento de carga através de um conjunto de VMs.

Este artigo descreve as noções básicas sobre esta capacidade, conceitos importantes e as restrições. Se pretender apenas expor os serviços num endereço IP, pode encontrar instruções simplificadas para [pública](load-balancer-get-started-internet-portal.md) ou [interno](load-balancer-get-started-ilb-arm-portal.md) configurações de Balanceador de carga. A adição de várias frontends é incremental para uma configuração de front-end único. Utilizar os conceitos neste artigo, pode expandir uma configuração simplificada em qualquer altura.

Quando definir um balanceador de carga do Azure, estão ligados com regras de front-end e uma configuração de conjunto de back-end. A sonda de estado de funcionamento referenciada pela regra é utilizada para determinar como novos fluxos são enviadas para um nó no conjunto back-end. O front-end (aka VIP) é definido por uma 3 cadeias de identificação composta por um endereço IP (interno ou público), um protocolo de transporte (UDP ou TCP) e um número de porta da regra de balanceamento de carga. O conjunto de back-end é uma coleção de configurações de IP de máquinas virtuais (parte do recurso NIC) que referenciam o conjunto de back-end de Balanceador de carga.

A tabela seguinte contém algumas configurações de front-end de exemplo:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A tabela mostra quatro frontends diferentes. Frontends n. º 1, #2 e #3 são um front-end único com várias regras. É utilizado o mesmo endereço IP, mas a protocolo ou a porta é diferente para cada front-end. Frontends n. º 1 e #4 são um exemplo de vários frontends, onde o mesmo protocolo de front-end e a porta são reutilizadas em vários frontends.

Balanceador de carga do Azure fornece flexibilidade na definição de regras de balanceamento de carga. Uma regra declara como um endereço e porta de front-end está mapeado para o endereço de destino e a porta no back-end. Se pretende ou não as portas de back-end são reutilizadas em regras dependem do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar a estrutura de configuração e a sonda do anfitrião. Existem dois tipos de regras:

1. A regra predefinida com nenhuma reutilização de porta de back-end
2. A regra de IP flutuante onde são reutilizadas portas de back-end

Balanceador de carga do Azure permite-lhe combinar ambos os tipos de regra na mesma configuração de Balanceador de carga. O Balanceador de carga pode utilizá-los em simultâneo para uma determinada VM ou qualquer combinação, desde que a cumprir as restrições da regra. O tipo de regra que escolher depende dos requisitos da sua aplicação e a complexidade de oferecer suporte a que a configuração. Deve avaliar que tipos de regra são melhores para o seu cenário.

Vamos explorar estes cenários mais iniciando com o comportamento predefinido.

## <a name="rule-type-1-no-backend-port-reuse"></a>#1 do tipo de regra: nenhum reutilização de porta de back-end

![Vários ilustração de front-end com verde e roxa front-end](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Neste cenário, os frontends estão configuradas da seguinte forma:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![Verde front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![roxa front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

O DIP é o destino do fluxo de entrada. No conjunto back-end, cada VM expõe o serviço numa porta exclusivo num DIP pretendido. Este serviço é associado o front-end através da definição de uma regra.

Iremos definir duas regras:

| Regra | Mapear front-end | Para o conjunto back-end |
| --- | --- | --- |
| 1 |![Verde front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento completado no balanceador de carga do Azure está agora da seguinte forma:

| Regra | Endereço IP de front-end | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP |80 |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP |81 |

Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Variando a porta de destino do fluxo, várias regras podem proporcionar fluxos para o DIP mesmo nas portas diferentes.

Sondas de estado de funcionamento são sempre direcionadas para o DIP de uma VM. Tem de se certificar de se a sua pesquisa reflete o estado de funcionamento da VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regra de tipo #2: reutilização de porta de back-end utilizando o IP flutuante

Balanceador de carga do Azure fornece reutilizar a porta de front-end através de vários frontends independentemente do tipo de regra utilizado. Além disso, alguns cenários de aplicação preferem ou exigem a mesma porta a ser utilizado por várias instâncias de aplicações numa única VM no conjunto de back-end. Exemplos comuns de uma reutilização de porta incluem o clustering de elevada disponibilidade, aplicações virtuais e expor vários pontos finais TLS sem a encriptação de rede.

Se pretender reutilizar a porta de back-end através de várias regras, tem de ativar o IP flutuante na definição da regra.

"IP flutuante" é terminologia do Azure para a parte que é conhecida como direta servidor devolver (DSR). DSR consiste em duas partes: uma topologia de fluxo e um endereço IP esquema de mapeamento. Um nível de plataforma, o Balanceador de carga do Azure sempre funciona numa topologia de fluxo DSR independentemente se o IP flutuante está ativada ou não. Isto significa que a parte de um fluxo de saída é sempre corretamente rescrita para fluxo diretamente para a origem.

Com o tipo de regra predefinida, o Azure expõe uma esquema de mapeamento de endereço IP para facilitar a utilização de balanceamento de carga tradicional. Ativar o IP flutuante altera o esquema de mapeamento de endereço IP para permitir flexibilidade adicional conforme explicado abaixo.

O diagrama seguinte ilustra esta configuração:

![Vários ilustração de front-end com verde e roxa front-end com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, a cada VM no conjunto de back-end tem três interfaces de rede:

* DIP: uma NIC Virtual associada com a VM (configuração de IP do recurso NIC do Azure)
* Front-end 1: uma interface de loopback dentro do SO que está configurado com o endereço IP de front-end 1 convidado
* Front-end 2: uma interface de loopback dentro do SO que está configurado com o endereço IP de front-end 2 convidado

> [!IMPORTANT]
> A configuração das interfaces de loopback é efetuada no SO convidado. Esta configuração não é efetuada ou é gerida pelo Azure. Sem esta configuração, as regras não irão funcionar. Definições de pesquisa de estado de funcionamento, utilize o DIP de VM, em vez da interface de loopback que representa o DSR front-end. Por conseguinte, o serviço tem de fornecer respostas de pesquisa numa porta DIP que reflete o estado do serviço fornecido na interface de loopback que representa o DSR front-end.

Vamos supor que a mesma configuração de front-end como no cenário anterior:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![Verde front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![roxa front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Iremos definir duas regras:

| Regra | Front-end | Mapear para o conjunto back-end |
| --- | --- | --- |
| 1 |![regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (na VM1 e VM2) |
| 2 |![regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (na VM1 e VM2) |

A tabela seguinte mostra o mapeamento completado no balanceador de carga:

| Regra | Endereço IP de front-end | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |mesmo que o front-end (65.52.0.1) |mesmo que o front-end (80) |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |mesmo que o front-end (65.52.0.2) |mesmo que o front-end (80) |

O destino do fluxo de entrada é o endereço IP de front-end na interface de loopback na VM. Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Variando o endereço IP de destino do fluxo, a reutilização de porta é possível na VM do mesma. O serviço está exposto ao balanceador de carga por enlace de endereço IP e porta de interface de loopback copiará o front-end.

Tenha em atenção que este exemplo altere a porta de destino. Apesar de este ser um cenário de IP flutuante, o Balanceador de carga do Azure suporta também definir uma regra de reescrever a porta de back-end de destino e para que seja diferente da porta de destino do front-end.

O tipo de regra de IP flutuante é a base de vários padrões de configuração de Balanceador de carga. Um exemplo que está atualmente disponível é a [AlwaysOn de SQL com vários serviços de escuta](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) configuração. Ao longo do tempo, iremos irá documentos mais um destes cenários.

## <a name="limitations"></a>Limitações

* Múltiplas configurações de front-end só são suportadas com VMs de IaaS.
* Com a regra de IP flutuante, a aplicação tem de utilizar a configuração de IP primária para fluxos de saída. Se vincula a aplicação para o endereço IP de front-end configurado no loopback interface no SO convidado, Azure do realizar o SNAT não está disponível para reescrever o fluxo de saída e o fluxo falha.
* Endereços IP públicos têm um efeito em faturação. Para obter mais informações, consulte [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* São aplicáveis a limites de subscrição. Para obter mais informações, consulte [os limites de serviço](../azure-subscription-service-limits.md#networking-limits) para obter mais detalhes.

## <a name="next-steps"></a>Passos Seguintes

- Reveja [ligações de saída](load-balancer-outbound-connections.md) para compreender o impacto de vários frontends no comportamento de ligação de saída.
