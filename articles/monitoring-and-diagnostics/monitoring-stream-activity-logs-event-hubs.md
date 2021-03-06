---
title: O registo de atividade do Azure para os Hubs de eventos de fluxo | Microsoft Docs
description: Saiba como transmitir o registo de atividade para os Event Hubs do Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 8a599558fc35ca2bf48ce2a5f11ec4978bf10277
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Fluxo de registo de atividade do Azure para os Event Hubs
Pode transmitir o [registo de atividade do Azure](monitoring-overview-activity-logs.md) em tempo real para qualquer aplicação ao:

* Utilizar incorporada **exportar** opção no portal
* Ativar o ID de regra de Service Bus do Azure num perfil de registo através de cmdlets do Azure PowerShell ou a CLI do Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que pode fazer com o registo de atividade e Event Hubs
Seguem-se que poderá utilizar a capacidade de transmissão em fluxo para o registo de atividade de duas formas:

* **Fluxo para sistemas de registo e telemetria de terceiros**: ao longo do tempo, transmissão em fluxo de Event Hubs do Azure irá tornar-se o mecanismo para encaminhar o registo de atividade para SIEMs de terceiros e soluções de análise de registo.
* **Criar uma plataforma de registo e telemetria personalizada**: Se já tiver uma plataforma de telemetria personalizada ou estiver a pensar sobre como criar um, altamente dimensionável de publicação-subscrição natureza dos Event Hubs permite-lhe de forma flexível ingerir o registo de atividade. Para obter mais informações, consulte [vídeo de Dan Rosanova sobre como utilizar os Hubs de eventos numa plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Ative a transmissão em fluxo de registo de atividade
Pode ativar a transmissão em fluxo de registo de atividade através de programação ou através do portal. Qualquer forma, escolha um espaço de nomes de Event Hubs e uma política de acesso partilhado para esse espaço de nomes. Um hub de eventos com o nome insights-registos-operationallogs é criado nesse espaço de nomes quando ocorre o primeiro evento de registo de atividade de novo. 

Se não tiver um espaço de nomes de Event Hubs, terá primeiro de criar um. Se anteriormente transmissão em eventos de registo de atividade para este espaço de nomes de Event Hubs, será reutilizado o hub de eventos foi criado anteriormente. 

A política de acesso partilhado define as permissões que tenha o mecanismo de transmissão em fluxo. Hoje em dia, necessita de transmissão em fluxo para os Event Hubs **gerir**, **enviar**, e **escutar** permissões. Pode criar ou modificar as políticas de acesso partilhado para o espaço de nomes de Event Hubs no portal do Azure, sob o **configurar** separador para o espaço de nomes de Event Hubs. 

Para atualizar o perfil de registo de registo de atividade para incluir a transmissão em fluxo, o utilizador que está a efetuar a alteração tem de ter a permissão de ListKey na regra de autorização que os Event Hubs. O espaço de nomes de Event Hubs não tem de estar na mesma subscrição que a subscrição que está a emitir os registos, desde que o utilizador que configura a definição tenha acesso RBAC adequado para ambas as subscrições.

### <a name="via-the-azure-portal"></a>Através do portal do Azure
1. Navegue para o **registo de atividade** secção utilizando o **todos os serviços** pesquisa no lado esquerdo do portal.
   
   ![Selecionar o registo de atividade da lista de serviços no portal](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Selecione o **exportar** botão na parte superior do registo.
   
   ![Botão de exportação no portal](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Tenha em atenção que as definições de filtro que tinha aplicada ao visualizar o registo de atividade na vista anterior não têm nenhum impacto nas suas definições de exportação. Trata-se apenas para efeitos de filtragem veem ao navegar pelo registo de atividade no portal.
3. Na secção que aparece, selecione **todas as regiões**. Não selecione as regiões específicas.
   
   ![Secção de exportação](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Se selecionar algo diferente de **todas as regiões**, que irá perder a chaves eventos que pretende receber. O registo de atividade é um registo (não regionais) global, para que a maior parte dos eventos não dispõe de uma região associada aos mesmos. 
   >

4. Selecione **guardar** para guardar estas definições. As definições são imediatamente aplicadas à sua subscrição.
5. Se tiver várias subscrições, repita esta ação e enviar todos os dados para o mesmo hub de eventos.

### <a name="via-powershell-cmdlets"></a>Através de cmdlets do PowerShell
Se já existir um perfil de registo, terá primeiro de remover o perfil de registo existente e, em seguida, crie um novo perfil de registo.

1. Utilize `Get-AzureRmLogProfile` para identificar se existir um perfil de registo.  Se existir um perfil de registo, localize o *nome* propriedade.
2. Utilize `Remove-AzureRmLogProfile` para remover o perfil de registo com o valor da *nome* propriedade.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Utilize `Add-AzureRmLogProfile` para criar um novo perfil de registo:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespaceName/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Através da CLI do Azure
Se já existir um perfil de registo, terá primeiro de remover o perfil de registo existente e, em seguida, crie um novo perfil de registo.

1. Utilize `az monitor log-profiles list` para identificar se existir um perfil de registo.
2. Utilize `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de registo com o valor da *nome* propriedade.
3. Utilize `az monitor log-profiles create` para criar um novo perfil de registo:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Consumir os dados de registo dos Event Hubs
O esquema para o registo de atividade está disponível no [monitorizar atividade de subscrição com o registo de atividade do Azure](monitoring-overview-activity-logs.md). Cada evento está a ser uma matriz de blobs JSON chamado *registos*.

## <a name="next-steps"></a>Passos Seguintes
* [O registo de atividade para uma conta de armazenamento de arquivo](monitoring-archive-activity-log.md)
* [Leia a descrição geral do registo de atividade do Azure](monitoring-overview-activity-logs.md)
* [Configurar um alerta com base em eventos de um registo de atividade](insights-auditlog-to-webhook-email.md)

