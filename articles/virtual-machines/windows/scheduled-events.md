---
title: Agendado eventos para VMs do Windows no Azure | Microsoft Docs
description: Agendar eventos utilizando o serviço de metadados do Azure para nas suas máquinas virtuais do Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 63318b78607802d7d70d65a186a396cbc655c40b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de metadados do Azure: Eventos agendados para VMs do Windows

Eventos agendados é um serviço de metadados do Azure que indica a hora da aplicação para se preparar para manutenção de máquina virtual. Fornece informações sobre eventos de manutenções (por exemplo, reiniciar o computador) para a aplicação possa preparar para os mesmos e limitar interrupção. Está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS no Windows e Linux. 

Para obter informações sobre eventos agendada no Linux, consulte [eventos agendados para VMs com Linux](../linux/scheduled-events.md).

> [!Note] 
> Eventos agendados está normalmente disponível em todas as regiões do Azure. Consulte [versão e disponibilidade de região](#version-and-region-availability) para obter informações de versão mais recentes.

## <a name="why-scheduled-events"></a>Por que motivo agendada eventos?

Muitas aplicações podem beneficiar da hora para se preparar para manutenção de máquina virtual. O tempo pode ser utilizado para executar tarefas específicas de aplicações que melhorar a disponibilidade, a fiabilidade e a possibilidade de assistência, incluindo: 

- Ponto de verificação e restauro
- Drenagem de ligação
- Ativação pós-falha de réplica primária 
- Remoção do agrupamento de Balanceador de carga
- Registo de eventos
- Encerramento correto 

A utilização de eventos agendada a aplicação pode detetar quando irá ocorrer e de manutenção acione tarefas para limitar o impacto.  

Eventos agendados fornece eventos nos seguintes casos de utilização:
- Plataforma iniciou a manutenção (por exemplo, atualização de SO de anfitrião)
- Utilizador iniciou a manutenção (por exemplo, utilizador reinicia ou redeploys uma VM)

## <a name="the-basics"></a>As noções básicas  

Serviço de metadados do Azure expõe informações sobre como executar máquinas virtuais com um ponto final de REST acessível a partir da VM. As informações estão disponíveis através de um IP não encaminhável, para que não está exposta fora da VM.

### <a name="endpoint-discovery"></a>Deteção de ponto final
Para VNET ativada VMs, o serviço de metadados está disponível a partir de um IP estático não encaminhável, `169.254.169.254`. O ponto final completo para a versão mais recente dos eventos agendada é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Se a Máquina Virtual não está a ser criado dentro de uma rede Virtual, os casos predefinido para serviços em nuvem e as VMs clássicas, lógica adicional é necessário para detetar o endereço IP a utilizar. Consulte este exemplo para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Versão e disponibilidade de região
O serviço de eventos agendada está com a versão. As versões são obrigatórias e a versão atual é `2017-08-01`.

| Versão | Tipo de versão | Regiões | Notas de Versão | 
| - | - | - | - |
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Removido um caráter de sublinhado prepended os nomes de recursos para as VMs do Iaas<br><li>Requisito de cabeçalho de metadados imposto para todos os pedidos | 
| 2017-03-01 | Pré-visualização | Todos |<li>Versão inicial

> [!NOTE] 
> Pré-visualização as versões anteriores dos eventos agendadas suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e desativar eventos agendados
Eventos agendados está ativada para o tempo de serviço a primeira efetuar um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de dois minutos.

Eventos agendados está desativada para o seu serviço, se não faz um pedido para 24 horas.

### <a name="user-initiated-maintenance"></a>Utilizador iniciou a manutenção
Utilizador iniciou a manutenção de máquina virtual através do portal do Azure, API, CLI, ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção na sua aplicação e permite à aplicação para se preparar para manutenção iniciada pelo utilizador.

Reinício de uma máquina virtual agendas de um evento com o tipo `Reboot`. Voltar a implementar uma máquina virtual agendas de um evento com o tipo `Redeploy`.

## <a name="using-the-api"></a>Utilizando a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o serviço de metadados, tem de fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado inadvertidamente. O `Metadata:true` cabeçalho não é necessário para todos os pedidos de eventos agendada. Falha ao incluir o cabeçalho do pedido irá resultar numa resposta pedido incorreto do serviço de metadados.

### <a name="query-for-events"></a>Consulta de eventos
Pode consultar eventos agendada, simplesmente ao efetuar a seguinte chamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma matriz de eventos agendadas. Uma matriz vazia significa que não existem atualmente não há eventos agendados.
No caso em que existem eventos agendados, a resposta contém uma matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador exclusivo global para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto faz com que a este evento. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está agendada para colocar em pausa para alguns segundos. A CPU está suspenso, mas não há nenhum impacto na memória, ficheiros abertos ou ligações de rede. <li>`Reboot`: A Máquina Virtual está agendada para reiniciar o computador (memória não persistentes é perdida). <li>`Redeploy`: A Máquina Virtual está agendado para mover para outro nó (discos efémeras perdem). |
| ResourceType | Tipo de recurso que afeta este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que tem impacto este evento. Isto é garantido que contém as máquinas no máximo uma [domínio de atualização](manage-availability.md), mas não pode conter todas as máquinas de UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado do evento | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para iniciar após o período de tempo especificado no `NotBefore` propriedade.<li>`Started`: Este evento foi iniciado.</ul> Não `Completed` ou estado semelhante nunca é fornecido; o evento já não será devolvido quando o evento estiver concluído.
| NotBefore| Tempo após o qual este evento pode iniciar. <br><br> Exemplo: <br><ul><li> MON, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está agendado uma quantidade mínima de tempo no futuro, com base no tipo de evento. Este tempo é refletido num evento `NotBefore` propriedade. 

|EventType  | Tenha em atenção mínima |
| - | - |
| Fixar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |

### <a name="event-scope"></a>Âmbito de eventos     
Agendada eventos são entregues para:        
 - Todas as máquinas virtuais num serviço em nuvem      
 - Todas as máquinas virtuais num conjunto de disponibilidade      
 - Todas as máquinas virtuais de um grupo de colocação do conjunto de dimensionamento.         

Como resultado, deve verificar o `Resources` campo no evento para identificar as VMs que vão ser afetado. 

### <a name="starting-an-event"></a>A partir de um evento 

Depois de ter aprendidas de um evento futura e concluir a lógica de encerramento correto, pode aprovar o evento pendente efetuando uma `POST` a chamada para o serviço de metadados com o `EventId`. Isto indica ao Azure que pode a Encurte a notificação mínima de tempo (quando possível). 

Segue-se o json esperado o `POST` corpo do pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que pretende agilizar a:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Confirmar um evento permite que o evento continuar para todos os `Resources` nos eventos, não apenas a máquina virtual que reconhece o evento. Por conseguinte, pode optar por elecionar uma leader para coordenar a confirmação, que pode ser tão simple como primeira máquina no `Resources` campo.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Passos Seguintes 

- Veja uma [agendada eventos demonstração](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) no Azure sexta-feira. 
- Reveja os exemplos de código eventos agendada no [Azure instância metadados agendada eventos repositório do Github](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Saiba mais sobre as APIs disponíveis no [metadados de instância de serviço](instance-metadata-service.md).
- Saiba mais sobre [planeada manutenção de máquinas virtuais do Windows no Azure](planned-maintenance.md).
