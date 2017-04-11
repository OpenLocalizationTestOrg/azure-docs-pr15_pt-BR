<properties
   pageTitle="Criar tabela como selecionar (CTAS) no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para codificação com a tabela de criar como instrução select (CTAS) depósito de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Criar tabela como selecionar (CTAS) no depósito de dados do SQL
Criar tabela como selecionar ou `CTAS` é um dos recursos mais importantes T-SQL disponíveis. É uma operação totalmente paralelizada que cria uma nova tabela com base na saída de uma instrução SELECT. `CTAS`é a maneira mais rápida e simples para criar uma cópia de uma tabela. Você pode ser considerada uma versão de teste de sobrecarga do `SELECT..INTO` se você quiser. Este documento fornece exemplos e as práticas recomendadas para `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Usando CTAS para copiar uma tabela

Talvez um dos mais comuns usa de `CTAS` está criando uma cópia de uma tabela para que você possa alterar DDL. Se por exemplo originalmente criado sua tabela como `ROUND_ROBIN` e agora desejar alterá-la a uma tabela distribuída em uma coluna, `CTAS` como você deseja alterar a coluna de distribuição. `CTAS`também podem ser usados para alterar os tipos de partição, indexação ou coluna.

Digamos que você criou essa tabela usando o tipo de distribuição padrão de `ROUND_ROBIN` distribuído desde que nenhuma coluna de distribuição especificada na `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Agora que você deseja criar uma nova cópia desta tabela com um índice de Columnstore agrupada, para que você possa aproveitar o desempenho de tabelas Columnstore agrupada. Você também deseja distribuir dessa tabela na ProductKey desde que você está antecipar junções nessa coluna e deseja evitar a movimentação de dados durante junções em ProductKey. Finalmente você também queira adicionar partição em OrderDateKey para que você pode excluir rapidamente dados antigos soltando partições antigas. Eis a instrução CTAS que copie sua tabela antiga para uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Finalmente você pode renomear tabelas para trocar em sua nova tabela e, em seguida, solte a sua tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Depósito de dados SQL Azure faz ainda não suporte automático criar ou automático estatísticas de atualização.  Para obter o melhor desempenho de suas consultas, é importante que estatísticas de ser criada sobre todas as colunas de todas as tabelas após o carregamento primeiro ou quaisquer alterações substanciais ocorrerem nos dados.  Para obter uma explicação detalhada das estatísticas, consulte o tópico de [estatísticas][] no grupo desenvolver de tópicos.

## <a name="using-ctas-to-work-around-unsupported-features"></a>Usando CTAS para trabalhar com recursos sem suporte

`CTAS`também podem ser usados para trabalhar em torno de um número dos recursos sem suporte listados abaixo. Isso geralmente pode provar ao ser uma situação de ganhos/win como não só será compatível com seu código, mas ele geralmente será executado com mais rapidez em SQL Data Warehouse. Isso é como resultado de seu design totalmente paralelizado. Cenários que podem ser contornados com CTAS incluem:

- SELECIONE.. EM
- JUNÇÕES ANSI atualizações
- Junções ANSI em exclusões
- Mesclar instrução

> [AZURE.NOTE] Tente imaginar "CTAS primeiro". Se você acha que você pode resolver um problema usando `CTAS` , em seguida, geralmente é a melhor maneira de abordá-lo - mesmo se você estiver escrevendo mais dados como resultado.
>

## <a name="selectinto"></a>SELECIONE.. EM
Você pode encontrar `SELECT..INTO` aparece em um número de casas em sua solução.

Abaixo está um exemplo de um `SELECT..INTO` instrução:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Para converter acima para `CTAS` é bastante direta:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS atualmente requer que uma coluna de distribuição ser especificado.  Se você não estiver tentando intencionalmente para alterar a coluna de distribuição, seu `CTAS` executará mais rapidamente se você selecionar uma coluna de distribuição que é a mesma que a tabela subjacente como essa estratégia evita movimentação de dados.  Se você estiver criando uma pequena tabela onde o desempenho não é um fator, em seguida, você pode especificar `ROUND_ROBIN` para evitar a decidir em uma coluna de distribuição.

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de junção de ANSI para instruções de atualização

Você descobrirá que você tem uma atualização complexa que associa mais de duas tabelas em conjunto usando o ANSI ingressando em sintaxe para executar a atualização ou exclusão.

Imagine que você tinha que atualizar esta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

A consulta original pode ter parecido com isto:

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Como o SQL Data Warehouse não oferece suporte ANSI associa a `FROM` cláusula de um `UPDATE` instrução, você não pode copiar este código sobre sem alterá-lo um pouco.

Você pode usar uma combinação de um `CTAS` e um join implícito para substituir este código:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Substituição de junção de ANSI para excluir demonstrativos
Às vezes, a melhor abordagem para exclusão de dados é usar `CTAS`. Em vez de excluir os dados simplesmente selecione os dados que você deseja manter. Este especialmente verdadeiro para `DELETE` instruções que usam ansi ingressando em sintaxe como SQL Data Warehouse não oferece suporte a ANSI associa a `FROM` cláusula de um `DELETE` instrução.

Um exemplo de uma instrução DELETE convertido está disponível abaixo:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Substituir instruções de mesclagem
Instruções de mesclagem podem ser substituídas, pelo menos parte, usando `CTAS`. Você pode consolidar os `INSERT` e o `UPDATE` em uma única instrução. Quaisquer registros excluídos precisarão ser fechadas em uma segunda instrução.

Um exemplo de um `UPSERT` disponível abaixo:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recomendação de CTAS: declarar explicitamente o tipo de dados e nulabilidade de saída

Quando migrar código talvez descubra que você encontrar esse tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instintivamente você imagina você deve migrar este código para um CTAS e você está correto. No entanto, há um problema de oculto aqui.

O seguinte código não produz o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Observe que a coluna "resultado" encaminhar carrega os valores de tipo e nulabilidade de dados da expressão. Se você não tiver cuidado, isso pode levar a variações sutis em valores.

Tente o seguinte exemplo:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

O valor armazenado para o resultado é diferente. Como o valor persistente na coluna resultado é usado em outras expressões o erro se torna ainda mais significativo.

![][1]

Isso é particularmente importante para migrações de dados. Apesar da segunda consulta é possivelmente mais precisa há um problema. Os dados seriam diferentes em comparação com o sistema de origem e que leva para as perguntas de integridade na migração. Essa é uma nesses casos raros onde a resposta "errada" é realmente o correto!

O motivo, podemos ver esse disparidade entre dois resultados é para baixo até a conversão de tipo implícito. No primeiro exemplo a tabela define a definição de coluna. Quando a linha é inserida ocorre uma conversão de tipo implícito. No segundo exemplo não há nenhuma conversão de tipo implícito como a expressão define o tipo de dados da coluna. Observe também que a coluna no segundo exemplo definida como uma coluna anulável enquanto no primeiro exemplo não tem. Quando a tabela foi criada na primeira nulabilidade de coluna de exemplo explicitamente foi definida. O segundo exemplo apenas estava à expressão e por padrão isso resultaria em uma definição de nulo.  

Resolver esses problemas que você deve definir explicitamente a conversão de tipo e nulabilidade no `SELECT` parte do `CTAS` instrução. Você não pode definir essas propriedades na parte de criar tabela.

O exemplo a seguir demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observe o seguinte:
- CAST ou CONVERT poderia ter sido usado
- ISNULL é usado para forçar nulabilidade não ADESÃO
- ISNULL é a função externa
- A segunda parte do ISNULL é uma constante isto é 0

> [AZURE.NOTE] Para a condição de nulidade corretamente ser configurá-é vital usar `ISNULL` e não `COALESCE`. `COALESCE`não é uma função determinante e então o resultado da expressão sempre será NULLable. `ISNULL`é diferente. É determinante. Portanto, quando a segunda parte do `ISNULL` função é uma constante ou um literal, o valor resultante será não nulo.

Esta dica não é apenas útil para assegurar a integridade dos seus cálculos. Também é importante para alternar de partição de tabela. Imagine que você tenha essa tabela definida como seu fato:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

No entanto, o campo de valor é uma expressão calculada não é parte dos dados de origem.

Para criar seu conjunto de dados particionado, que talvez você queira fazer isso:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

A consulta seria executado perfeitamente bem. O problema surge quando você tenta realizar a mudança de partição. As definições da tabela não correspondem. Para tornar as definições da tabela correspondem a CTAS precisa ser modificada.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Você pode ver, portanto, que tipo consistência e manter propriedades nulabilidade em um CTAS é uma boa prática recomendada engenharia. Ele ajuda a manter a integridade em seus cálculos e também garante que a mudança de partição é possível.

Consulte MSDN para obter mais informações sobre como usar o [CTAS][]. É uma das instruções mais importantes no armazém de dados do SQL Azure. Certifique-se de que entender totalmente.

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
