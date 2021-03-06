---
title: A agir os eventos de armazenamento de Blobs do Azure | Microsoft Docs
description: Utilize a Azure Event Grid para subscrever a eventos de armazenamento de Blobs.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 2762466c0130ead36372a93f4c3b852cb378a02a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="reacting-to-blob-storage-events"></a>A agir os eventos de armazenamento de BLOBs

Eventos de armazenamento do Azure permitem a reagir a criação e eliminação de blobs com arquiteturas sem servidor modernas de aplicações. Isto é feito sem a necessidade de código complicado ou serviços de consulta ineficaz e dispendiosa.  Em vez disso, os eventos são enviadas por push através do [grelha de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para subscritores como [das funções do Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou mesmo para o seu próprio serviço de escuta de http personalizado e apenas paga que utilizar. 

Cenários comuns de eventos do Blob storage incluem processamento de imagem ou as vídeo, a indexação da pesquisa ou qualquer fluxo de trabalho orientado para o ficheiro.  Carregamentos de ficheiros assíncrona são uma excelente opção para eventos.  Quando as alterações são pouco frequentes, mas o seu cenário exigir a capacidade de resposta imediata, arquitetura baseada em eventos pode ser especialmente eficiente.

Disponibilidade de eventos de armazenamento está associada à grelha de evento [disponibilidade](../../event-grid/overview.md) e ficará disponível noutras regiões como sucede grelha de eventos. Observe [eventos de armazenamento de BLOBs de rota para personalizadas web endpoint - CLI](storage-blob-event-quickstart.md) ou [eventos de armazenamento de BLOBs de rota para personalizadas web endpoint - PowerShell](storage-blob-event-quickstart-powershell.md) para obter um exemplo rápido. 

![Modelo de grelha de eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Contas do Blob Storage
Eventos de armazenamento de BLOBs estão disponíveis no [contas do Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) e na [contas de armazenamento de v2 de objetivo geral](../common/storage-account-options.md#general-purpose-v2). **V2 de objetivo geral (GPv2)** são contas de armazenamento que suportam todas as funcionalidades para todos os serviços de armazenamento, incluindo tabelas, filas, ficheiros e Blobs. A **conta de armazenamento de BLOBs** é uma conta do storage especializadas para armazenar os dados não estruturados como blobs (objetos) no Storage do Azure. Contas do blob storage são como as contas do storage para fins gerais e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho funcionalidades que utiliza atualmente, incluindo 100% de consistência de API para blobs de blocos e blobs de acréscimo. Para aplicações que requerem apenas armazenamento de blobs de blocos ou de blobs de acréscimo, recomendamos a utilização das contas de armazenamento de Blobs. 

## <a name="available-blob-storage-events"></a>Eventos de armazenamento de BLOBs disponíveis
Grelha de evento utiliza [subscrições de evento](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de evento para subscritores.  Subscrições de eventos de armazenamento de BLOBs podem incluir dois tipos de eventos:  

> |Nome do evento|Descrição|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|É desencadeado quando um blob é criado ou substituído através de `PutBlob`, `PutBlockList`, ou `CopyBlob` operações|
> |`Microsoft.Storage.BlobDeleted`|É desencadeado quando um blob é eliminado através de um `DeleteBlob` operação|

## <a name="event-schema"></a>Esquema de eventos
Eventos de armazenamento de blob contém todas as informações que necessárias para responder a alterações nos seus dados.  Pode identificar um evento de armazenamento de BLOBs, porque a propriedade eventType começa com "Microsoft".  
Informações adicionais sobre a utilização de propriedades de eventos de grelha de eventos estão documentadas na [esquema de eventos de evento grelha](../../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Tópico|string|Id do Azure Resource Manager completo da conta do storage que emite o evento.|
> |Requerente|string|O caminho relativo de recurso para o objeto que é o assunto do evento, utilizando o mesmo formato de Azure Resource Manager expandida que utilizamos para descrever as contas do storage, serviços e contentores de RBAC do Azure.  Este formato inclui um nome de blob preservação de maiúsculas e minúsculas.|
> |eventTime|string|Data/hora que o evento foi gerado, no formato ISO 8601|
> |eventType|string|"Microsoft.Storage.BlobCreated" ou "Microsoft.Storage.BlobDeleted"|
> |Id|string|Identificador exclusivo se este evento|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão do esquema de propriedades de nível superior.|
> |dados|objeto|Recolha de dados de eventos específicos de armazenamento de BLOBs|
> |data.contentType|string|O tipo de conteúdo do blob, tal como faria devolvido no cabeçalho de tipo de conteúdo de blob|
> |data.contentLength|número|O tamanho do blob como número inteiro que representa um número de bytes, tal como faria devolvido no cabeçalho do Content-Length de blob.  Enviadas com BlobCreated eventos, mas não com BlobDeleted.|
> |data.url|string|O url do objeto que é o assunto do evento|
> |data.eTag|string|O etag do objecto quando este evento é desencadeado.  Não está disponível para o evento BlobDeleted.|
> |data.api|string|O nome da operação de api que acionou este evento.  Para eventos de BlobCreated, este valor é "PutBlob", "PutBlockList" ou "CopyBlob".  Para eventos de BlobDeleted, este valor é "DeleteBlob".  Estes valores são os mesmos nomes de api que estejam presentes nos registos de diagnóstico do Storage do Azure.  Consulte [registados operações e mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|string|Um valor opaco cadeia que representa a sequência de lógica de eventos para qualquer nome de blob específico.  Os utilizadores podem utilizar a comparação de cadeia padrão para compreender a sequência relativa de dois eventos no mesmo nome de blob.|
> |data.requestId|string|Id do pedido gerado pelo serviço para a operação de armazenamento API.  Pode ser utilizado para correlacionar ao Storage do Azure registos através do campo "cabeçalho de id de pedido" nos registos de diagnóstico e é devolvido estabeleça chamada de API no cabeçalho de 'x-ms-request-id'. Consulte [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|string|Id do pedido de cliente fornecido para o armazenamento de operação de API.  Pode ser utilizado para correlacionar para registos de diagnóstico de armazenamento do Azure utilizando o campo "client-request-id" nos registos e podem ser fornecidas nos pedidos de cliente utilizando o cabeçalho "x-ms-client-request-id". Consulte [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|objeto|Dados de diagnóstico ocasionalmente incluídos pelo serviço de armazenamento do Azure.  Quando presente, deve ser ignorado pelos consumidores de eventos.|
|data.blobType|string|O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob".| 

Eis um exemplo de um evento de BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Para obter mais informações, consulte [esquema de eventos de armazenamento de BLOBs](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtragem de eventos
Subscrições de eventos de blob podem ser filtradas com base no tipo de evento e o nome do contentor e o nome do blob do objeto que foi criado ou eliminado.  Os filtros podem ser aplicados às subscrições de evento ou durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) da subscrição de evento ou [numa altura posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Filtros de requerente em projetos de grelha de eventos com base em "começa com" e "termina com" correspondências, para que os eventos com um assunto correspondente são entregues para o subscritor. 

O requerente de eventos de armazenamento de BLOBs utiliza o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para fazer corresponder a todos os eventos para uma conta de armazenamento, pode deixar os filtros de requerente vazio.

Para fazer corresponder os eventos de criados num conjunto de partilhar um prefixo de contentores de blobs, utilize um `subjectBeginsWith` filtrar como:

```
/blobServices/default/containers/containerprefix
```

Para fazer corresponder os eventos de blobs criados no contentor específico, utilize um `subjectBeginsWith` filtrar como:

```
/blobServices/default/containers/containername/
```

Para fazer corresponder os eventos de blobs criados no contentor específico, um prefixo de nome de blob de partilha, utilize um `subjectBeginsWith` filtrar como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para fazer corresponder os eventos de blobs criados na partilha um sufixo de BLOBs de contentor específico, utilize um `subjectEndsWith` filtro como ". log" ou "*.jpg"

Para obter mais informações, consulte [conceitos de grelha de evento](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Práticas de consumo de eventos
As aplicações que processam eventos de armazenamento de BLOBs devem seguir alguns práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para eventos de rota para o mesmo processador de eventos, é importante para não partem do princípio de eventos são de uma origem específica, mas o tópico da mensagem para se certificar de que são provenientes da conta do storage que está à espera de verificação.
> * Da mesma forma, verifique se o eventType um estão preparados para o processo e não partem do princípio de que todos os eventos recebidos serão os tipos de que espera.
> * Chegarem novas mensagens podem fora de ordem e após alguns atraso, utilize os campos de etag compreender se as informações sobre os objetos estão ainda atualizadas.  Além disso, utilize os campos do sequencer para compreender a ordem dos eventos de qualquer objeto específico.
> * Utilize o campo de blobType para compreender o tipo de operações que são permitidos no blob e tipos de qual biblioteca de cliente devem utilizar para aceder a BLOBs. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Utilize o campo de url com a `CloudBlockBlob` e `CloudAppendBlob` construtores para aceder a BLOBs.
> * Ignore os campos que não compreender.  Esta prática irão ajudá-lo a manter-se resiliente para novas funcionalidades que podem ser adicionadas no futuro.


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a grelha de eventos e atribua os eventos de armazenamento de BLOBs tentar:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Encaminhar o armazenamento de BLOBs eventos para um ponto final web personalizado](storage-blob-event-quickstart.md)
