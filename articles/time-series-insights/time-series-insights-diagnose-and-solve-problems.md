---
title: Diagnosticar e resolver problemas nas informações de séries de tempo do Azure | Microsoft Docs
description: Este artigo descreve como diagnosticar, resolver problemas e resolver problemas comuns que poderá encontrar no seu ambiente de informações de séries de tempo do Azure.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: f0c1b8aa99e9ac9c73f57af17490dd3a465a9cac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no seu ambiente de informações de séries de tempo

## <a name="problem-1-no-data-is-shown"></a>Problema 1: Não existem dados são apresentados
Existem várias razões comuns por que motivo não poderá ver os dados no [Explorador de Insights de séries de tempo do Azure](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Dados de origem do evento de r: causa não estão no formato JSON
As informações de séries de tempo do Azure suporta apenas os dados JSON. Para exemplos JSON, consulte [formas de JSON suportado](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Chave de origem do evento b: causa está em falta uma permissão necessária
* Para um IoT Hub, tem de fornecer a chave que tenha **serviço ligar** permissão.

   ![Permissão de ligação de serviço IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **iothubowner** e **serviço** funcionarão, porque têm **serviço ligar** permissão.
   
* Para um hub de eventos, terá de fornecer a chave que tenha **escutar** permissão.

   ![Permissão de escuta de hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **ler** e **gerir** funcionarão, porque têm **escutar** permissão.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Possível fazer com que o grupo de consumidores fornecido não é exclusiva Insights de séries de tempo de c:
Durante o registo de am IoT Hub ou hub de eventos, especifique o grupo de consumidores que deve ser utilizado para ler os dados. Este grupo de consumidores tem **não** ser partilhado. Se o grupo de consumidores for partilhado, o hub de eventos subjacente automaticamente desliga um dos leitores aleatoriamente. Forneça um grupo de consumidores exclusivo das informações de série de tempo ler a partir do.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problema de 2: Alguns dados são apresentados, mas algumas está em falta
Quando podem ver dados parcialmente, mas os dados é lagging, existem várias possibilidades a ter em consideração:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Uma causa possível r: o seu ambiente é obter limitada
Este é um problema comum quando são aprovisionados ambientes após a criação de uma origem de evento com dados.  Os Hubs IoT do Azure e de Hubs de eventos armazenam dados de cópia de segurança para sete dias.  TSI sempre será iniciada a provenientes do evento de mais antigo (FIFO), da origem do evento.  Por isso, se tiver cinco milhões de eventos numa origem de evento, quando liga a um S1, ambiente de TSI única unidade, TSI irá ler aproximadamente um milhão de eventos por dia.  Isto poderá ser apresentado ver como se se tratasse TSI está com tempos limite de latência de cinco dias à primeira vista.  O que acontece, na verdade, é que o ambiente está a ser limitado.  Se tiver a eventos antigos na sua origem de evento, pode abordam uma de duas formas:

- Limites de retenção da sua origem de evento para ajudar a eliminar eventos antigos que não pretende que apareçam na TSI de alteração
- Aprovisione um tamanho maior de ambiente (em termos de número de unidades de) para aumentar o débito de eventos antigos.  O ambiente utilizando o exemplo acima, se aumentado nesse mesmo ambiente S1 às cinco unidades durante um dia, deve ser catch-up para agora dentro do dia.  Se a produção de eventos de estado de repouso 1M ou menos eventos diário, pode reduzir a capacidade do evento de volta para baixo para uma unidade após detetou-se de.  

O limite de limitação é imposto com base no tipo SKU do ambiente e a capacidade. Todas as origens de eventos no ambiente de partilham esta capacidade. Se a origem do evento para o seu IoT Hub ou hub de eventos está a enviar dados que ultrapassem os limites impostos, verá limitação e um atraso.

O diagrama seguinte mostra um ambiente de informações de séries de tempo que tem um SKU de S1 e uma capacidade de 3. Pode eventos de 3 milhões de entrada por dia.

![Capacidade atual da SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Por exemplo, suponha que este ambiente está ingestão relacionadas mensagens de um hub de eventos. Observe a frequência de entrada mostrada no diagrama seguinte:

![Taxa de entrada de exemplo para um hub de eventos](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Como é mostrado no diagrama, a taxa diária de entrada é ~ 67,000 mensagens. Esta taxa traduz-se aproximadamente 46 mensagens cada minuto. Se cada mensagem de hub de eventos é simplificada para um único evento Insights de séries de tempo, este ambiente não verá nenhuma limitação. Se cada mensagem de hub de eventos é simplificada para 100 eventos Insights de séries de tempo, em seguida, 4,600 eventos devem ser ingeridos cada minuto. Ambiente S1 SKU que tem uma capacidade de 3 pode eventos de entrada apenas 2,100 cada minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2,100 eventos por minuto). Por conseguinte, verá um desfasamento devido à limitação. 

Para uma compreensão como funciona o flattening lógica, consulte [formas de JSON suportado](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Passos de resolução recomendada para a limitação excessiva
Para corrigir o desfasamento, aumenta a capacidade SKU do seu ambiente. Para obter mais informações, consulte [como dimensionar o ambiente de tempo série Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Uma causa possível ingestão b: inicial de dados históricos está a causar entrada lenta
Se estiver a ligar uma origem de evento existente, é provável que o seu hub IoT Hub ou evento já tiver dados no mesmo. O ambiente inicia a extração de dados a partir do início do período de retenção de mensagens da origem de evento.

Este comportamento é o comportamento predefinido e não pode ser substituído. Pode interagir com limitação, e pode demorar algum tempo para acompanhar no ingestão de dados históricos.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Passos de resolução recomendada de ingestão inicial grande
Para corrigir o desfasamento, siga os passos seguintes:
1. Aumenta a capacidade SKU para o valor máximo permitido (10 neste caso). Depois de aumenta a capacidade, o processo de entrada é iniciado obtendo segurança muito mais rapidamente. Pode visualizar a rapidez, obtendo cópias de segurança, o gráfico de disponibilidade no [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com). São-lhe cobrados para a capacidade de aumento.
2. Depois do desfasamento é atualizado, diminua a capacidade SKU de volta a taxa de entrada normal.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema 3: Meu origem do evento *nome da propriedade timestamp* definição não funciona
Certifique-se de que o nome e valor em conformidade com as seguintes regras:
* É o nome da propriedade timestamp _maiúsculas e minúsculas_.
* O valor da propriedade timestamp que provêm da sua origem de evento, como uma cadeia JSON, deve ter o formato _aaaa-MM-ddTHH. FFFFFFFK_. Um exemplo de uma cadeia de tal é "2008-04-12T12:53Z".

A forma mais fácil de garantir que o *nome da propriedade timestamp* são capturadas e a funcionar corretamente é a utilização do explorer TSI.  No Explorador de TSI, utilizando o gráfico, selecione um período de tempo depois de que forneceu o *nome da propriedade timestamp*.  Clique com o botão direito a seleção e escolha o *explorar eventos* opção.  O primeiro cabeçalho de coluna deve ser o *nome da propriedade timestamp* e deve ter um *($ts)* junto a palavra *Timestamp*, vez:
- *(abc)* , que deverá indicar TSI está a ler os valores de dados como cadeias
- *Ícone de calendário*, que deverá indicar TSI está a ler o valor de dados como *datetime*
- *#*, que deverá indicar TSI está a ler os valores de dados como um número inteiro


## <a name="next-steps"></a>Passos Seguintes
- Para obter ajuda adicional, iniciar uma conversação a [fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Também pode utilizar [suporte do Azure](https://azure.microsoft.com/support/options/) para as opções de suporte assistido.
