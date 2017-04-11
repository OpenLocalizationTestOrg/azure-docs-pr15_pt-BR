<properties
   pageTitle="Partição tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Primeiros passos com tabela partição no armazém de dados do SQL Azure."
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
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Tabelas de partição no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

Partição é suportado em todos os tipos de tabela do SQL Data Warehouse; incluindo columnstore agrupada, índice agrupado e pilha.  Também há suporte para a partição em todos os tipos de distribuição, incluindo hash ou round robin distribuído.  Dividir seus dados em grupos menores de dados e na maioria dos casos, partição partição ativa é feita em uma coluna de data.

## <a name="benefits-of-partitioning"></a>Benefícios de partição

Partição vantagem desempenho de consulta e a manutenção de dados.  Se ele benefícios ambos ou apenas uma depende como os dados são carregados e se a mesma coluna pode ser usada para ambos os fins, desde que partição só pode ser feito em uma coluna.

### <a name="benefits-to-loads"></a>Benefícios aos carregamentos

O principal benefício da partição em SQL Data Warehouse é melhorar a eficiência e o desempenho de carregamento por uso dos dados de exclusão de partição, troca e mesclagem.  Na maioria dos casos os dados são particionados em uma coluna de data que é intimamente vinculada à sequência de que os dados são carregados no banco de dados.  Um dos maiores benefícios de usar partições para manter dados-evitar log de transação.  Enquanto simplesmente inserindo, atualizando ou excluam dados pode ser a abordagem mais simples, com um pouco de pensamento e esforço, usando a partição durante o processo de carregamento pode melhorar substancialmente o desempenho.

Migrando de partição pode ser usado para remover ou substituir uma seção de uma tabela rapidamente.  Por exemplo, uma tabela de fatos de vendas pode conter dados apenas para os últimos 36 meses.  No final de cada mês, mês mais antigo de dados de vendas é excluído da tabela.  Estes dados podem ser excluídos, usando uma instrução delete para excluir os dados para o mês mais antigo.  No entanto, excluir uma grande quantidade de linha por linha de dados com uma instrução delete pode levar muito tempo, bem como criar o risco de transações grandes que poderia levar muito tempo para reversão se algo der errado.  Uma melhor abordagem é simplesmente descartar a mais antigo partição de dados.  Onde a exclusão de linhas individuais pode levar horas, excluir toda uma partição pode levar segundos.

### <a name="benefits-to-queries"></a>Benefícios às consultas

Partição também pode ser usado para melhorar o desempenho de consulta.  Se uma consulta se aplica um filtro em uma coluna dividida, isso pode limitar a imagem digitalizada para somente as partições qualificadas que pode ser um subconjunto menor dos dados, evitando uma verificação de tabela inteira.  Com a introdução de índices columnstore agrupada, os benefícios de desempenho de eliminação predicado são menos útil, mas em alguns casos pode haver um benefício às consultas.  Por exemplo, se a tabela de fatos de vendas é particionada em 36 meses usando o campo de data de venda, consultas filtro na data de venda podem ignorar pesquisando em partições que não corresponderem ao filtro.

## <a name="partition-sizing-guidance"></a>Orientações de dimensionamento de partição

Enquanto partição pode ser usada para melhorar o desempenho alguns cenários, criando uma tabela com **muitos** partições pode afetar o desempenho em algumas circunstâncias.  Essas questões estão especialmente verdadeiros para tabelas columnstore agrupada.  Para a partição para ser útil, é importante entender quando usar partição e o número de partições para criar.  Não há nenhuma regra rápida rígida como para quantas partições são muitos, isso depende seus dados e quantas partições você estão carregando simultaneamente.  Mas como um regra geral, pense da adição 10s para 100s das partições, não 1000s.

Ao criar partição nas tabelas de **columnstore agrupada** , é importante considerar quantas linhas ficará em cada partição.  Para compactação ideal e desempenho de tabelas columnstore agrupada, um mínimo de 1 milhão de linhas por distribuição e partição é necessária.  Antes de partições são criadas, o SQL Data Warehouse já divide cada tabela em 60 bancos de dados distribuídos.  Qualquer partição adicionada a uma tabela é além as distribuições criadas nos bastidores.  Usando neste exemplo, se a tabela de fatos de vendas continha 36 partições mensais e considerando que o SQL Data Warehouse tem 60 distribuições, em seguida, a tabela de fatos de vendas deve conter 60 milhões de linhas por mês ou linhas de 2,1 bilhões quando forem preenchidos todos os meses.  Se uma tabela contiver significativamente menos linhas do que o número mínimo recomendado de linhas por partição, considere usar partições menos para fazer aumenta o número de linhas por partição.  Consulte também o artigo de[índice] de [indexação]que inclui consultas que podem ser executadas em SQL Data Warehouse para avaliar a qualidade de índices de columnstore cluster.

## <a name="syntax-difference-from-sql-server"></a>Diferença de sintaxe do SQL Server

SQL Data Warehouse introduz uma definição simplificada de partições que é um pouco diferente do SQL Server.  Funções e esquemas de partição não são usadas em SQL Data Warehouse como eles estão no SQL Server.  Em vez disso, tudo o que você precisa fazer é identificar coluna dividida e os pontos de limite.  A sintaxe de partição pode ser um pouco diferente do SQL Server, os conceitos básicos são os mesmos.  Uma coluna de partição por tabela, que pode ser partição de intervalo de suporte do SQL Server e SQL Data Warehouse.  Para saber mais sobre partição, consulte [tabelas particionadas e índices][].

O exemplo abaixo de uma instrução SQL Data Warehouse particionado [criar tabela][] , partições a tabela de FactInternetSales na coluna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrando partição do SQL Server

Migrar definições de partição do SQL Server para SQL Data Warehouse simplesmente:

- Elimine o [esquema de partição][]do SQL Server.
- Adicione a definição de [função partition][] à tabela criar.

Se você estiver migrando uma tabela particionada de uma instância do SQL Server o abaixo SQL podem ajudar a investigar o número de linhas em cada partição.  Tenha em mente que, se o mesmo detalhamento partição é usado no SQL Data Warehouse, o número de linhas por partição diminuirá por um fator de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gerenciamento de carga de trabalho

Uma consideração de peça final incluir para a decisão de partição de tabela é o [gerenciamento de carga de trabalho][].  Gerenciamento de carga de trabalho em SQL Data Warehouse é basicamente o gerenciamento de memória e concorrência.  No SQL Data Warehouse a memória máxima alocada para cada distribuição durante a execução da consulta é classes de recursos controlados.  Ideal suas partições serão dimensionadas em relação aos outros fatores como as necessidades de memória da criação de índices columnstore agrupada.  Agrupadas columnstore índices benefício bastante quando eles estão alocados mais memória.  Portanto, você desejará garantir que não é fiquem sem recompilação de índice uma partição de memória. Aumentar a quantidade de memória disponível para sua consulta pode ser obtido alternando da função padrão, smallrc, para uma das outras funções como largerc.

Informações sobre a alocação de memória por distribuição estão disponíveis consultando os modos de exibição de gerenciamento dinâmico de Gerenciador de recursos. Na realidade sua concessão de memória será menor que figuras abaixo. No entanto, isso fornece um nível de orientação que você pode usar quando as partições para operações de gerenciamento de dados de dimensionamento.  Tente evitar sua partições além a concessão de memória fornecido pela classe extra grande recurso de dimensionamento. Se as partições ultrapassar esta figura você correrá o risco de pressão de memória que por sua vez leva a menos compactação ideal.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Migrando de partição

SQL Data Warehouse suporta partição de divisão, mesclagem e migrando. Cada uma dessas funções é excuted usando a instrução [ALTER TABLE][] .

Para alternar partições entre duas tabelas, você deve garantir que as partições Alinhar em seus respectivos limites e que as definições da tabela correspondem. Como as restrições de seleção não estão disponíveis para impor o intervalo de valores em uma tabela a tabela de origem deve conter os mesmos limites de partição como a tabela de destino. Se esse não for o caso, a opção de partição falhará como os metadados de partição não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados

O método mais eficiente para dividir uma partição que já contenha dados é usar um `CTAS` instrução. Se a tabela particionada é um columnstore agrupada, em seguida, a partição de tabela deve estar vazia antes que ela pode ser dividida.

Abaixo está um exemplo de tabela particionada columnstore contendo uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Criando o objeto de estatística, garantimos que metadados de tabela são mais precisos. Se nós omitir criando estatísticas, SQL Data Warehouse usará os valores padrão. Para obter detalhes sobre estatísticas revise [estatísticas][].

Podemos então pode consultar a contagem de linha usando a `sys.partitions` modo de exibição de catálogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se tentamos dividir essa tabela, podemos receberá um erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, nível de 15, estado 1, linha 44 DIVIDIDA cláusula da instrução ALTER partição falha porque a partição não está vazia.  Somente partições vazias podem ser divididas em quando um índice columnstore existe na tabela. Considere desabilitar o índice columnstore antes a instrução ALTER partição de emissão, em seguida, recriar o índice columnstore após alterar partição é concluída.

No entanto, podemos usar `CTAS` para criar uma nova tabela para armazenar nossos dados.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Como os limites de partição são alinhados uma opção é permitida. Isso deixará a tabela de origem com uma partição vazia que podemos subsequentemente dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que falta para fazer é para alinhar nossos dados para os novos limites de partição usando `CTAS` e alterne nossos dados de volta para a tabela principal

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Quando tiver concluído a movimentação dos dados é uma boa ideia atualizar as estatísticas na tabela de destino para garantir que eles precisamente refletem a nova distribuição dos dados em suas respectivas partições:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabela de controle de origem

Para evitar a sua definição de tabela de **enferrujado** no seu sistema de controle de origem, convém considerar a abordagem a seguir:

1. Criar a tabela como uma tabela particionada, mas sem valores de partição

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`a tabela como parte do processo de implantação:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Com essa abordagem o código no controle de origem permanece estático e os valores de limite partição são permitidos para ser dinâmico; Desenvolvendo com o depósito ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os artigos sobre [Tabela visão geral][Visão geral], [Tipos de dados de tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [indexação de uma tabela de][índice], [Mantendo estatísticas de tabela][estatísticas] e [Tabelas temporárias][temporário].  Para obter mais informações sobre as práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[gerenciamento de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Índices e tabelas particionadas]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTERAR TABELA]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CRIAR TABELA]: https://msdn.microsoft.com/library/mt203953.aspx
[Função Partition]: https://msdn.microsoft.com/library/ms187802.aspx
[esquema de partição]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
