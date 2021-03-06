---
title: Migrar a conta de automatização e de recursos
description: Este artigo descreve como mover uma conta de automatização na automatização do Azure e aos recursos associados a partir de uma subscrição para outro.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a9e76634a7392bca93ba06749741505e7b34cb41
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="migrate-automation-account-and-resources"></a>Migrar a conta de automatização e de recursos
Para as contas de automatização e os respetivos recursos associados (ou seja, recursos, runbooks, módulos, etc.) que tenha criado no portal do Azure e pretender migrar de um grupo de recursos para outro ou a partir de uma subscrição para outro, pode conseguir isto facilmente com o [mover recursos](../azure-resource-manager/resource-group-move-resources.md) funcionalidade disponível no portal do Azure. No entanto, antes de prosseguir com esta ação, deve rever primeiro o seguinte [lista de verificação antes de mover os recursos](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) e além disso, a lista seguinte específica de automatização.  

1. O grupo de recursos/subscrição de destino tem de ser na mesma região que a origem. Ou seja, não não possível mover as contas de automatização em regiões.
2. Quando mover os recursos (por exemplo, runbooks, tarefas, etc.), o grupo de origem e o grupo de destino estão bloqueados durante a operação. Escrever e operações de eliminação são bloqueados nos grupos até concluir a movimentação. 
3. Todos os runbooks ou variáveis, o que fazer referência a um ID de recurso ou na subscrição da subscrição existente, devem ser atualizadas depois de concluída a migração.  

> [!NOTE]
> Esta funcionalidade não suporta mover recursos de automatização clássico.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Para mover a conta de automatização com o portal
1. Da sua conta de automatização, clique em **mover** na parte superior da página.<br> ![Opção de mover](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Optar por mover a conta de automatização para outro grupo de recursos, ou outra subscrição.
3. No **mover recursos** painel, tenha em atenção que apresenta recursos relacionados com a sua conta de automatização e os grupos de recursos. Selecione o **subscrição** e **grupo de recursos** na lista pendente, ou selecione a opção **criar um novo grupo de recursos** e introduza um nome do novo grupo de recursos no campo fornecido. 
4. Rever e selecionar a caixa de verificação para reconhecer *compreender as ferramentas e scripts terão de ser atualizadas para utilizar o novo recurso IDs depois de recursos foram movidos* e, em seguida, clique em **OK**.<br> ![Mover o painel de recursos](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Esta ação pode demorar vários minutos a concluir. No **notificações**, são apresentados com um Estado de cada ação que ocorre - validação, a migração e, em seguida, por fim, quando for concluído.    

## <a name="to-move-the-automation-account-using-powershell"></a>Para mover a conta de automatização com o PowerShell
Para mover os recursos de automatização existentes para outro grupo de recursos ou subscrição, utilize o **Get-AzureRmResource** cmdlet para obter a conta de automatização específica e, em seguida, **mover AzureRmResource** cmdlet para efetuar a mudança.

O primeiro exemplo mostra como mover uma conta de automatização para um novo grupo de recursos.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Depois de executar o exemplo de código acima, lhe for pedido para confirmar que pretende efetuar esta ação. Assim que clicar em **Sim** e permitir que o script continuar, não receberá quaisquer notificações enquanto está a efetuar a migração. 

Para mover para uma nova subscrição, incluir um valor para o *DestinationSubscriptionId* parâmetro.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Tal como acontece com o exemplo anterior, lhe for pedido para confirmar a mudança. 

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre como mover recursos para o novo grupo de recursos ou subscrição, consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md)
* Para obter mais informações sobre o controlo de acesso baseado em funções na automatização do Azure, consulte [controlo de acesso baseado em funções na automatização do Azure](automation-role-based-access-control.md).
* Para mais informações sobre cmdlets do PowerShell para gerir a sua subscrição, consulte o artigo [utilizar o Azure PowerShell com o Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Para saber mais sobre as funcionalidades de portais para gerir a sua subscrição, consulte [no portal do Azure para gerir os recursos](../azure-resource-manager/resource-group-portal.md).
