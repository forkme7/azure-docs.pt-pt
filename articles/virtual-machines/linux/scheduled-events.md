---
title: Agendadas eventos para VMs com Linux no Azure | Microsoft Docs
description: Agendar eventos utilizando o serviço de metadados do Azure para as máquinas virtuais do Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: db4a0d1f288394276cd400e7a060cfb3662b34f0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Serviço de metadados do Azure: Eventos agendados para VMs com Linux

Eventos agendados é um serviço de metadados do Azure que indica a hora da aplicação para se preparar para manutenção de máquina virtual (VM). Fornece informações sobre eventos de manutenções (por exemplo, reiniciar o computador), para que a aplicação pode preparar para os mesmos e limitar interrupção. Está disponível para todos os tipos de máquinas virtuais do Azure, incluindo PaaS e IaaS no Windows e Linux. 

Para obter informações sobre eventos agendadas no Windows, consulte [agendada eventos para VMs do Windows](../windows/scheduled-events.md).

> [!Note] 
> Eventos agendados está normalmente disponível em todas as regiões do Azure. Consulte [versão e disponibilidade de região](#version-and-region-availability) para obter informações de versão mais recentes.

## <a name="why-use-scheduled-events"></a>Porquê utilizar agendada eventos?

Muitas aplicações podem beneficiar da hora para se preparar para manutenção VM. O tempo pode ser utilizado para efetuar tarefas específicas da aplicação que melhorar a disponibilidade, a fiabilidade e a possibilidade de assistência, incluindo: 

- Ponto de verificação e de restauro.
- Drenagem de ligação.
- Ativação pós-falha de réplica primária.
- Remoção de um conjunto de Balanceador de carga.
- Registo de eventos.
- Encerramento correto.

Com eventos agendada, a aplicação pode detetar quando irá ocorrer e de manutenção acione tarefas para limitar o impacto.  

Eventos agendados fornece eventos nos seguintes casos de utilização:

- Manutenção iniciadas por plataforma (por exemplo, uma atualização do sistema operativo do anfitrião)
- Manutenção iniciada pelo utilizador (por exemplo, um utilizador é reiniciado ou redeploys uma VM)

## <a name="the-basics"></a>As noções básicas  

  Serviço de metadados expõe informações sobre como executar as VMs através da utilização de um ponto final de REST está acessível a partir da VM. As informações estão disponíveis através de um IP nonroutable para que não está exposta fora da VM.

### <a name="scope"></a>Âmbito
Agendada eventos são entregues para:

- Todas as VMs num serviço em nuvem.
- Todas as VMs num conjunto de disponibilidade.
- Todas as VMs de um grupo de colocação do conjunto de dimensionamento. 

Como resultado, verifique o `Resources` campo no evento para identificar as VMs que são afetadas.

### <a name="endpoint-discovery"></a>Deteção de ponto final
Para VNET ativada VMs, metadados de serviço está disponível a partir de um IP estático nonroutable, `169.254.169.254`. O ponto final completo para a versão mais recente dos eventos agendada é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Se a VM não está a ser criada dentro de uma rede Virtual, os casos predefinido para serviços em nuvem e as VMs clássicas, lógica adicional é necessário para detetar o endereço IP a utilizar. Para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), consulte este exemplo.

### <a name="version-and-region-availability"></a>Versão e disponibilidade de região
O serviço de eventos agendada está com a versão. As versões são obrigatórias; a versão atual é `2017-08-01`.

| Versão | Tipo de versão | Regiões | Notas de Versão | 
| - | - | - | - | 
| 2017-08-01 | Disponibilidade Geral | Todos | <li> Removido um caráter de sublinhado prepended os nomes de recursos para as VMs do Iaas<br><li>Requisito de cabeçalho de metadados imposto para todos os pedidos | 
| 2017-03-01 | Pré-visualização | Todos | <li>Versão inicial


> [!NOTE] 
> Pré-visualização as versões anteriores dos eventos agendados suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Ativar e desativar eventos agendados
Eventos agendados está ativada para o tempo de serviço a primeira efetuar um pedido de eventos. Deve esperar uma resposta atrasada na sua primeira chamada de dois minutos.

Eventos agendados está desativada para o seu serviço, se não faz um pedido para 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
Manutenção VM iniciada pelo utilizador através do portal do Azure, API, CLI ou PowerShell resulta num evento agendado. Em seguida, pode testar a lógica de preparação de manutenção na sua aplicação e a aplicação pode preparar para manutenção iniciada pelo utilizador.

Se reiniciar uma VM, um evento com o tipo `Reboot` está agendada. Se, volte a implementar uma VM, um evento com o tipo `Redeploy` está agendada.

## <a name="use-the-api"></a>Utilize a API

### <a name="headers"></a>Cabeçalhos
Quando consulta os metadados do serviço, tem de fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado inadvertidamente. O `Metadata:true` cabeçalho não é necessário para todos os pedidos de eventos agendada. Falha ao incluir o cabeçalho do pedido resulta numa resposta "Pedido incorreto" do serviço de metadados.

### <a name="query-for-events"></a>Consulta de eventos
Pode consultar eventos agendados ao efetuar a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Uma resposta contém uma matriz de eventos agendadas. Uma matriz vazia significa que atualmente não há eventos agendados.
No caso em que existem eventos agendados, a resposta contém uma matriz de eventos. 
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
| EventType | Impacto faz com que a este evento. <br><br> Valores: <br><ul><li> `Freeze`: A VM está agendada para colocar em pausa por alguns segundos. A CPU está suspenso, mas não existe nenhum efeito na memória, abrir ficheiros ou ligações de rede. <li>`Reboot`: A VM está agendada para reiniciar o computador. (Memória nonpersistent é perdida.) <li>`Redeploy`: A VM está agendada para mover para outro nó. (Discos efémeras serão perdidos.) |
| ResourceType | Tipo de recurso que afeta a este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que afeta a este evento. A lista é garantida que contém as máquinas no máximo uma [domínio de atualização](manage-availability.md), mas não poderá conter todas as máquinas de UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para iniciar após o período de tempo especificado no `NotBefore` propriedade.<li>`Started`: Este evento foi iniciado.</ul> Não `Completed` ou estado semelhante nunca lhe foi fornecido. O evento já não é devolvido quando o evento estiver concluído.
| NotBefore| Tempo após o qual este evento pode iniciar. <br><br> Exemplo: <br><ul><li> MON, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está agendado uma quantidade mínima de tempo no futuro, com base no tipo de evento. Este tempo é refletido num evento `NotBefore` propriedade. 

|EventType  | Tenha em atenção mínima |
| - | - |
| Fixar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |

### <a name="start-an-event"></a>Iniciar um evento 

Depois de mais de um evento futura e concluir a lógica de encerramento correto, pode aprovar o evento pendente efetuando uma `POST` a chamada para o serviço de metadados com `EventId`. Esta chamada indica ao Azure que pode a Encurte a notificação mínima de tempo (quando possível). 

O seguinte exemplo JSON é esperado o `POST` corpo do pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém `EventId` para o evento que pretende agilizar a:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemplo de bash
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Confirmar um evento permite que o evento continuar para todos os `Resources` nos eventos, não apenas a VM que reconhece o evento. Por conseguinte, pode optar por elecionar uma leader para coordenar a confirmação, que pode ser tão simple como primeira máquina no `Resources` campo.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento pendente:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Passos Seguintes 
- Veja [agendada eventos no Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Reveja os exemplos de código eventos agendada no [repositório do Github de eventos agendados Azure instância metadados](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Saiba mais sobre as APIs que estão disponíveis no [instância metadados serviço](instance-metadata-service.md).
- Saiba mais sobre [a manutenção para computadores virtuais Linux no Azure planeada](planned-maintenance.md).
