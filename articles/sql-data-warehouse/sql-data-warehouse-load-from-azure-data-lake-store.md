---
title: 'Tutorial: Carregar a partir do arquivo Azure Data Lake ao Azure SQL Data Warehouse | Microsoft Docs'
description: Utilize as tabelas externas do PolyBase para carregar dados do Azure Data Lake Store para o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c6030d1951c22dddfe6df01225c63cf503a370ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Carregar dados do Azure Data Lake Store ao SQL Data Warehouse
Utilize as tabelas externas do PolyBase para carregar dados do Azure Data Lake Store para o Azure SQL Data Warehouse. Apesar de poder executar consultas ad hoc em dados armazenados no ADLS, recomendamos que importar os dados para o armazém de dados do SQL Server para um melhor desempenho.

> [!div class="checklist"]
> * Crie objetos de base de dados necessários para carregar a partir do Azure Data Lake Store.
> * Ligar a um diretório do Azure Data Lake Store.
> * Carregar dados para o Azure SQL Data Warehouse.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Para executar este tutorial, precisa de:

* Aplicação de diretório Active Directory do Azure a utilizar para autenticação de serviços. Para criar, siga [autenticação do Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Terá do ID de cliente, a chave e o OAuth2.0 valor de ponto final do Token da aplicação do Active Directory para ligar ao seu Azure Data Lake do SQL Data Warehouse. Detalhes sobre como obter estes valores são na ligação acima. Para registo de aplicações do Azure Active Directory, utilize o ID da aplicação como o ID de cliente.
> 

* Um armazém de dados SQL do Azure. Consulte [criar e a consulta e Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Um Azure Data Lake Store. Consulte [introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Criar uma credencial
Para aceder ao Azure Data Lake Store, terá de criar uma chave mestra de base de dados para encriptar o segredo da credencial utilizado no próximo passo. Em seguida, criar uma credencial de um âmbito de base de dados, que armazena as credenciais de principal de serviço de multimédia no AAD. Os dos que utilizou o PolyBase para ligar ao armazenamento de Blobs do Windows Azure, tenha em atenção que a sintaxe de credencial diferente.

Para ligar ao Azure Data Lake Store, terá **primeiro** criar uma aplicação do Azure Active Directory, crie uma chave de acesso e conceder o acesso de aplicação para o recurso do Azure Data Lake. Para obter instruções, consulte [autenticar para o Data Lake Store através do Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Criar a origem de dados externas
Utilize esta opção [criar origem de dados externa](/sql/t-sql/statements/create-external-data-source-transact-sql) comando para armazenar a localização dos dados. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Configurar o formato de dados
Para importar os dados de ADLS, tem de especificar o formato de ficheiro externo. Este objeto define a forma como os ficheiros são escritos no ADLS.
Para obter a lista completa, observe a nossa documentação de T-SQL [criar formato de ficheiro externo](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Criar as tabelas externas
Agora que tiver especificado o formato de ficheiro e de origem de dados, está pronto para criar as tabelas externas. As tabelas externas são como interagem com dados externos. O parâmetro de localização pode especificar um ficheiro ou diretório. Se especificar um diretório, serão carregados todos os ficheiros dentro do diretório.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerações de tabela externa
Criar uma tabela externa é fácil, mas existem algumas nuances que precisam de ser abordada.

As tabelas externas são do tipo seguro. Isto significa que cada linha de dados que está a ser ingeridos deve satisfazer a definição de esquema de tabela.
Se uma linha não corresponde a definição de esquema, a linha é rejeitada da carga.

As opções de REJECT_TYPE e REJECT_VALUE permitem-lhe definir o número de linhas ou que percentagem dos dados tem de estar presente na tabela final. Durante o carregamento, se o valor de rejeitar for atingido, a carga de falha. A causa mais comum de linhas rejeitadas é um erro de correspondência de definição de esquema. Por exemplo, se uma coluna incorretamente é dado o esquema de int quando os dados no ficheiro são uma cadeia, cada linha irão falhar ao carregar.

 Azure Data Lake store utiliza o controlo de acesso baseado em ' (RBAC) da função para controlar o acesso aos dados. Isto significa que o Principal de serviço tem de ter permissões de leitura para os diretórios definidos no parâmetro de localização e para os subordinados do diretório final e ficheiros. Isto permite que o PolyBase autenticar e carregar os dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados de utilização do Azure Data Lake Store o [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrução. 

CTAS cria uma nova tabela e preenche-lo com os resultados de uma instrução select. CTAS define a tabela de novo para ter a mesma colunas e tipos de dados como os resultados da instrução select. Se selecionar todas as colunas a partir de uma tabela externa, a nova tabela é uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, estamos a criar uma tabela hash de distribuída chamada DimProduct do nosso DimProduct_external de tabela externa.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compressão columnstore
Por predefinição, o SQL Data Warehouse armazena a tabela como um índice columnstore em cluster. Após a conclusão de uma carga, algumas das linhas de dados não podem ser comprimidas para o columnstore.  Há diversos motivos por que motivo esta situação pode ocorrer. Para obter mais informações, consulte [Gerir índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho das consultas e compressão columnstore depois de uma carga, reconstrua a tabela para forçar o índice columnstore para comprimir todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar as estatísticas
É melhor criar estatísticas de coluna única imediatamente após uma carga. Existem algumas opções de estatísticas. Por exemplo, se criar estatísticas de coluna única em cada coluna poderá demorar muito tempo a recriar todas as estatísticas. Se souber que determinadas colunas não vai ser em predicados de consulta, pode ignorar criar estatísticas nessas colunas.

Se optar por criar estatísticas de coluna única em cada coluna de cada tabela, pode utilizar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no [estatísticas](sql-data-warehouse-tables-statistics.md) artigo.

O exemplo seguinte é um ponto de partida para criar estatísticas. Cria estatísticas de coluna única cada coluna na tabela de dimensão e cada coluna joining nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou várias colunas para outras colunas da tabela de factos mais tarde.

## <a name="achievement-unlocked"></a>Achievement desbloqueado!
Carregou com êxito dados para o Azure SQL Data Warehouse. Excelente trabalho!

## <a name="next-steps"></a>Passos Seguintes 
Neste tutorial, criou tabelas externas para definir a estrutura dos dados armazenados no Azure Data Lake Store e, em seguida, utilizar a instrução PolyBase CREATE TABLE AS SELECT para carregar dados para o armazém de dados. 

Fez tudo isto:
> [!div class="checklist"]
> * Objetos de base de dados criada necessários para carregar a partir do Azure Data Lake Store.
> * Ligado a um diretório do Azure Data Lake Store.
> * Dados carregados para o Azure SQL Data Warehouse.
> 

Carregamento de dados é o primeiro passo para desenvolver uma solução de armazém de dados do armazém de dados do SQL Server a utilizar. Consulte a nossa recursos de desenvolvimento.

> [!div class="nextstepaction"]
>[Saiba como desenvolver tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-overview.md)




