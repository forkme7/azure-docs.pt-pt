---
title: Resolver problemas de recetores do hub de eventos no Azure Stream Analytics
description: Consultar as melhores práticas para a consideração dos grupos de consumidores de Event Hubs nas tarefas do Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 20614986fc6c6afa9a92d163bf973a148e0517c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Depurar o Azure Stream Analytics com recetores do hub de eventos

Pode utilizar Event Hubs do Azure no Azure Stream Analytics para inserção ou dados de uma tarefa de saída. Uma melhor prática para utilizar Event Hubs é utilizar vários grupos de consumidores, para garantir a escalabilidade de tarefa. Uma razão é que o número de leitores na tarefa de Stream Analytics para uma introdução específica afetará o número de leitores num grupo de consumidores único. O número exatos de recetores baseia-se nos detalhes de implementação interno para a lógica da topologia de escalamento horizontal. O número de recetores não está exposto externamente. O número de leitores pode alterar a hora de início da tarefa ou durante as atualizações de tarefa.

> [!NOTE]
> Quando o número de leitores for alterada durante uma atualização de tarefa, avisos transitórios são escritos para registos de auditoria. Tarefas do Stream Analytics recuperar automaticamente a partir destes problemas transitórios.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Número de leitores por partição excede o limite de Event Hubs cinco

Cenários em que o número de leitores por partição excede o limite de Event Hubs cinco incluem o seguinte:

* Múltiplas instruções SELECIONADAS: Se utilizar várias instruções que se referem à **mesmo** hub de eventos de entrada, cada instrução SELECIONADA faz com que um recetor novo a ser criado.
* UNION: Ao utilizar uma União, é possível ter várias entradas que se referem para o **mesmo** grupo hub e consumidores de eventos.
* ASSOCIAÇÃO automática: Ao utilizar uma operação de associação automática, é possível consultar o **mesmo** hub de eventos várias vezes.

## <a name="solution"></a>Solução

As seguintes melhores práticas podem ajudar a mitigar cenários em que o número de leitores por partição excede o limite de Event Hubs cinco.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dividir a sua consulta em vários passos, utilizando uma cláusula WITH

A cláusula WITH Especifica um conjunto de resultados com nome temporária que pode ser referenciado por uma cláusula FROM na consulta. É possível definir a cláusula WITH no âmbito da execução de uma única instrução SELECT.

Por exemplo, em vez desta consulta:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilize esta consulta:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Certifique-se de que as entradas vincular a grupos de consumidores diferentes

Para consultas no qual os três ou mais entradas estão ligadas ao mesmo grupo de consumidores de Event Hubs, crie grupos de consumidores separado. Isto requer a criação de entradas de Stream Analytics adicionais.


## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx)
