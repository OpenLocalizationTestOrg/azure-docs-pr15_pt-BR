<properties
   pageTitle="Tabelas temporárias em SQL Data Warehouse | Microsoft Azure"
   description="Primeiros passos com tabelas temporárias no armazém de dados do SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias em SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

Tabelas temporárias são muito útil ao processar dados - especialmente durante transformação onde os resultados intermediários são temporárias. Em SQL Data Warehouse tabelas temporárias existem no nível da sessão.  Eles são visíveis apenas para a sessão em que foram criados e automaticamente são descartados quando sessão faz logoff.  Tabelas temporárias oferecem um benefício de desempenho, pois os resultados são gravados local em vez de armazenamento remoto.  Tabelas temporárias são um pouco diferentes no depósito de dados do Azure SQL Azure SQL Database como eles podem ser acessados de qualquer lugar dentro a sessão, incluindo dentro e fora de um procedimento armazenado.

Este artigo contém orientações essenciais para usar tabelas temporárias e realça os princípios de tabelas temporárias nível de sessão. Usando as informações neste artigo pode ajudá-lo a modularizar seu código, melhorando reutilização e facilidade de manutenção do seu código.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária

Tabelas temporárias são criadas por simplesmente prefixando seu nome de tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

Tabelas temporárias também podem ser criadas com um `CTAS` usando exatamente a mesma abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`é um comando poderoso e tem a vantagem de ser muito eficiente em seu uso de espaço de log de transação. 


## <a name="dropping-temporary-tables"></a>Descartar as tabelas temporárias

Quando uma nova sessão é criada, não devem existir nenhum tabelas temporárias.  No entanto, se você estiver chamando o mesmo procedimento armazenado, que cria um temporário com o mesmo nome, para garantir que seu `CREATE TABLE` instruções são bem sucedidas uma verificação da pré-existência de simple com um `DROP` podem ser usados como no exemplo abaixo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para codificar consistência, é recomendável usar esse padrão para tabelas e tabelas temporárias.  Também é uma boa ideia usar `DROP TABLE` remover tabelas temporárias quando terminar com eles em seu código.  No procedimento armazenado desenvolvimento é bastante comum para ver os comandos de soltar reunidas no final de um procedimento para garantir que esses objetos são eliminadas.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizar código

Como tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário, isso pode ser explorado para ajudá-lo a modularizar código do seu aplicativo.  Por exemplo, o procedimento armazenado abaixo reúne as práticas recomendadas do acima para gerar DDL que irá atualizar todas as estatísticas no banco de dados por nome de estatística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesse estágio a única ação que ocorreu é a criação de um procedimento armazenado que será simplesmente gerado uma tabela temporária, #stats_ddl, com instruções DDL.  Esse procedimento armazenado será solte #stats_ddl se ele já existir para garantir que ela não falhará se executar mais de uma vez dentro de uma sessão.  No entanto, como não há nenhuma `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, ele sairá na tabela criada para que ele possa ser lido fora do procedimento armazenado.  Em SQL Data Warehouse, ao contrário de outros bancos de dados do SQL Server, é possível usar a tabela temporária fora o procedimento que o criou.  Tabelas temporárias SQL Data Warehouse podem ser usado **em qualquer lugar** dentro da sessão. Isso pode levar a mais de código modular e gerenciável como no exemplo abaixo:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações de tabela temporária

SQL Data Warehouse impõe algumas limitações ao implementar tabelas temporárias.  Atualmente, apenas sessão supervisionadas tabelas temporárias são suportadas.  Tabelas temporárias globais não são suportadas.  Além disso, modos de exibição não podem ser criados em tabelas temporárias.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os artigos no [Tabela visão geral][Visão geral]de [Tipos de dados de tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [indexação de uma tabela de][índice], [partição de uma tabela de][partição] e de [Estatísticas de tabela de manutenção de][estatísticas].  Para obter mais informações sobre as práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
