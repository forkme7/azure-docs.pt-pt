---
title: As métricas do Storage do Azure no Monitor do Azure | Microsoft Docs
description: Saiba mais sobre a nova métrica oferecida do Monitor do Azure.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 3c4c9a330286eb00aa6a0da6e95b56b49b6eaa6c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Métricas do Armazenamento do Azure no Azure Monitor

Com a métrica no armazenamento do Azure, pode analisar tendências de utilização, pedidos de rastreio e diagnosticar problemas com a sua conta de armazenamento.

Monitor do Azure fornece interfaces de utilizador unificadas monitorização através de serviços do Azure diferentes. Para obter mais informações, consulte [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Armazenamento do Azure integra Monitor do Azure através do envio de dados métricos da plataforma de Monitor do Azure.

## <a name="access-metrics"></a>Métricas de acesso

Monitor do Azure fornece várias formas de métricas de acesso. Pode aceder às mesmas a partir de [portal do Azure](https://portal.azure.com), as APIs de Monitor do Azure (REST e .net) e soluções de análise, tais como a operação Management Suite e Hubs de eventos. Para obter mais informações, consulte [métricas de Monitor de Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As métricas são ativadas por predefinição e pode aceder aos últimos 30 dias de dados. Se precisar de manter os dados para um período de tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [definições de diagnóstico](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Monitor do Azure.

### <a name="access-metrics-in-the-azure-portal"></a>Métricas de acesso no portal do Azure

Pode monitorizar as métricas ao longo do tempo no portal do Azure. O exemplo seguinte mostra como ver **UsedCapacity** ao nível da conta.

![captura de ecrã de aceder a métricas no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Com base nas métricas que suportam dimensões, tem de filtro com o valor de dimensão pretendido. O exemplo seguinte mostra como ver **transações** ao nível da conta com **êxito** tipo de resposta.

![captura de ecrã de aceder a métrica com dimensão no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Métricas de acesso com a API REST

Monitor do Azure fornece [REST APIs](/rest/api/monitor/) ler a definição de métrica e valores. Esta secção mostra como ler as métricas de armazenamento. ID de recurso é utilizado em todas as APIS REST. Para obter mais informações, leia [compreender o ID de recurso dos serviços de no armazenamento](#understanding-resource-id-for-services-in-storage).

O exemplo seguinte mostra como utilizar [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comandos para simplificar a testar com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista os definição de métrica nível conta com a API REST

O exemplo seguinte mostra como listar métrica definição ao nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Se pretende listar as definições de métricas de blob, tabela, ficheiro ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

A resposta contém a definição de métrica no formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Ler valores de métricos de nível de conta com a API REST

O exemplo seguinte mostra como ler os dados métricos ao nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

Acima exemplo, se pretender ler os valores de métricos de blob, tabela, ficheiro ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

A seguinte resposta contém valores métricos no formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Métricas de acesso com o SDK do .net

Monitor do Azure fornece [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) ler a definição de métrica e valores. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Tem de utilizar `0.18.0-preview` ou versão posterior para as métricas do storage. ID de recurso é utilizado no .net SDK. Para obter mais informações, leia [compreender o ID de recurso dos serviços de no armazenamento](#understanding-resource-id-for-services-in-storage).

O exemplo seguinte mostra como utilizar o Azure Monitor .net SDK para ler as métricas do storage.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista os definição de métrica nível conta com o SDK do .net

O exemplo seguinte mostra como listar métrica definição ao nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Se pretende listar as definições de métricas de blob, tabela, ficheiro ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Leia os valores de métricos com o SDK do .net

O exemplo seguinte mostra como ler `UsedCapacity` dados ao nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Acima exemplo, se pretender ler os valores de métricos de blob, tabela, ficheiro ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Leia os valores de métricos multidimensionais com o SDK do .net

Multidimensionais com base nas métricas, tem de definir o filtro de dados de metadados se pretender ler os dados métricos no valor de dimensão específica.

O exemplo seguinte mostra como ler os dados métricos na métrica dimensão multi de suporte:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Noções sobre o ID de recurso para serviços no Storage do Azure

ID do recurso é um identificador exclusivo de um recurso no Azure. Quando utiliza a API de REST de Monitor do Azure para ler valores ou definições de métricas, tem de utilizar o ID de recurso para o recurso no qual pretende operar. O modelo de ID de recurso segue este formato:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Armazenamento fornece métricas de nível de conta de armazenamento e o nível de serviço com a monitorização do Azure. Por exemplo, pode obter métricas para apenas armazenamento de Blobs. Cada nível tem a suas próprias ID de recurso, o que é utilizado para obter as métricas de apenas nesse nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recurso para uma conta de armazenamento

O seguinte mostra o formato para especificar o ID de recurso para uma conta de armazenamento.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID de recurso para os serviços de armazenamento

O seguinte mostra o formato para especificar o ID de recurso para cada um dos serviços de armazenamento.

* ID de recurso de serviço blob `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID de recurso de serviço tabela `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID de recurso de serviço fila `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID de recurso de serviço do ficheiro `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de recurso no Monitor de Azure REST API

O seguinte mostra o padrão utilizado ao chamar a API de REST de Monitor do Azure.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Métricas de capacidade
Valores de métricas de capacidade são enviados para o Monitor de Azure a cada hora. Os valores são atualizados diariamente. O intervalo de tempo define o intervalo de tempo para o qual os valores da métrica são apresentados. O intervalo de tempo suportado para todas as métricas de capacidade é uma hora (PT1H).

Armazenamento do Azure fornece as métricas de capacidade seguinte no Monitor do Azure.

### <a name="account-level"></a>Nível de conta

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento utilizado pela conta de armazenamento. Para contas de armazenamento standard, é a soma de capacidade utilizada pela blob, tabela, ficheiros e fila. Para contas de armazenamento premium e contas do Blob storage, é o mesmo que BlobCapacity. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blobs

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total do armazenamento de BLOBs numa conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos de BLOBs armazenados na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([definição](#metrics-dimensions)) |
| ContainerCount    | O número de contentores na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Table Storage

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de tabela utilizado pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades de tabela na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de filas utilizado pela conta do storage. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número de mensagens de filas não expiradas na conta de armazenamento. <br/><br/>Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de ficheiros

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de ficheiros utilizado pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| FileCount   | O número de ficheiros na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de ficheiros partilha na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: médio <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

Métricas de transação são enviadas do armazenamento do Azure para monitorizar o Azure cada minuto. Todas as métricas de transação estão disponíveis no nível de conta e o serviço (armazenamento de BLOBs, armazenamento de tabelas, ficheiros do Azure e o armazenamento de filas). O intervalo de tempo define o intervalo de tempo que os valores de métricos são apresentados. Os grains hora suportados para todas as métricas de transação são PT1H e PT1M.

Storage do Azure fornece as métricas de transação seguintes no Monitor do Azure.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: milissegundos <br/> Tipo de agregação: médio <br/> Dimensões aplicáveis: GeoType, ApiName ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. <br/><br/> Unidade: milissegundos <br/> Tipo de agregação: médio <br/> Dimensões aplicáveis: GeoType, ApiName ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. Disponibilidade é calculada ao colocar o valor total de pedidos faturáveis e dividindo-lo pelo número de pedidos aplicáveis, incluindo os pedidos que produzidos erros inesperados. Todos os erros inesperados resultam na disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada. <br/><br/> Unidade: por cento <br/> Tipo de agregação: médio <br/> Dimensões aplicáveis: GeoType, ApiName ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99,99 |

## <a name="metrics-dimensions"></a>Dimensões de métricas

Storage do Azure suporta os seguintes dimensões com base nas métricas no Monitor do Azure.

| Nome da Dimensão | Descrição |
| ------------------- | ----------------- |
| BlobType | O tipo de blob de métricas de Blob apenas. Os valores suportados são **BlockBlob** e **PageBlob**. Acrescentar Blob está incluído no BlockBlob. |
| ResponseType | Tipo de resposta de transação. Os valores disponíveis incluem: <br/><br/> <li>ServerOtherError: Todos os outros erros de lado do servidor, exceto aqueles descrito </li> <li> ServerBusyError: Pedido autenticado que devolveu um código de estado HTTP 503. </li> <li> ServerTimeoutError: Excedido pedido autenticado que devolveu um código de estado HTTP 500. O tempo limite foi excedido devido a um erro de servidor. </li> <li> AuthorizationError: Pedido autenticado que falhou devido a acesso não autorizado de dados ou uma falha de autorização. </li> <li> NetworkError: Pedido autenticado que falhou por erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes de expiração do tempo limite. </li> <li>    ClientThrottlingError: Erro de limitação do lado do cliente. </li> <li> ClientTimeoutError: Excedido pedido autenticado que devolveu um código de estado HTTP 500. Se o tempo limite de rede do cliente ou o limite de tempo do pedido é definido para um valor menor que o esperado pelo serviço de armazenamento, é um limite de tempo esperado. Caso contrário, é reportado como um ServerTimeoutError. </li> <li> ClientOtherError: Todos os outros erros de lado do cliente, exceto aqueles descrito. </li> <li> Com êxito: Pedido com êxito|
| GeoType | Transação de cluster principal ou secundário. Os valores disponíveis incluem principais e secundários. Aplica-se para acesso de leitura Georreplicação redundante Storage(RA-GRS) ao ler objetos do inquilino secundário. |
| apiName | O nome da operação. Por exemplo: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Para todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Para as dimensões de suporte de métricas, tem de especificar o valor de dimensão para ver os valores de métricas correspondente. Por exemplo, se observar **transações** valor para respostas com êxito, tem de filtrar a **ResponseType** dimensão com **êxito**. Ou se observar **BlobCount** valor para BLOBs de blocos, tem de filtrar a **BlobType** dimensão com **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade do serviço de métricas legadas

Métricas de legado estão disponíveis em paralelo com a métrica de Monitor do Azure gerida. O suporte mantém o mesmo até que o armazenamento do Azure termina o serviço de métricas de legado.

## <a name="next-steps"></a>Passos Seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
