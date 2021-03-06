---
title: SDK .NET da Azure CosmosDB tabela API & recursos | Microsoft Docs
description: Saiba tudo sobre API do Azure Cosmos DB tabela, incluindo as datas de versão, as datas de extinção e as alterações efetuadas entre cada versão.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 7e012e07b8f93554ea44404c611a7bc0eb64a0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Cosmos Azure API de .NET de tabela de base de dados: Transferir e notas de versão
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Transferência do SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentação da API**|[Documentação de referência da API de .NET](https://aka.ms/acdbtableapiref)|
|**Início rápido**|[Azure Cosmos DB: Criar uma aplicação com o .NET e a tabela de API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a API de tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Arquitetura suportada atual**|[O Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Foram adicionada validação para ETAGs com formato incorreto no modo direto.
* Erros de consulta LINQ fixo no modo de Gateway.
* APIs síncronas agora ser executada no conjunto de threads com SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb TableRequestOptions
* Correção de Erros

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de extinção
A Microsoft disponibiliza notificação, pelo menos, **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

O [Windowsazure PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido e substituído pelo pacote de pré-visualização do [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) pacote. O SDK Windowsazure PremiumTable será descontinuado no dia 15 de Novembro de 2018, em que momento pedidos para o SDK extinto não será permitido.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomenda-se que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível. 

Todos os pedidos de BD do Cosmos do Azure utilizando um SDK extinto são rejeitados pelo serviço.
<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 de Março de 2018|--- |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|--- |
| [1.0.0](#1.0.0) |15 de Novembro de 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 de Novembro de 2017 |--- |

## <a name="troubleshooting"></a>Resolução de problemas

Se receber o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

durante a tentativa de utilizar o pacote Microsoft.Azure.CosmosDB.Table NuGet, tem duas opções para corrigir o problema:

* Utilize a consola de gerir pacotes para instalar o pacote de Microsoft.Azure.CosmosDB.Table e as respetivas dependências. Para tal, escreva o seguinte na consola do Gestor de pacotes para a sua solução. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Utilizar a ferramenta de gestão de pacote Nuget preferencial, instale o pacote Microsoft.Azure.Storage.Common Nuget antes de instalar Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ver também
Para saber mais sobre a API de tabela de base de dados do Azure Cosmos, consulte [introdução à API de tabela de base de dados do Azure Cosmos](table-introduction.md). 
