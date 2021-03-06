---
title: Em vez de ETL, conceber ELT para o Azure SQL Data Warehouse | Microsoft Docs
description: Em vez de ETL, crie um processo de extração, carregamento e transformação (ELT) para carregar os dados ou o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5ceb8cfd8efea66dbf17b8c522316b9a010e437d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2018
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Conceber a extração, carregamento e transformação (ELT) para o armazém de dados SQL do Azure

Em vez de extracção, transformação e carregamento (ETL), crie um processo de extração, carregamento e transformação (ELT) para carregar dados para o Azure SQL Data Warehouse. Este artigo apresenta formas para criar um processo ELT move os dados para um armazém de dados do Azure.

## <a name="what-is-elt"></a>O que é ELT?

Extrair, a carga, e transformação (ELT) é um processo pelo qual dados movem-se de um sistema de origem para um armazém de dados de destino. Este processo é efetuado regularmente, por exemplo hora a hora ou diariamente obter recentemente gerou dados no armazém de dados. A melhor forma de obter dados de origem para o armazém de dados está a desenvolver um processo ELT que utiliza o PolyBase para carregar dados para o SQL Data Warehouse.

ELT carrega primeiro e, em seguida, transforma os dados, enquanto que a extração, transformação e carregamento (ETL) transforma os dados antes de carregá-lo. Efetuar ELT em vez de ETL guarda o custo de fornecer os seus próprios recursos para transformar os dados antes do que é carregado. Ao utilizar o SQL Data Warehouse, ELT tira partido do sistema MPP para efetuar as transformações.

Apesar de existirem muitas variações para implementar ELT para o SQL Data Warehouse, estes são os passos básicos:  

1. Extrair os dados de origem para ficheiros de texto.
2. Encaminhado para os dados no Blob storage do Azure ou do Azure Data Lake Store.
3. Prepare os dados de carregamento.
2. Carregar os dados para o SQL Data Warehouse tabelas de testes, utilizando o PolyBase.
3. Transforme os dados.
4. Inserir os dados em tabelas de produção.


Para um tutorial de carregamento, consulte [PolyBase de utilização para carregar dados do blob storage do Azure ao Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [carregar padrões blogue](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opções para carregar com o PolyBase

O PolyBase é uma tecnologia que acede a dados fora da base de dados através da linguagem T-SQL. É a melhor forma de carregar dados para o SQL Data Warehouse. Com o PolyBase, os dados carrega em paralelo da origem de dados diretamente em nós de computação. 

Para carregar dados com o PolyBase, pode utilizar qualquer uma destas opções de carregamento.

- [O PolyBase com T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os dados estão no Blob storage do Azure ou do Azure Data Lake Store. Dá-lhe mais controlo sobre o processo de carregamento, mas também requer que defina os objetos de dados externas. Os outros métodos de definem estes objetos nos bastidores, como mapear tabelas de origem para tabelas de destino.  Para orquestrar as cargas de T-SQL, pode utilizar o Azure Data Factory, SSIS ou as funções do Azure. 
- [O PolyBase com SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funciona bem quando os dados de origem estiver no SQL Server, SQL Server no local ou na nuvem. SSIS define a origem para mapeamentos de tabela de destino e também orquestra a carga. Se já tiver pacotes SSIS, pode modificar os pacotes para trabalhar com o novo destino de armazém de dados. 
- [O PolyBase com o Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) é outra ferramenta de orquestração.  Define um pipeline e agendas de tarefas. 
- [O PolyBase com o Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transfere dados de uma tabela de armazém de dados do SQL Server para um dataframe Databricks e/ou escreve dados de um dataframe Databricks a uma tabela do SQL Data Warehouse.

### <a name="polybase-external-file-formats"></a>Formatos de ficheiro externo do PolyBase

O PolyBase carrega dados da UTF-8 e UTF-16 codificado delimitada por ficheiros de texto. Para além dos ficheiros de texto delimitado, carrega a partir de formatos de ficheiro Hadoop RC ficheiro, ORC e Parquet. O PolyBase pode carregar dados do Gzip e ficheiros comprimidos Snappy. O PolyBase atualmente não suporta ASCII expandida, o formato de largura fixa e formatos aninhados, tais como o WinZip, JSON e XML.

### <a name="non-polybase-loading-options"></a>Opções de carregamento de não-PolyBase
Se os dados não são compatíveis com o PolyBase, pode utilizar [bcp](/sql/tools/bcp-utility) ou [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP carrega diretamente ao SQL Data Warehouse, sem passar o Blob storage do Azure e destina-se apenas para cargas de pequenas. Tenha em atenção de que o desempenho de carregamento de uma destas opções é significativamente mais lento do que o PolyBase. 


## <a name="extract-source-data"></a>Extrair dados de origem

Obter dados fora do seu sistema de origem depende da origem.  O objetivo é mover os dados para os ficheiros de texto delimitado. Se estiver a utilizar o SQL Server, pode utilizar [ferramenta da linha de comandos do bcp](/sql/tools/bcp-utility) para exportar os dados.  

## <a name="land-data-to-azure-storage"></a>Dados de telefone para o armazenamento do Azure

Para encaminhado para os dados no armazenamento do Azure, é possível mover a [Blob storage do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Em ambos, os dados devem ser armazenados nos ficheiros de texto. O Polybase pode carregar a partir de qualquer localização.

Estas são as ferramentas e serviços, que pode utilizar para mover dados para o Storage do Azure.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) serviço melhora o débito de rede, o desempenho e à previsibilidade. O ExpressRoute é um serviço que encaminha os seus dados através de uma ligação privada dedicada para o Azure. As ligações do ExpressRoute não encaminhar dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, latências inferiores e uma maior segurança ligações típicas através da internet pública.
- [Utilitário AZCopy](../storage/common/storage-moving-data.md) move os dados para armazenamento do Azure através da internet pública. Isto funciona se os tamanhos de dados for inferior a 10 TB. Para executar cargas regularmente com o AZCopy, teste a velocidade da rede para ver se é aceitável. 
- [Fábrica de dados do Azure (ADF)](../data-factory/introduction.md) tem um gateway que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados a partir do seu servidor local até o Storage do Azure. Para utilizar o Data Factory com o SQL Data Warehouse, consulte [carregar dados para o SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Preparar dados

Poderá ter de preparar e limpar os dados na sua conta de armazenamento antes de carregá-lo para o SQL Data Warehouse. Preparação de dados pode ser executada enquanto os dados na origem, como, exporta os dados para ficheiros de texto, ou depois dos dados no armazenamento do Azure.  É mais fácil trabalhar com os dados no como início do processo quanto possível.  

### <a name="define-external-tables"></a>Definir as tabelas externas
Pode carregar dados, terá de definir as tabelas externas no seu armazém de dados. O PolyBase utiliza tabelas externas para definir e aceder aos dados no armazenamento do Azure. A tabela externa é semelhante a uma tabela normal. A principal diferença é que os pontos de tabela externa para dados armazenados fora do armazém de dados. 

Definir as tabelas externas envolve especificando a origem de dados, o formato de ficheiros de texto e as definições de tabela. Estes são os tópicos de sintaxe de T-SQL, terá de:
- [CRIAR ORIGEM DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql)

Para obter um exemplo de criação de objetos externos, consulte o [criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) passo do tutorial de carregamento.

### <a name="format-text-files"></a>Ficheiros de texto de formato

Depois de definidos os objetos externos, tem de alinhar as linhas dos ficheiros de texto com a tabela externa e a definição de formato de ficheiro. Os dados em cada linha do ficheiro de texto devem estar alinhadas com a definição de tabela.

Para formatar os ficheiros de texto:

- Se os dados for proveniente de uma origem não relacionais, terá de transformá-lo em linhas e colunas. Se os dados estão a partir de uma origem relacional ou não relacionais, os dados têm de ser transformados para alinhar com as definições da coluna da tabela na qual pretende carregar os dados. 
- Formato de dados no ficheiro de texto para alinhar com os tipos de dados e de colunas na tabela de destino do armazém de dados do SQL Server. Desalinhamento entre a tabela de armazém de dados e tipos de dados nos ficheiros de texto externo faz com que as linhas a ser rejeitada durante o carregamento.
- Campos separados no ficheiro de texto com um terminador.  Não se esqueça de utilizar um caráter ou uma sequência de carateres que não se encontra na sua origem de dados. Utilize o terminador especificado com [criar formato de ficheiro externo](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Carregar para uma tabela de teste
Para obter dados no armazém de dados, funciona bem para o primeiro carregamento de dados para uma tabela de teste. Ao utilizar uma tabela de testes, pode processar erros sem que interfiram entre as tabelas de produção e evitar a execução de operações de reversão na tabela de produção. Uma tabela de testes também dá-lhe a oportunidade para utilizar o SQL Data Warehouse para executar transformações antes de inserir os dados em tabelas de produção.

Para carregar com T-SQL, execute o [criar tabela AS SELECIONE (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) instrução T-SQL. Este comando insere os resultados de uma instrução select para uma nova tabela. Quando seleciona a instrução de uma tabela externa, importa os dados externos. 

No exemplo seguinte, ext. Data é uma tabela externa. Todas as linhas são importadas para uma nova tabela chamada dbo. Data.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformar os dados
Embora seja dados na tabela de teste, efetue transformações que requer a sua carga de trabalho. Em seguida, mova os dados para uma tabela de produção.

## <a name="insert-data-into-production-table"></a>Inserir dados na tabela de produção

A inserção em... Instrução SELECT move os dados da tabela de teste para a tabela permanente. 

Como criar um processo ETL, tente executar o processo de uma amostra de pequeno teste. Tente extrair 1000 linhas da tabela para um ficheiro, movê-la para o Azure e, em seguida, tente ao carregá-lo para uma tabela de teste. 

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros
Muitos dos nossos parceiros tem carregar soluções. Para obter mais informações, consulte uma lista dos nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Passos Seguintes
Para carregar orientações, consulte [orientações para dados de carga](guidance-for-loading-data.md).


