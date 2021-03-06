---
title: Serviço limites na Azure Search | Microsoft Docs
description: Limites de serviço utilizados para o planeamento de capacidade e os limites máximos nos pedidos e respostas de pesquisa do Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 59b6efd8459f675bc44891d7b04251260fdd38e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2018
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço da Azure Search
Máximo limita-se no armazenamento, as cargas de trabalho e quantidades de índices, documentos, e outros objetos dependem se [aprovisionar da Azure Search](search-create-service-portal.md) em **livres**, **básico**, ou **Padrão** escalões de preço.

+ **Livre** é um serviço partilhado do multi-inquilino que vem com a sua subscrição do Azure.

+ **Básico** fornece recursos de informática dedicados para cargas de trabalho de produção em escala mais pequena.

+ **Standard** é executado em máquinas dedicadas com mais capacidade de armazenamento e processamento em cada nível. Standard, é apresentada no quatro níveis: S1, S2, S3 e S3 HD.

  S3 alta densidade (S3 HD) é projetada para cargas de trabalho específicas: [vários inquilinos](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequenos (um milhões de documentos por índice, índices de três thousand por serviço). Esta camada não fornece o [funcionalidade de indexador](search-indexer-overview.md). No S3 HD, a ingestão de dados tem tirar partido a abordagem de push, utilizando chamadas de API para enviar dados de origem para o índice. 

> [!NOTE]
> Um serviço é aprovisionado em nenhum escalão específico. Jumping camadas para obterem capacidade envolve o aprovisionamento de um novo serviço (não há nenhuma atualização no local). Para obter mais informações, consulte [escolha um SKU ou camada](search-sku-tier.md). Para obter mais informações sobre como ajustar capacidade dentro de um serviço que já tenha sido já aprovisionada, consulte [Dimensionar níveis de recursos de consulta e cargas de trabalho de indexação](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites de subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Gratuito | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |
| Campos máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |
| Perfis de classificação máximos por índice |100 |100 |100 |100 |100 |100 |
| Funções máximas por perfil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> criados depois de enlace tardio 2017 tem um limite de aumento de 15 índices, indexadores e origens de dados, os serviços básicos. Serviços que criou anteriormente ter 5. O escalão básico destina o SKU apenas com um limite inferior de 100 campos por índice.

## <a name="document-limits"></a>Limites do documento 

Na maior parte das regiões, pesquisa do Azure (Basic, S1, S2, S3, S3 HD) de escalões de preço contagens de documentos ilimitados para todos os serviços criou após Novembro/Dezembro de 2017. Esta secção identifica as regiões onde aplicam limites e como determinar se o seu serviço é afetado. 

Para determinar se o serviço tem os limites do documento, verifique o mosaico de utilização na página de descrição geral do seu serviço. Contagens de documentos estão ilimitado ou assunto para um limite com base na camada.

  ![Mosaico utilização](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Regiões e os serviços de terem os limites do documento

Serviços de terem limites se foram criados antes de enlace tardio 2017 ou estão em execução em centros de dados utilizando inferiores capacidade clusters para o alojamento de serviços de pesquisa do Azure. Os centros de dados afetadas são as seguintes regiões:

+ Leste da Austrália
+ Ásia Oriental
+ Índia Central
+ Oeste do Japão
+ EUA Centro-Oeste

Para os serviços sujeitas a limites de documento, são aplicáveis os seguintes limites máximos:

|  Gratuito | Básica | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 biliões por serviço |1 milhão por índice ou 200 milhões por partição |

> [!Note] 
> Para os serviços de alta densidade de S3 criados após o enlace tardio 2017, o documento de milhões de 200 por partição foi removido, mas o documento de milhões de 1 por permanece de limite de índice.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada de API

O tamanho máximo do documento ao chamar uma API de índice é aproximadamente 16 megabytes.

O tamanho do documento é, na verdade, de um limite no tamanho do corpo do pedido de API do índice. Uma vez que pode passar um lote de vários documentos para a API de índice de uma só vez, o limite de tamanho verdade depende de documentos quantos são no batch. Para um lote com um único documento, o tamanho máximo do documento é 16 MB de JSON.

Para manter o tamanho do documento para baixo, lembre-se excluir os dados não consultável do pedido. Imagens e outros dados binários não são diretamente consultável e não devem ser armazenados no índice. Para integrar os dados não consultável os resultados da pesquisa, defina um campo não pesquisáveis que armazena uma referência de URL para o recurso.

## <a name="indexer-limits"></a>Limites do indexador

Um limite de aumento de 15 índices, indexadores e origens de dados, os serviços básicos criados após o enlace tardio 2017 ter.

| Recurso | Livre&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/A |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/A |
| Carga máxima de indexação por invocação |10 000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/A |
| Tempo de execução máximo | 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/A  |
| Indexador de blob: o tamanho máximo de blob, MB |16 |16 |128 |256 |256 |N/A  |
| Indexador de blob: número máximo de conteúdo extraídos de um blob caracteres |32,000 |64,000 |milhões de 4 |milhões de 4 |milhões de 4 |N/A |

<sup>1</sup> os serviços gratuitos ter indexador tempo de execução máximo de 3 minutos para as origens de blob e 1 minuto para todas as outras origens de dados.

<sup>2</sup> criados depois de enlace tardio 2017 tem um limite de aumento de 15 índices, indexadores e origens de dados, os serviços básicos. Serviços que criou anteriormente ter 5.

<sup>3</sup> serviços S3 HD não incluem o suporte.

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

Calcula QPS tem de ter sido programada independentemente por cada cliente. Índice de tamanho e complexidade, tamanho de consulta e complexidade e a quantidade de tráfego são determinants primários de QPS. Não é possível para oferecer estimativas significativas quando esses fatores são desconhecidos.

Calcula é mais previsível quando calculado nos serviços em execução em recursos dedicados (escalões básico e padrão). Pode fazer a estimativa QPS mais detalhadamente porque tem controlo sobre mais dos parâmetros. Para obter orientações sobre a estimativa da abordagem, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limites de pedido de API
* Máximo de 16 MB por pedido <sup>1</sup>
* Comprimento máximo do URL de 8 KB
* Documentos máximo de 1000 por lote de índice carrega, intercala ou elimina
* Campos máximos de 32 na cláusula $orderby
* Tamanho do termo de pesquisa máximo é 32,766 bytes (32 KB menos de 2 bytes) de texto codificado UTF-8

<sup>1</sup> na Azure Search, o corpo de um pedido está sujeita a um limite superior de 16 MB, impor um limite prático no conteúdo do campos individuais ou de coleções que caso contrário, não estão restritos pelo teórico limites (consulte [suportado dados tipos de](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições de composição de campo e).

## <a name="api-response-limits"></a>Limites de resposta de API
* Máximos 1000 documentos devolvidos por página de resultados de pesquisa
* Sugestões de 100 máximos devolvidos por pedido de API Sugerir

## <a name="api-key-limits"></a>Limites de chave de API
Chaves de API são utilizadas para autenticação do serviço. Existem dois tipos. Chaves de administração são especificadas no cabeçalho do pedido e concedam acesso de leitura e escrita completa para o serviço. Chaves de consulta são só de leitura, especificado no URL e, normalmente distribuídas por aplicações cliente.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
