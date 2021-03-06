---
title: Integrar os registos de recursos do Azure com os sistemas SIEM | Microsoft Docs
description: Saiba mais sobre a integração de registo do Azure, as suas capacidades principais e como funciona.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Introdução à integração de registos do Azure

Pode utilizar a integração de registo do Azure para integrar os registos não processados a partir dos seus recursos do Azure com os sistemas Security Information and Event Management (SIEM) no local. Utilizar a integração de registo do Azure apenas se um conector para [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) não está disponível a partir do seu fornecedor SIEM.

É o método preferencial de integração de registos do Azure utilizando o conector de Monitor do Azure do seu fornecedor SIEM. Para utilizar o conector, siga as instruções em [fluxo Monitor monitorização para os dados dos event hubs](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). 

No entanto, se o fornecedor do SIEM não fornecer um conector para monitorizar o Azure, poderá utilizar a integração de registo do Azure como uma solução temporária até um conector está disponível. Integração de registo do Azure é uma opção apenas se a integração de registo do Azure suporta o SIEM.

> [!IMPORTANT]
> Se o seu interesse primário é recolher registos de máquina virtual, a maioria dos fornecedores SIEM incluir esta opção na sua solução. Utilizar o SIEM conector do fornecedor é sempre a alternativa preferencial.

Integração de registo do Azure recolhe eventos do Windows a partir de registos do Visualizador de eventos do Windows, [registos de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [alertas do Centro de segurança do Azure](../security-center/security-center-intro.md), e [os registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) do Recursos do Azure. Integração ajuda a sua solução SIEM fornecer um dashboard unificado para todos os elementos, se no local ou na nuvem. Pode utilizar um dashboard para receber, Agregar, correlacionar e analisar os alertas de eventos de segurança.

> [!NOTE]
> Atualmente, a integração de registo do Azure suporta apenas Azure comercial e nuvens de Azure Government. Não são suportadas outras nuvens.

![Diagrama do processo de integração de registo do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Os registos que pode a integrar

Azure produz um vasto conjunto registo para cada serviço do Azure. Os registos representam três tipos de registo:

* **Registos de controlo/gestão**: proporcionam visibilidade a [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) operações criar, ATUALIZAR e eliminar. Um registo de atividade do Azure é um exemplo deste tipo de registo.
* **Dados plane registos**: dar visibilidade sobre eventos que são gerados quando utiliza um recurso do Azure. Um exemplo deste tipo de registo é o Visualizador de eventos de Windows **sistema**, **segurança**, e **aplicação** canais na máquina virtual do Windows. Outro exemplo é diagnósticos do Azure que configura através do Azure Monitor de registo.
* **Processar eventos**: fornecer eventos analisados e informações de alertas que são processados por si. Um exemplo deste tipo de evento é alertas do Centro de segurança do Azure. Centro de segurança do Azure processa e analisa a sua subscrição para fornecer alertas que são relevantes para a sua postura de segurança atual.

Integração de registo do Azure suporta ArcSight, QRadar e Splunk. Consulte o fornecedor do SIEM para avaliar se o fornecedor tem um conector nativo. Não utilize a integração de registo do Azure esteja disponível um conector nativo.

Se não existem outras opções estão disponíveis, considere utilizar a integração de registo do Azure. A tabela seguinte inclui a nossa recomendações:

|SIEM | Cliente já utiliza o integrador de registos do Azure | Cliente está a investigar as opções de integração do SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Começar a migrar para o [suplemento de Monitor do Azure para Splunk](https://splunkbase.splunk.com/app/3534/). | Utilize o [Splunk conector](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrar para ou a começar a utilizar o conector de QRadar documentado na última secção [Azure de fluxo de dados para um hub de eventos para o consumo de monitorização por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Utilizar o conector de QRadar documentado na última secção [Azure de fluxo de dados para um hub de eventos para o consumo de monitorização por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continue a utilizar o integrador de registos do Azure, até um conector está disponível. Em seguida, migre para a solução com base no conector.  | Considere a utilização de Log Analytics do Azure como alternativa. Não carregar para a integração de registo do Azure, a menos que está disposto a seguir o processo de migração quando o conector fica disponível. |

> [!NOTE]
> Embora a integração de registo do Azure é uma solução livre, existem custos de armazenamento do Azure associadas ao armazenamento de informações de ficheiro de registo.

Se necessitar de assistência, pode criar um [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **integração de registo**.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresenta-lhe a integração de registo do Azure. Para saber mais sobre a integração de registo do Azure e os tipos de registos que são suportados, consulte os artigos seguintes:

* [Introdução ao Azure registo integração](security-azure-log-integration-get-started.md). Este tutorial orienta-o através da instalação de integração de registo do Azure. Também descreve como integrar os registos a partir do armazenamento do Windows Azure Diagnostics (WAD), os registos de atividade do Azure, alertas do Centro de segurança do Azure e os registos de auditoria do Azure Active Directory.
* [Integração de registo do Azure perguntas mais frequentes (FAQ)](security-azure-log-integration-faq.md). Estas FAQ responde a questões recorrentes sobre a integração de registo do Azure.
* Saiba mais sobre como [transmitir dados para um hub de eventos para consumo por uma ferramenta externa de monitorização do Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
