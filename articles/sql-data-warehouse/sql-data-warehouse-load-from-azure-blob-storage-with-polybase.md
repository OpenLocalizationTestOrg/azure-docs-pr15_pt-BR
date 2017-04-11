<properties
   pageTitle="Carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Saiba como usar PolyBase para carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse. Carrega algumas tabelas de dados públicos para o esquema de depósito de dados de varejo de Contoso."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse (PolyBase)

> [AZURE.SELECTOR]
- [Fábrica de dados](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Use comandos PolyBase e T-SQL para carregar dados do armazenamento de blob do Microsoft Azure em depósito de dados do SQL Azure. 

Para mantê-lo simples, este tutorial carrega duas tabelas de um público Blob de armazenamento do Azure para o esquema de Contoso varejo Data Warehouse. Para carregar o conjunto completo de dados, execute o exemplo [carregar Contoso varejo Data Warehouse completo][] do repositório exemplos do Microsoft SQL Server.

Neste tutorial, você irá:

1. Configurar PolyBase para carregar do armazenamento de blob do Microsoft Azure
2. Carregar dados públicos em seu banco de dados
3. Realizar otimizações após o carregamento.


## <a name="before-you-begin"></a>Antes de começar
Para executar este tutorial, você precisa de uma conta Azure que já tenha um banco de dados do SQL Data Warehouse. Se você ainda não tiver isso, consulte [criar um depósito de dados do SQL][].

## <a name="1-configure-the-data-source"></a>1. configure a fonte de dados

PolyBase usa objetos externos T-SQL para definir o local e atributos dos dados externos. As definições de objeto externo são armazenadas no depósito de dados do SQL. Os dados em si são armazenados externamente.

### <a name="11-create-a-credential"></a>1.1. Criar uma credencial

**Ignorar esta etapa** se você estiver carregando os dados públicos de Contoso. Você não precisa acesso seguro para os dados públicos porque ela já está acessível para qualquer pessoa.

**Não ignorar esta etapa** se você estiver usando este tutorial como um modelo para carregar seus próprios dados. Para acessar dados por meio de uma credencial, use o seguinte script para criar uma credencial de escopo do banco de dados e usá-lo ao definir o local da fonte de dados.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Vá para a etapa 2.

### <a name="12-create-the-external-data-source"></a>1.2. Criar uma fonte de dados externos

Use este comando [Criar fonte de dados externa][] para armazenar o local dos dados e o tipo de dados. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Se você optar por tornar o azure blob contêineres de armazenamento público, lembre-se de que como o proprietário dos dados que você será cobrado por dados encargos de saída quando os dados saem do data center. 

## <a name="2-configure-data-format"></a>2. configurar o formato de dados

Os dados são armazenados em arquivos de texto em armazenamento de blob do Microsoft Azure e cada campo é separado com um delimitador. Execute este comando [Criar formato de arquivo externo][] para especificar o formato dos dados nos arquivos de texto. Os dados de Contoso são compactados e delimitado por barra vertical.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. criar as tabelas externas

Agora que você especificou o formato de arquivo e de fonte de dados, você está pronto para criar as tabelas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Crie um esquema para os dados. 

Para criar um local para armazenar os dados de Contoso no banco de dados, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3,2. Crie tabelas externas. 

Execute esse script para criar as tabelas externas DimProduct e FactOnlineSales. Todos estamos aqui é definir nomes de coluna e tipos de dados e vinculá-los ao local e o formato dos arquivos de armazenamento de blob do Microsoft Azure. A definição é armazenada no depósito de dados do SQL e os dados ainda estão sendo o Blob de armazenamento do Azure.

O parâmetro **local** é a pasta sob a pasta raiz no Azure armazenamento de Blob. Cada tabela é em uma pasta diferente.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. carregar os dados
Há diferentes maneiras de acessar dados externos.  Você pode consultar dados diretamente da tabela de dados externo, carregue os dados em tabelas de banco de dados novo ou adicionar dados externos a tabelas de banco de dados existente.  


### <a name="41-create-a-new-schema"></a>4.1. Criar um novo esquema

CTAS cria uma nova tabela que contenha dados.  Primeiro, crie um esquema para os dados de contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Carregar os dados em novas tabelas

Para carregar dados do armazenamento de blob do Microsoft Azure e salvá-lo em uma tabela dentro de seu banco de dados, use a instrução [Criar tabela como selecionar (Transact-SQL)][] . Carregar CTAS utiliza as tabelas externas acentuadas que você acabou de criar. Para carregar os dados para novas tabelas, use uma instrução de [CTAS][] por tabela. 

CTAS cria uma nova tabela e preenche com os resultados de uma instrução select. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados como os resultados da instrução select. Se você selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e tipos de dados na tabela de dados externo.

Neste exemplo, podemos criar a dimensão e a tabela de fatos conforme distribuídas tabelas de hash. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 acompanhar o andamento de carga

Você pode controlar o andamento do seu carregamento usando gerenciamento dinâmico DMVs (exibições). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. otimizar columnstore compactação

Por padrão, o SQL Data Warehouse armazena a tabela como um índice de columnstore agrupada. Após a conclusão de uma carga, algumas das linhas de dados não podem ser compactadas no columnstore.  Há vários motivos por que isso pode acontecer. Para saber mais, consulte [Gerenciar columnstore índices][].

Para otimizar o desempenho da consulta e compactação columnstore após uma carga, recrie a tabela para forçar o índice columnstore para compactar todas as linhas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obter mais informações sobre como manter columnstore índices, consulte o artigo [Gerenciar columnstore índices][] .

## <a name="6-optimize-statistics"></a>6. otimizar estatísticas

É melhor criar estatísticas de coluna única imediatamente após uma carga. Há algumas opções para estatísticas. Por exemplo, se você criar estatísticas de coluna única em cada coluna ele pode levar muito tempo para reconstruir todas as estatísticas. Se você souber que determinadas colunas não serão em predicados de consulta, você pode pular estatísticas criando nessas colunas.

Se você decidir criar estatísticas de coluna única em cada coluna de cada tabela, você pode usar a amostra de código de procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas][] .

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensão e em cada coluna ingresso em tabelas de fatos. Você sempre pode adicionar posteriormente estatísticas únicas ou várias colunas a outras colunas da tabela de fatos.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Conquista desbloqueada!

Com êxito, você carregou dados públicos no depósito de dados do SQL Azure. Trabalho excelente!

Agora você pode começar consultando as tabelas usando consultas semelhante ao seguinte:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Próximas etapas
Para carregar os dados de Contoso varejo Data Warehouse completos, use o script para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Criar um depósito de dados do SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Visão geral de desenvolvimento de depósito de dados do SQL]: sql-data-warehouse-overview-develop.md
[Gerenciar índices de columnstore]: sql-data-warehouse-tables-index.md
[Estatísticas]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CRIAR FONTE DE DADOS EXTERNOS]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CRIAR UM FORMATO DE ARQUIVO EXTERNO]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[Criar tabela como selecionar (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Carregar o Data Warehouse completo do Contoso varejo]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
