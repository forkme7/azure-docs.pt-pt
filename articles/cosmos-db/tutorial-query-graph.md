---
title: Como consultar dados de gráfico no Azure Cosmos DB? | Microsoft Docs
description: Saiba como consultar dados de gráfico no Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: efeee797a537a16b1b44174ed2d6f7ea3348a25a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Tutorial: Consultar a Graph API do Azure Cosmos DB através do Gremlin

A [Graph API](graph-introduction.md) do Azure Cosmos DB suporta consultas [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Este artigo fornece documentos e consultas de exemplo para poder começar. É fornecida uma referência ao Gremlin detalhada no artigo [Suporte para Gremlin](gremlin-support.md).

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com o Gremlin

## <a name="prerequisites"></a>Pré-requisitos

Para estas consultas funcionarem, tem de ter uma conta do Azure Cosmos DB e dados de gráfico no contentor. Não tem qualquer um destes? Conclua o [início rápido de 5 minutos](create-graph-dotnet.md) ou o [tutorial do programador](tutorial-query-graph.md) para criar uma conta e povoar a base de dados. Pode executar as seguintes consultas com a [biblioteca de gráfico .NET do Azure Cosmos DB](graph-sdk-dotnet.md), a [consola do Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) ou o seu controlador Gremlin favorito.

## <a name="count-vertices-in-the-graph"></a>Contar vértices no gráfico

O fragmento seguinte mostra como contabilizar o número de vértices no gráfico:

```
g.V().count()
```

## <a name="filters"></a>Filtros

Pode executar filtros através dos passos `has` e `hasLabel` do Gremlin e combiná-los com `and`, `or` e `not` para criar filtros mais complexos. O Azure Cosmos DB fornece uma indexação independente de esquema de todas as propriedades nos vértices e graus para consultas rápidas:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projeção

Pode projetar algumas propriedades nos resultados da consulta com o passo `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Localizar arestas e vértices relacionados

Até ao momento, apenas vimos operadores de consulta que funcionam em qualquer base de dados. Os gráficos são rápidos e eficientes para operações transversais quando precisar de navegar para arestas e vértices relacionados. Vamos localizar todos os amigos do Tomás. Podemos fazê-lo através do passo `outE` do Gremlin para localizar todas as arestas exteriores do Tomás e atravessá-las nos vértices interiores dessas arestas através do passo `inV` do Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A consulta seguinte executa dois saltos para localizar todos os "amigos de amigos" do Tomás ao chamar `outE` e `inV` duas vezes. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Pode criar consultas mais complexas e implementar uma lógica de gráfico transversal poderosa com o Gremlin, incluindo misturar expressões de filtro, efetuar o ciclo com o passo `loop` e implementar a navegação condicional com o passo `choose`. Saiba mais sobre o que pode fazer com o [Suporte para Gremlin](gremlin-support.md)!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a fazer consultas com o Graph 

Agora pode avançar para o tutorial seguinte para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-sql-api.md)
