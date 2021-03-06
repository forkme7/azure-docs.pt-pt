---
title: Descrição geral do Estado de funcionamento do serviço | Microsoft Docs
description: Personalizado informações sobre como as aplicações do Azure são afetadas pelo problemas atuais e futuras serviço do Azure e manutenção.
services: Resource health
documentationcenter: ''
author: rboucher
manager: ''
editor: ''
ms.assetid: ''
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: robb
ms.openlocfilehash: f0efe96684d77fb83b69a4da12d312872da2f768
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2018
---
# <a name="service-health"></a>Service Health
Estado de funcionamento do serviço fornece um dashboard personalizável que controla o estado de funcionamento dos seus serviços do Azure nas regiões onde utilizá-los. Neste dashboard, pode controlar eventos Active Directory, como problemas de interrupção contínua do serviço, futuros maintence planeada ou advisories de estado de funcionamento relevantes. Quando eventos tornam-se Inativas, estes são colocadas no seu histórico de estado de funcionamento até 90 dias. Por fim, pode utilizar o dashboard de estado de funcionamento do serviço para criar e gerir alertas de estado de funcionamento do serviço que proativamente notificá-lo quando resolver problemas de serviço são que afetam a.

## <a name="service-health-events"></a>Eventos de estado de funcionamento do serviço
Estado de funcionamento do serviço controla os três tipos de eventos de estado de funcionamento que possam afetar os recursos:
1. **Problemas de serviço** -problemas de serviços do Azure que afetá-lo agora. 
2. **A manutenção planeada** -manutenções que podem afetar a disponibilidade dos seus serviços no futuro.  
3. **Advisories de estado de funcionamento** -alterações nos serviços do Azure que necessitam da sua atenção. Os exemplos incluem quando são preteridas a funcionalidades do Azure ou se for excedido uma quota de utilização.

## <a name="get-started-with-service-health"></a>Introdução ao serviço de estado de funcionamento
Para iniciar o dashboard de estado de funcionamento do serviço, selecione o mosaico estado de funcionamento do serviço no seu dashboard do portal. Se anteriormente tiver removido no mosaico ou estiver a utilizar o dashboard personalizado, procure por serviço de integridade do serviço de "Mais serviços" (inferior esquerda no seu dashboard).

![Introdução ao serviço de estado de funcionamento](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Ver problemas atuais que tem impacto nos seus serviços
O **Service problemas** vista mostra a quaisquer problemas em curso nos serviços do Azure que estão a afetar os recursos. Pode compreender quando iniciou o problema e os serviços e regiões são afetadas. Também pode ler a atualização mais recente para compreender que Azure está a fazer para resolver o problema. 

![Gerir o problema do serviço](./media/service-health-overview/azure-service-health-overview-2.png)

Escolha o **potencial impacto** separador para ver a lista de recursos que possui que poderão ser afetados pelo problema específica. Pode transferir uma lista CSV destes recursos para partilhar com a sua equipa.

![Gerir o problema do serviço - impacto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obter ligações e explicações transferíveis 
Pode obter uma ligação para o problema utilizar no seu sistema de gestão do problema. Pode transferir o PDF e, por vezes, ficheiros CSV a partilha com pessoas que não tem acesso ao portal do Azure.   

![Gerir o problema do serviço - Gestão de problemas](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obter o suporte da Microsoft
Contacte o suporte se o recurso for deixado num Estado incorreto, mesmo depois do problema está resolvido.  Utilize as ligações de suporte no lado direito da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Afixar um mapa de estado de funcionamento personalizado ao seu dashboard
Filtre o serviço de estado de funcionamento para mostrar as subscrições empresariais críticos, regiões e tipos de recursos. Guarde o filtro e afixar um mapa de universo de estado de funcionamento personalizado ao seu dashboard do portal. 

![Mapa de estado de funcionamento personalizado de filtro](./media/service-health-overview/azure-service-health-overview-6a.png)

![PIN de um mapa de estado de funcionamento personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de estado de funcionamento do serviço
Estado de funcionamento do serviço integra-se com a monitorização do Azure para alertá-lo através de mensagens de correio eletrónico, mensagens de texto e webhook notificações quando os recursos empresariais críticos são afetados. Configure um alerta de registo de atividade para o evento de estado de funcionamento de serviço apropriado. Encaminhar esse alerta às pessoas apropriadas na sua organização utilizando grupos de ação. Para obter mais informações, consulte [configurar alertas de estado de funcionamento de serviço](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

# <a name="next-steps"></a>Próximos Passos
Configure alertas para que seja notificado dos problemas de estado de funcionamento. Para obter mais informações, consulte [configurar alertas de estado de funcionamento do serviço](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
