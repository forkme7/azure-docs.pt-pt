---
title: Unidades de pedido & calcular o débito - base de dados do Azure Cosmos | Microsoft Docs
description: Saiba mais sobre como compreender, especifique e estimar os requisitos da unidade de pedido na base de dados do Azure Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: rimman
ms.openlocfilehash: 2b69b3b5fee0d1148a762f817d9c5a8bc67806e7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades no Azure Cosmos DB de pedido

[BD do Azure do Cosmos](https://azure.microsoft.com/services/cosmos-db/) é globalmente múltiplos modelo base de dados distribuída da Microsoft. Com base de dados do Azure Cosmos, não terá alugar máquinas virtuais, implementar software ou monitorizar bases de dados. BD do Azure do Cosmos é operada e monitorizado continuamente por engenheiros superiores da Microsoft para fornecer proteção de dados, desempenho e disponibilidade de classe do mundo. Pode aceder a dados utilizando APIs à sua escolha, tal como o [API do SQL Server](documentdb-introduction.md), [MongoDB API](mongodb-introduction.md), [API de tabela](table-introduction.md)e através da graph o [Gremlin API](graph-introduction.md) - são suportados nativamente todos. 

A moeda de base de dados do Azure Cosmos é o **unidade de pedido (RU)**. Com RUs, não terá as capacidades de leitura/escrita ou o aprovisionar da CPU, memória e IOPS de reserva. BD do Azure do Cosmos suporta um número de APIs com operações diferentes de leituras simples e escreve consultas complexas de gráfico. Uma vez que nem todos os pedidos são iguais, são atribuídas uma quantidade normalizada de **unidades de pedido** com base na quantidade de computação necessária para servir o pedido. O número de unidades de pedido de uma operação é determinista e pode controlar o número de unidades de pedido utilizada por todas as operações do BD Azure Cosmos através de um cabeçalho de resposta. 

Para fornecer um desempenho previsível, terá de reservar débito em unidades de 100 RU por segundo. Pode [estimar o débito tem](request-units.md#estimating-throughput-needs) utilizando a BD do Cosmos Azure [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner).

![Calculadora de débito][5]

Depois de ler este artigo, poderá responder às seguintes questões:  

* Quais são unidades de pedido e custos de pedido na base de dados do Azure Cosmos?
* Como posso especificar a capacidade da unidade de pedido de um contentor do BD Azure Cosmos?
* Como estimar que tem de unidade de pedido da minha aplicação?
* O que acontece se posso exceder a capacidade de unidade de pedido para um contentor do BD Azure Cosmos?

BD do Cosmos do Azure é uma base de dados com múltiplos modelo; é importante ter em atenção que este artigo é aplicável a todos os modelos de dados e APIs do BD Azure Cosmos. Este artigo utiliza termos genéricos como *contentor* e um *item* referir-se genericamente a uma coleção, gráfico, ou uma tabela e um documento, um nó ou uma entidade, respetivamente.

## <a name="request-units-and-request-charges"></a>Unidades de pedido e custos de pedido
BD do Azure do Cosmos oferece um desempenho previsível, rápido por *reservar* recursos para satisfazer as necessidades de débito da sua aplicação.  Porque a aplicação de carga e alteração de padrões ao longo do tempo de acesso, base de dados do Azure Cosmos permite-lhe facilmente aumentar ou diminuir a quantidade de débito reservado disponível para a aplicação.

Com base de dados do Azure Cosmos, débito reservado é especificado em termos de unidades de pedido de processamento por segundo. Pode considerar unidades de pedido como moeda de débito, em *reservar* uma quantidade garantida de unidades de pedido disponíveis para a aplicação numa base por segundo.  Cada operação na base de dados do Azure Cosmos - escrita de um documento, executar uma consulta, atualizar um documento - consome CPU, memória e IOPS.  Ou seja, cada operação implica um *pedido encargos*, que é expresso em *unidades de pedido*.  Compreender os fatores que afetam os encargos de unidade de pedido, juntamente com os requisitos de débito da sua aplicação, permite-lhe executar a aplicação como custo eficientemente possível. O Explorador de dados no portal do Azure também é uma ferramenta wonderful para testar as principais de uma consulta.

Recomendamos que comece por ver o vídeo seguinte, onde o Azure Gestor de programa do Cosmos DB Andrew Liu aborda unidades de pedido.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Especificar a capacidade de unidade de pedido na base de dados do Azure Cosmos
Ao iniciar um novo contentor, especifique o número de unidades de pedido por segundo (RU por segundo) que pretende reservado. Com base no débito aprovisionado, base de dados do Azure Cosmos atribui partições físicas para alojar o contentor e divisões/rebalances dados em partições à medida que o que aumenta.

Podem ser criadas Azure contentores de BD do Cosmos como fixo ou ilimitado. Os contentores de tamanho fixo têm um limite máximo de 10 GB e débito de 10 000 de RU/s. Para criar um contentor ilimitado tem de especificar um débito mínimo de 1.000 RU/s e uma [chave de partição](partition-data.md). Uma vez que os dados poderão ter para serem divididas entre várias partições, é necessário escolher uma chave de partição tem uma cardinalidade elevada (100 para milhões de valores distintos). Ao selecionar uma chave de partição com vários valores distintos, certifique-se de que a tabela/contentor/gráfico e pedidos podem ser escalados uniformemente por base de dados do Azure Cosmos. 

> [!NOTE]
> Uma chave de partição é um limite de lógico e não um físico. Por conseguinte, não é necessário limitar o número de valores de chave de partição distintos. Na realidade é melhor ter distintos mais valores de chaves de partição que inferior, como base de dados do Azure Cosmos tem mais opções de balanceamento de carga.

Eis um fragmento de código para criar um contentor com 3,000 unidades de pedido por segundo, utilizando o SDK .NET:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

BD do Azure do Cosmos funciona um modelo de reserva para um débito. Ou seja, é-lhe faturado para a quantidade de débito *reservado*, independentemente da quantidade de débito de que está ativamente *utilizado*. Como a aplicação da alteração de padrões de carga, dados e a utilização, pode dimensionar facilmente para cima e baixo a quantidade de reservado RUs através de SDKs ou utilizando o [Portal do Azure](https://portal.azure.com).

Cada contentor está mapeado para um `Offer` recursos no Azure Cosmos DB, que tem metadados sobre o débito aprovisionado. Pode alterar o débito alocado ao procurar o recurso de oferta correspondente para um contentor, em seguida, atualizá-la com o novo valor de débito. Eis um fragmento de código para alterar o débito de um contentor para 5000 unidades de pedido por segundo, utilizando o SDK .NET:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Não há nenhum impacto sobre a disponibilidade do seu contentor quando altera o débito. Normalmente, o débito reservado nova é eficaz dentro de segundos na aplicação de débito de novo.

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolamento de débito nas bases de dados globalmente distribuídas

Quando ter replicado a base de dados para mais de uma região, base de dados do Azure Cosmos fornece isolamento de débito para se certificar de que a utilização de RU na região de um não afeta utilização RU noutra região. Por exemplo, se escrever dados para uma região e ler os dados do noutra região, os RUs utilizado para efetuar a operação de escrita na região *A* não entram na direção oposta ao RUs utilizados para a operação de leitura na região *B*. RUs não estão divididos em regiões no qual implementou. Cada região na qual a base de dados é replicado possui a quantidade total de RUs aprovisionado. Para obter mais informações sobre a replicação global, consulte [como distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considerações de unidade de pedido
Quando a estimar o número de unidades de pedido para aprovisionar para o contentor do Azure Cosmos DB, é importante considerar as seguintes variáveis em consideração:

* **Tamanho do item**. À medida que aumenta de tamanho do número de unidades de pedido utilizadas para ler ou escrever os dados também aumenta.
* **Contagem de propriedade do item**. Partindo do princípio de indexação predefinido de todas as propriedades, as unidades consumidas para escrever um aumento de nó/documento/entidade à medida que aumenta de contagem de propriedade.
* **Consistência dos dados**. Quando utilizar modelos de consistência de dados, tais como forte ou tem um vínculo vinculada, unidades de pedido adicionais são consumidas ler itens.
* **Indexada propriedades**. Uma política de índice em cada contentor determina as propriedades que são indexadas por predefinição. Pode reduzir o consumo de unidade de pedido ao limitar o número de propriedades indexadas ou ao ativar a indexação lento.
* **A indexação de documento**. Por predefinição a cada item é automaticamente indexado. Consumir unidades de pedido de menos se optar por não indexar alguns dos seus itens.
* **Padrões de consulta**. A complexidade de uma consulta afeta o número de unidades de pedido são consumidas para uma operação. O número de predicados, natureza os predicados, projeções, número de UDFs e o tamanho dos dados de origem - todos os influenciar o custo das operações de consulta.
* **Utilização do script**.  Tal como acontece com consultas, acionadores e procedimentos armazenados consumam unidades de pedido com base na complexidade das operações que está a ser executadas. Como desenvolver a sua aplicação, Inspecione o cabeçalho de encargos de pedido para compreender melhor a forma como cada operação está a consumir capacidade de unidade de pedido.

## <a name="estimating-throughput-needs"></a>A estimar necessidades de débito
Uma unidade de pedido é uma medida de custo de processamento de pedidos normalizada. Uma unidade de pedido único representa a capacidade de processamento necessária para ler (através da ligação automática ou id) de um único 1 KB por item de 10 valores de propriedade exclusivo (excluindo as propriedades do sistema). Um pedido para criar (insert), substituir ou eliminar o item mesmo irá consumir mais processamento do serviço e, deste modo, mais unidades de pedido.   

> [!NOTE]
> A linha de base da unidade de pedido de 1 para um 1 KB item corresponde a uma ação obter simple através da ligação automática ou id do item.
> 
> 

Por exemplo, aqui está uma tabela que mostra como muitas unidades de pedido para aprovisionar para itens com três tamanhos diferentes (de 1 KB, 4 KB e 64 KB) e dois níveis de desempenho diferentes (500 lidas por segundo + 100 escritas por segundo e 500 lidas por segundo + 500 escritas por segundo). A consistência de dados foi configurada no *sessão*, e a política de indexação foi definida como *nenhum*.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do item</strong></p></td>
            <td valign="top"><p><strong>Leituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Writes/second</strong></p></td>
            <td valign="top"><p><strong>Unidades de pedidos</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Utilizar a Calculadora de unidade de pedido
Para ajudar a otimizar as estimativas de débito de clientes, não há uma conta baseada na web [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner) para ajudar a estimar os requisitos da unidade de pedido para operações comuns, incluindo:

* Item cria (escritas)
* Leituras de item
* Elimina o item
* Atualizações do item

A ferramenta também inclui suporte para estimar necessidades de armazenamento de dados baseadas em itens de exemplo que fornecer.

Utilizar a ferramenta é simple:

1. Carregar um ou mais itens representativos (por exemplo, um documento JSON de exemplo).
   
    ![Carregar itens para a Calculadora de unidade de pedido][2]
2. Para estimar os requisitos de armazenamento de dados, introduza o número total de itens (por exemplo, documentos, tabelas ou gráficos) pretende armazenar.
3. Introduza o número de criação, leitura, atualização e operações delete que precisa (numa base por segundo). Para estimar os encargos de unidade de pedido de operações de atualização do item, carregue uma cópia do item de exemplo do passo 1 acima, que inclui atualizações de campo típica.  Por exemplo, se o item atualizações normalmente modificar duas propriedades com o nome *lastLogin* e *userVisits*, em seguida, basta copiar um item de exemplo, atualize os valores para as duas propriedades e carregar o item copiado.
   
    ![Introduza os requisitos de débito na Calculadora de unidade de pedido][3]
4. Clique em calcular e examine os resultados.
   
    ![Resultados de calculadora de unidade de pedido][4]

> [!NOTE]
> Se tiver de tipos de itens que variam significativamente em termos de tamanho e o número de propriedades indexadas, em seguida, carregue uma amostra de cada *tipo* típica do item para a ferramenta e, em seguida, calcular os resultados.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Utilize o cabeçalho de resposta de encargos de pedido de base de dados do Azure Cosmos
Cada resposta do serviço de base de dados do Azure Cosmos inclui um cabeçalho personalizado (`x-ms-request-charge`) que contenha as unidades de pedido consumidas para um determinado pedido. Também é acessível através do Azure Cosmos DB SDKs este cabeçalho. SDK .NET, `RequestCharge` é uma propriedade do `ResourceResponse` objeto.  Para consultas, o Explorador de dados de base de dados do Azure Cosmos no portal do Azure fornece informações de encargos de pedido para consultas executadas.

Com isto em mente, um método para estimar a quantidade de débito reservado exigido pela sua aplicação consiste em registar os encargos de unidade de pedido associados a execução de operações típicas num item representativo utilizado pela sua aplicação e, em seguida, estimar o número de operações que antecipa para efetuar a cada segundo.  Lembre-se de que medir e incluir consultas típicas e a utilização de script de base de dados do Azure Cosmos bem.

> [!NOTE]
> Se tiver de tipos de itens de que diferem significativamente em termos de tamanho e o número de propriedades indexadas, em seguida, registe a taxa de unidade de pedido de operação aplicável associada a cada *tipo* do item típica.
> 
> 

Por exemplo:

1. Registe a taxa de unidade de pedido de criação (inserir) um item típico. 
2. Registe a taxa de unidade de pedido de leitura de um item típico.
3. Registe a taxa de unidade de pedido de atualizar um item típico.
4. Registe a taxa de unidade de pedido de consultas de item típica, comuns.
5. O pedido unidade encargos de quaisquer scripts personalizados (procedimentos armazenados, acionadores, funções definidas pelo utilizador) utilizado pela aplicação de registo
6. Calcule as unidades de pedido necessários indicadas o número estimado de operações que antecipa para ser executada a cada segundo.

## <a name="a-request-unit-estimate-example"></a>Um exemplo de estimativa de unidade de pedido
Considere o seguinte documento de ~ 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Documentos são minified na base de dados do Azure Cosmos, pelo que o sistema calcular o tamanho do documento acima é ligeiramente inferior a 1 KB.
> 
> 

A tabela seguinte mostra pedido aproximado encargos de unidade para operações comuns deste item (encargos de unidade pedido aproximado parte do princípio de que o nível de consistência de conta é definido como *sessão* e de que todos os itens são automaticamente indexada):

| Operação | Encargos de unidade de pedido |
| --- | --- |
| Criar item |~ 15 RU |
| Item de leitura |~ 1 RU |
| Item de consulta por id |~2.5 RU |

Além disso, esta tabela mostra pedido aproximado encargos de unidade para consultas típicos utilizados na aplicação:

| Consulta | Encargos de unidade de pedido | n. º de itens devolvidos |
| --- | --- | --- |
| Selecione prato por id |~2.5 RU |1 |
| Selecione foods por fabricante |~ 7 RU |7 |
| Selecione pelo grupo prato e ordem por ponderação |~70 RU |100 |
| Selecione 10 foods superiores de um grupo de prato |~10 RU |10 |

> [!NOTE]
> Custos de RU varia com base no número de itens devolvidos.
> 
> 

Com estas informações, pode estimar os requisitos de RU para esta aplicação dado o número de operações e consultas de que esperar por segundo:

| Consulta/operação | Número estimado por segundo | RUs necessárias |
| --- | --- | --- |
| Criar item |10 |150 |
| Item de leitura |100 |100 |
| Selecione foods por fabricante |25 |175 |
| Selecione pelo grupo prato |10 |700 |
| Selecione 10 principais |15 |Total de 150 |

Neste caso, o que esperava um requisito de débito médio de 1,275 RU/s.  Arredondamento até 100 mais próximo, teria de aprovisionar 1.300 RU/s para o contentor desta aplicação.

## <a id="RequestRateTooLarge"></a> Exceder os limites de débito reservado do BD Azure Cosmos
Recuperar-se de que o consumo de unidade de pedido é avaliado uma taxa por segundo. Para aplicações que excedem a taxa de unidade de pedido de aprovisionamento, os pedidos serão taxa limitado até que a taxa de ignora abaixo do nível de débito aprovisionado. Quando um pedido obtém taxa limitada, o servidor preventivamente termina o pedido com `RequestRateTooLargeException` (código de estado HTTP 429) e devolve o `x-ms-retry-after-ms` cabeçalho que indica a quantidade de tempo, em milissegundos, que o utilizador terá de aguardar antes de repetir o pedido.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se estiver a utilizar os SDK de cliente .NET e LINQ consultas, em seguida, na maioria das nunca tem de lidar com esta exceção, como a versão atual do SDK de cliente .NET implicitamente intercete esta resposta, respeita o servidor especificado depois de repetir cabeçalho e repete as tentativas de o pedido automaticamente. A menos que a sua conta está a ser acedida em simultâneo por vários clientes, a tentativa seguinte ocorrerá será bem sucedida.

Se tiver mais do que um cliente cumulativamente e a funcionar acima a taxa de pedidos, o comportamento de repetição predefinido não poderá suffice e o cliente irá gerar um `DocumentClientException` com o estado de código 429 à aplicação. Em casos como esta, poderá considerar a processar o comportamento de repetição e a lógica na rotinas de processamento de erros da aplicação ou aumentar o débito aprovisionado para o contentor.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre débito reservado com bases de dados do Azure Cosmos DB, explore estes recursos:

* [Preços de base de dados do Cosmos do Azure](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [A criação de partições de dados na base de dados do Azure Cosmos](partition-data.md)

Para saber mais sobre a base de dados do Azure Cosmos, consulte o artigo da base de dados do Azure Cosmos [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Para começar com dimensionamento e desempenho de teste com base de dados do Azure Cosmos, consulte o artigo [desempenho e dimensionamento de teste com base de dados do Azure Cosmos](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
