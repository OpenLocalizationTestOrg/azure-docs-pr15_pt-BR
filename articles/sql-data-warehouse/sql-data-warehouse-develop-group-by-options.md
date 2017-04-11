<properties
   pageTitle="Agrupar por opções em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar o grupo de opções no armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Agrupar por opções no depósito de dados do SQL

Cláusula [GROUP BY][] é usado para agregar dados a um conjunto de linhas de resumo. Ele também tem algumas opções que estender sua funcionalidade que precisam ser contornado como elas não são suportadas diretamente por depósito de dados do SQL Azure.

Essas opções estão
- Agrupar por com ROLLUP
- CONJUNTOS DE AGRUPAMENTO
- Agrupar por cubo

## <a name="rollup-and-grouping-sets-options"></a>Opções de conjuntos de Rollup e agrupamento
A opção mais simples aqui é usar `UNION ALL` em vez disso, para executar o acúmulo em vez de depender de sintaxe explícita. O resultado é exatamente o mesmo

Abaixo está um exemplo de um grupo usando instrução a `ROLLUP` opção:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Usando ROLLUP solicitamos as agregações a seguintes:
- País e região
- País/região
- Total geral

Para substituir isso você precisará usar `UNION ALL`; especificar as agregações necessário explicitamente para retornar os mesmos resultados:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para conjuntos de AGRUPAMENTO tudo o que precisa fazer é adotar o mesmo principal, mas apenas criar seções UNION ALL para os níveis de agregação que queremos ver

## <a name="cube-options"></a>Opções de cubo
É possível criar um grupo por com cubo usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicados e difíceis. Para atenuar isso, você pode usar essa mais avançada abordagem.

Vamos usar o exemplo acima.

A primeira etapa é definir o cubo' ' que define todos os níveis de agregação que queremos criar. É importante Anote a associação de referência cruzada das duas tabelas derivadas. Isso gera todos os níveis para nós. O restante do código existe realmente para formatação.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Os resultados dos CTAS podem ser vistos abaixo:

![][1]

A segunda etapa é especificar uma tabela de destino para armazenar provisórios resultados:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

A terceira etapa é para loop sobre nosso cubo de realizar a agregação de colunas. A consulta será executado uma vez para cada linha na tabela temporária #Cube e armazenar os resultados da tabela temporária #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Finalmente podemos pode retornar os resultados, simplesmente lendo da tabela temporária #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Dividir o código em seções e gerando uma construção loop o código se torna mais gerenciável e manutenção.


## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[AGRUPAR POR]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
