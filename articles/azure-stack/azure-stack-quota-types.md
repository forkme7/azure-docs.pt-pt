---
title: Tipos de quota na pilha do Azure | Microsoft Docs
description: Reveja os tipos diferentes de quota disponíveis para serviços e recursos na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: b68a963dae4b3621bfd9ecdcbc20146d7b20c457
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2018
---
# <a name="quota-types-in-azure-stack"></a>Tipos de quota na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

[Quotas](azure-stack-plan-offer-quota-overview.md#plans) definir limites de recursos que uma subscrição de utilizador pode aprovisionar ou consumir. Por exemplo, uma quota pode permitir que um utilizador criar VMs até cinco. Cada recurso pode ter os seus próprios tipos de quotas.

## <a name="compute-quota-types"></a>Tipos de quota de computação
| **Tipo** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| Número máx. de máquinas virtuais | 20 | O número máximo de máquinas virtuais que pode criar uma subscrição nesta localização. |
| Número máximo de núcleos de máquina virtual | 50 | O número máximo de núcleos que pode criar uma subscrição nesta localização (por exemplo, uma VM A3 tem quatro núcleos). |
| Define o número máx. de disponibilidade | 10 | O número máximo de conjuntos de disponibilidade que podem ser criados nesta localização. |
| Define o número máx. de dimensionamento da máquina virtual | 20 | O número máximo de conjuntos de dimensionamento de máquina virtual que podem ser criados nesta localização. |



## <a name="storage-quota-types"></a>Tipos de quota de armazenamento
| **Item** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| Capacidade máxima (GB) |500 |Capacidade de armazenamento total que pode ser utilizada por uma subscrição nesta localização. |
| Número total de contas de armazenamento |20 |O número máximo de contas do storage que pode criar uma subscrição nesta localização. |

> [!NOTE]  
> Pode demorar até duas horas antes de é aplicada uma quota de armazenamento nova. 
> 
> 

## <a name="network-quota-types"></a>Tipos de quota de rede
| **Item** | **Default value** (Valor predefinido) | **Descrição** |
| --- | --- | --- |
| Número máximo IPs públicos |50 |O número máximo de IPs públicos que pode criar uma subscrição nesta localização. |
| Redes virtuais máx. |50 |O número máximo de redes virtuais que pode criar uma subscrição nesta localização. |
| Gateways de rede virtual máx. |1 |O número máximo de gateways de rede virtual (Gateways de VPN) que pode criar uma subscrição nesta localização. |
| Máx. ligações de rede |2 |O número máximo de ligações de rede (ponto a ponto ou site a site) que pode criar uma subscrição em todos os gateways de rede virtual nesta localização. |
| Balanceadores de carga máx. |50 |O número máximo de balanceadores de carga que pode criar uma subscrição nesta localização. |
| NICs máximos |100 |O número máximo de interfaces de rede que pode criar uma subscrição nesta localização. |
| Grupos de segurança de rede máx. |50 |O número máximo de grupos de segurança de rede que pode criar uma subscrição nesta localização. |

## <a name="view-an-existing-quota"></a>Ver uma quota existente
1. Clique em **mais serviços** > **fornecedores de recursos**.
2. Selecione o serviço com a quota de que pretende visualizar.
3. Clique em **Quotas**e selecione a quota de que pretende visualizar.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre planos, ofertas e quotas.](azure-stack-plan-offer-quota-overview.md)

[Crie quotas ao criar um plano.](azure-stack-create-plan.md)
