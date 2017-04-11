<properties
   pageTitle="Visão geral das tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Guia de Introdução com tabelas de depósito de dados do Azure SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Visão geral das tabelas no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

Introdução à criação de tabelas no SQL Data Warehouse é simple.  A sintaxe de [Criar tabela][] básica segue a sintaxe comum provavelmente já familiarizados com de como trabalhar com outros bancos de dados.  Para criar uma tabela, basta nomear sua tabela, nomeie as colunas e definir tipos de dados para cada coluna.  Se você tiver criar tabelas em outros bancos de dados, isso deve parecer muito familiar para você.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

O exemplo acima cria uma tabela chamada Customers com duas colunas, nome e sobrenome.  Cada coluna é definida com um tipo de dados de varchar (25), o que limita os dados a serem 25 caracteres.  Esses atributos fundamentais de uma tabela, bem como outras pessoas, principalmente são iguais a outros bancos de dados.  Tipos de dados são definidos para cada coluna e garantem a integridade dos dados.  Os índices podem ser adicionados para melhorar o desempenho, reduzindo e/s.  Partição pode ser adicionado para melhorar o desempenho quando você precisa modificar dados.

[Renomeando] [ RENAME] uma tabela do SQL Data Warehouse esta aparência:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tabelas distribuídas

Um novo atributo fundamental introduzido por sistemas distribuídos como SQL Data Warehouse é a **coluna de distribuição**.  A coluna de distribuição é muito o que parece.  É a coluna que determina como distribuir ou divida, os dados nos bastidores.  Quando você cria uma tabela sem especificar a coluna de distribuição, a tabela é distribuída automaticamente usando o **round robin**.  Enquanto as tabelas de repetição alternada podem ser suficientes em alguns cenários, definir colunas de distribuição pode reduzir significativamente a movimentação de dados durante as consultas, portanto, otimizando o desempenho.  Consulte [Distribuindo uma tabela][Distribuir] para saber mais sobre como selecionar uma coluna de distribuição.

## <a name="indexing-and-partitioning-tables"></a>Indexação e partição tabelas

Como você se tornam mais avançados usando SQL Data Warehouse e desejar otimizar o desempenho, você desejará saber mais sobre o Design da tabela.  Para saber mais, consulte os artigos sobre [Tipos de dados de tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [uma tabela de indexação][índice] e[partição]de [partição de uma tabela].

## <a name="table-statistics"></a>Estatísticas de tabela

Estatísticas estão um fundamental para obter o melhor desempenho fora seu depósito de dados do SQL.  Desde que o SQL Data Warehouse não ainda cria automaticamente e estatísticas de atualização para você, como você pode ter vir esperar no Azure SQL Database, nosso artigo sobre [estatísticas][] de leitura podem ser um dos artigos mais importantes que você leia para garantir que você obtenha o melhor desempenho de suas consultas.

## <a name="temporary-tables"></a>Tabelas temporárias

Tabelas temporárias são tabelas que só existem durante a de seu logon e não podem ser vistas por outros usuários.  Tabelas temporárias podem ser uma boa maneira de impedir que outras pessoas vejam os resultados temporários e também reduzir a necessidade de limpeza.  Como tabelas temporárias também utilizam armazenamento local, eles podem oferecer desempenho mais rápido para algumas operações.  Consulte os artigos de [Tabela temporária][temporário] para obter mais detalhes sobre tabelas temporárias.

## <a name="external-tables"></a>Tabelas externas

Tabelas externas, também conhecido como Polybase tabelas, são tabelas que podem ser consultadas de depósito de dados do SQL, mas ponto a dados externos de depósito de dados do SQL.  Por exemplo, você pode criar uma tabela externa que aponta para arquivos em armazenamento de Blob do Azure.  Para obter mais detalhes sobre como criar e consultar uma tabela externa, consulte [carregar dados com Polybase][].  

## <a name="unsupported-table-features"></a>Recursos de tabela sem suporte

Embora o SQL Data Warehouse contiver muitos dos mesmos recursos de tabela oferecidos por outros bancos de dados, há alguns recursos que ainda não são aceitas.  Abaixo está uma lista de alguns dos recursos de tabela que ainda não são aceitas.

| Recursos sem suporte |
| --- |
|[Propriedade de identidade][] (consulte a [Solução alternativa de chave substitutas atribuindo][])|
|Chave primária, chaves estrangeiras, exclusivos e verificação de [Restrições de tabela][]|
|[Índices exclusivos][]|
|[Colunas calculadas][]|
|[Colunas esparsas][]|
|[Tipos definidos pelo usuário][]|
|[Sequência][]|
|[Disparadores][]|
|[Modos de exibição indexados][]|
|[Sinônimos][]|

## <a name="table-size-queries"></a>Consultas de tamanho de tabela

Uma forma simples para identificar linhas consumidas por uma tabela em cada uma das 60 distribuições e espaço é usar [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, usando os comandos DBCC pode ser bastante limitação.  Exibições de gerenciamento dinâmico (DMVs) permitirá que você veja muito mais detalhes, bem como oferece controle muito maior sobre os resultados da consulta.  Comece criando este modo de exibição, que será referido por muitos dos nossos exemplos neste e em outros artigos.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Resumo do espaço de tabela

Esta consulta retorna linhas e espaço por tabela.  É uma ótima consulta para ver quais tabelas são suas tabelas maiores e se eles são round robin ou hash distribuído.  Para tabelas de hash distribuído também mostra a coluna de distribuição.  Na maioria dos casos suas tabelas maiores devem ser hash distribuído com um índice de columnstore agrupada.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espaço de tabela por tipo de distribuição

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espaço de tabela por tipo de índice

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Resumo de espaço de distribuição

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os artigos em [Tipos de dados de tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [uma tabela de indexação][índice], [partição uma tabela][partição], [Estatísticas de tabela de manutenção de][estatísticas] e [Tabelas temporárias][temporário].  Para obter mais informações sobre as práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

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
[Carregar os dados com Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CRIAR TABELA]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propriedade de identidade]: https://msdn.microsoft.com/library/ms186775.aspx
[Solução alternativa de chaves substitutas de atribuição]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Restrições de tabela]: https://msdn.microsoft.com/library/ms188066.aspx
[Colunas calculadas]: https://msdn.microsoft.com/library/ms186241.aspx
[Colunas esparsas]: https://msdn.microsoft.com/library/cc280604.aspx
[Tipos definidos pelo usuário]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequência]: https://msdn.microsoft.com/library/ff878091.aspx
[Disparadores]: https://msdn.microsoft.com/library/ms189799.aspx
[Modos de exibição indexados]: https://msdn.microsoft.com/library/ms191432.aspx
[Sinônimos]: https://msdn.microsoft.com/library/ms177544.aspx
[Índices exclusivos]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
