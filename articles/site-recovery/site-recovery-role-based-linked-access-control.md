---
title: Utilizar o controlo de acesso baseado em funções para gerir o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como aplicar e utilizar o controlo de acesso baseado em funções (RBAC) para gerir as implementações do Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: d02ecc3293d0607dd3e19ade3c1d9087b544703b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Utilizar o controlo de acesso baseado em funções para gerir implementações do Azure Site Recovery

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizar o RBAC, pode segregar responsabilidades na sua equipa e conceder apenas permissões de acesso específico aos utilizadores conforme necessário para efetuar tarefas específicas.

O Azure Site Recovery fornece 3 funções incorporadas para controlar as operações de gestão do Site Recovery. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Contribuinte do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação de desastres que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completos, como o caso may seja.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e a Reativação Pós-falha. Um utilizador com esta função não é possível ativar ou desativar a replicação, criar ou eliminar cofres, registar a nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação de desastre quem pode máquinas virtuais de ativação pós-falha ou as aplicações quando indicado pelos proprietários da aplicação e os administradores de TI uma situação real ou simulado desastre como uma DR desagregar. Post resolução de desastre, o operador de DR pode voltar a proteger e a reativação pós-falha as máquinas virtuais.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de IT que pode monitorizar o estado atual da proteção e emitir pedidos de suporte, se necessário.

Se estiver à procura para definir as suas próprias funções para o controlo ainda mais, consulte como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para ativar replicação para novas máquinas virtuais
Quando uma nova máquina Virtual é replicado para o Azure utilizando o Azure Site Recovery, níveis de acesso do utilizador associada são validadas para garantir que o utilizador tem as permissões necessárias para utilizar os recursos do Azure fornecidos ao Site Recovery.

Para ativar a replicação para uma nova máquina virtual, tem de ter um utilizador:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão para escrever na conta de armazenamento selecionada

Um utilizador tem as seguintes permissões de replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que as permissões relevantes estão adicionadas por modelo de implementação (Gestor de recursos / clássico) utilizado para a implementação de recursos.

| **Tipo de Recurso** | **Modelo de implementação** | **Permissão** |
| --- | --- | --- |
| Computação | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clássica | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rede | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clássica | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Armazenamento | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clássica | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de Recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere utilizar o 'Contribuinte de Máquina Virtual' e 'Clássico contribuinte de Máquina Virtual' [funções incorporadas](../role-based-access-control/built-in-roles.md) para a implementação do Resource Manager e clássico modelos, respetivamente.

## <a name="next-steps"></a>Passos Seguintes
* [Controlo de acesso baseado em funções](../role-based-access-control/role-assignments-portal.md): começar a utilizar o RBAC no portal do Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas de controlo de acesso baseado em funções](../role-based-access-control/troubleshooting.md): Obtenha sugestões sobre como corrigir problemas comuns.
