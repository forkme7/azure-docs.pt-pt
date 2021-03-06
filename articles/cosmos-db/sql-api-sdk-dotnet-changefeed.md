---
title: 'Azure Cosmos DB: API de processador Feed do .NET alteração, SDK & recursos | Microsoft Docs'
description: Saiba tudo sobre a API de processador de Feed de alteração e o SDK, incluindo as datas de versão, as datas de extinção e as alterações efetuadas entre cada versão do SDK do processador .NET de Feed de alteração.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kfile
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/19/2018
ms.author: maquaran
ms.openlocfilehash: 72eb329c03893f801e112ad33bca0c57c5ee46a0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Processador de Feed de alteração de .NET SDK: Transferir e notas de versão
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alteração de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**Transferência do SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Alterar a documentação de referência da API de biblioteca de Feed de processador](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Introdução ao SDK .NET do processador de Feed de alteração](change-feed.md)|
|**Arquitetura suportada atual**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [O Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notas de versão

### <a name="stable-builds"></a>Estáveis compilações

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correções de estimativa de trabalho pendentes.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Melhoramentos de estabilidade.
* Suporte para o ponto de verificação manual.
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.21 e superior.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adiciona suporte para .NET 2.0 padrão. O pacote agora suporta `netstandard2.0` e `net451` os monikers do framework.
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.17.0 e superior.
* Compatível com [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) versões 1.5.1 e superior.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige um problema com o cálculo de estimativa do trabalho restantes quando o Feed de alteração foi vazio ou não trabalho estava pendente.
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.13.2 e superior.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionar um método para obter uma estimativa do trabalho restantes para ser processado no Feed de alteração.
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.13.2 e superior.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.14.1 e abaixo.

### <a name="pre-release-builds"></a>Versão de pré-lançamento compilações

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Nova v2 API:
  * Padrão de construtor para construção flexível do processador: a classe de ChangeFeedProcessorBuilder.
    * Pode efetuar qualquer combinação de parâmetros.
    * Pode demorar DocumentClient instância para a recolha de monitorização e/ou concessão (não disponível no v1).
  * IChangeFeedObserver.ProcessChangesAsync agora demora CancellationToken.
  * IRemainingWorkEstimator - o restantes estimator de trabalho pode ser utilizada em separado do processador.
  * Novos pontos de extensibilidade:
    * IParitionLoadBalancingStrategy - para personalizado de balanceamento de carga de partições entre instâncias do processador.
    * ILease, ILeaseManager - para a gestão de concessão personalizado.
    * IPartitionProcessor - alterações de processamento personalizadas numa partição.
* Utiliza o registo - [LibLog](https://github.com/damianh/LibLog) biblioteca.
* 100% compatível com versões anteriores com a API de v1.
* Compatível com [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.21.1 e superior.

## <a name="release--retirement-dates"></a>Versão & extinção datas
A Microsoft vai fornecer pelo menos notificação **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomenda-se que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível. 

Qualquer pedido de BD do Cosmos utilizando um SDK extinto será rejeitado pelo serviço.

<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [1.3.2](#1.3.2) |18 de Abril de 2018 |--- |
| [1.3.1](#1.3.1) |13 de março de 2018 |--- |
| [1.2.0](#1.2.0) |31 de outubro de 2017 |--- |
| [1.1.1](#1.1.1) |29 de Agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de Agosto de 2017 |--- |
| [1.0.0](#1.0.0) |07 de Julho de 2017 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a base de dados do Cosmos, consulte [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

