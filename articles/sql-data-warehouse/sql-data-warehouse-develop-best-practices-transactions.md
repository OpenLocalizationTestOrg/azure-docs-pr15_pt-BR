<properties
   pageTitle="Otimizando transações para SQL Data Warehouse | Microsoft Azure"
   description="Diretrizes de práticas recomendadas sobre como escrever atualizações de transação eficiente no armazém de dados do SQL Azure"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Otimizando transações para depósito de dados do SQL

Este artigo explica como otimizar o desempenho do seu código transações minimizando o risco de reversões longas.

## <a name="transactions-and-logging"></a>Transações e registro em log

Transações são um componente importante de um mecanismo de banco de dados relacional. SQL Data Warehouse usa transações durante a modificação de dados. Estas transações podem ser explícitas ou implícitas. Único `INSERT`, `UPDATE` e `DELETE` instruções são exemplos de transações implícitos. Transações explícitas são gravadas explicitamente por um desenvolvedor usando `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` e geralmente são usadas quando várias instruções de modificação precisam ser ligados juntos em uma única unidade atômica. 

Depósito de dados SQL Azure confirma alterações no banco de dados usando os logs de transação. Cada distribuição tem seu próprio log de transação. Gravações do log de transação são automáticas. Não há nenhuma configuração necessária. No entanto, durante este processo garante a gravação ele introduzir uma sobrecarga no sistema. Você pode minimizar esse impacto escrevendo código transacionalmente eficiente. Código transacionalmente eficiente amplamente cai em duas categorias.

- Aproveitar construções de log mínimo onde possível
- Dados de processo usando lotes supervisionadas para evitar singulares transações longa duração
- Adotar uma partição migrando padrão para grandes modificações para uma determinada partição

## <a name="minimal-vs-full-logging"></a>Mínimo versus log completo

Ao contrário de operações totalmente registradas, o que usam o log de transação para controlar a cada alteração de linha, operações registradas de forma mínima controlar as alocações de extensão e somente as alterações de metadados. Portanto, o log mínimo envolve log apenas as informações que é necessárias para reverter a transação em caso de uma falha ou uma solicitação explícita (`ROLLBACK TRAN`). Como muito menos informações são rastreadas no log de transação, uma operação de registradas de forma mínima executa melhor do que uma operação de totalmente registrada da mesma forma tamanho. Além disso, como gravações menos ficar o log de transação, uma quantidade muito menor de dados de log é gerada e portanto é mais i / eficiente.

Os limites de segurança de transação se aplicam apenas às operações totalmente registradas.

>[AZURE.NOTE] Operações registradas de forma mínima podem participar de transações explícitas. Como todas as alterações nas estruturas de alocação são controladas, é possível reverter operações registradas de forma mínima. É importante entender que a alteração seja registrada "mínimo" não é não conectado.

## <a name="minimally-logged-operations"></a>Operações registradas de forma mínima

As seguintes operações são capazes de no mínimo sendo registrados:

- Criar tabela como selecionar ([CTAS][])
- INSERIR.. SELECIONE
- CRIAR ÍNDICE
- ALTER RECOMPILAÇÃO DE ÍNDICE
- DESCARTAR ÍNDICE
- TRUNCAR TABELA
- DESCARTAR TABELA
- PARTIÇÃO DE MUDANÇA ALTER TABLE

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Operações de movimentação de dados internas (como `BROADCAST` e `SHUFFLE`) não são afetados pelo limite de segurança de transação.

## <a name="minimal-logging-with-bulk-load"></a>Mínimo de registro em log com carregamento em massa

`CTAS`e `INSERT...SELECT` são ambas operações em massa carga. No entanto, ambas são influenciadas pela definição da tabela de destino e dependem do cenário carregar. Abaixo está uma tabela que explica se a operação em massa será no mínimo ou totalmente registrada:  

| Índice principal               | Cenário de carga                                            | Modo de log |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Pilha                        | Qualquer                                                      | **Mínimo**  |
| Índice agrupado             | Tabela de destino vazio                                       | **Mínimo**  |
| Índice agrupado             | Páginas existentes no destino não se sobreponham linhas carregadas | **Mínimo**  |
| Índice agrupado             | Sobreposição de linhas carregado com páginas existentes no destino        | Completo         |
| Índice de Columnstore agrupado | Tamanho de lote > = 102,400 por partição alinhado à distribuição | **Mínimo**  |
| Índice de Columnstore agrupado | Lote tamanho < 102,400 por partição alinhado à distribuição  | Completo         |

Vale a pena observar que todas as gravações para atualizar índices secundários ou não-clusterizados sempre será operações totalmente registradas.

> [AZURE.IMPORTANT] SQL Data Warehouse tem 60 distribuições. Portanto, considerando que todas as linhas são distribuídas uniformemente e iniciais em uma única partição, o lote precisará conter 6,144,000 linhas ou maior no mínimo esteja conectado ao gravar um índice de Columnstore agrupado. Se a tabela está particionada e as linhas sendo inseridas abrangem limites de partição, você precisará 6,144,000 linhas por limite de partição presumindo mesmo a distribuição de dados. Cada partição em cada distribuição independentemente deve exceder o limite de 102.400 linha para inserir mínimas sejam registrados para a distribuição.

Carregar dados em uma tabela não vazia com um índice de cluster geralmente pode conter uma mistura de linhas totalmente conectadas e registradas de forma mínima. Um índice agrupado é uma árvore balanceada (árvore b) das páginas. Se a página está sendo gravado já contém linhas de outra transação, em seguida, essas gravações serão totalmente registradas. No entanto, se a página estiver vazia, em seguida, a gravação para essa página será no mínimo registrada.

## <a name="optimizing-deletes"></a>Otimizando exclusões

`DELETE`é uma operação totalmente conectada.  Se você precisar excluir uma grande quantidade de dados em uma tabela ou uma partição, geralmente faz mais sentido para `SELECT` os dados que você deseja manter, que podem ser executados como uma operação registrada de forma mínima.  Para fazer isso, crie uma nova tabela com [CTAS][].  Uma vez criado, use a [RENOMEAR][] a troca de sua tabela antiga com a tabela recém-criado.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Otimizando atualizações

`UPDATE`é uma operação totalmente conectada.  Se você precisa atualizar um grande número de linhas em uma tabela ou uma partição geralmente é muito mais eficiente para usar uma operação de mínima conectada como [CTAS][] para fazê-lo.

No exemplo abaixo de uma tabela inteira atualização foi convertida em uma `CTAS` para que o registro em log mínimo é possível.

Nesse caso estamos retrospectively adicionando um valor de desconto para as vendas na tabela:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Recriar tabelas grandes pode se beneficiar usando recursos de gerenciamento de carga de trabalho de depósito de dados do SQL. Para obter mais detalhes consulte a seção de gerenciamento de carga de trabalho no artigo [concorrência][] .

## <a name="optimizing-with-partition-switching"></a>Otimizando com a alternância de partição

Ao se deparar com modificações de grande escala dentro de uma [partição de tabela][], uma partição migrando padrão faz muito sentido. Se a modificação de dados é significativa e abrange várias partições, em seguida, simplesmente iteração sobre as partições atinge o mesmo resultado.

As etapas para executar uma opção de partição são da seguinte maneira:
1. Criar um vazio out partição
2. Executar a atualização como um CTAS
3. Mudar os dados existentes para a tabela fora
4. Alternar os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições para alternar, primeiro precisamos construir um procedimento auxiliar como mostrado abaixo. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Esse procedimento maximiza a reutilização de código e mantém a partição migrando exemplo mais compacto.

O código abaixo demonstra as cinco etapas mencionadas acima para obter uma partição completa migrando rotina.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimizar o registro em log com pequenos lotes

Para operações de modificação de dados grandes, faz sentido para dividir a operação em blocos ou lotes escopo a unidade de trabalho.

Um exemplo prático é fornecido abaixo. O tamanho do lote foi definido para um número simples para realçar a técnica. Na verdade, o tamanho do lote seria significativamente maior. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pausar e a orientação de escala

Depósito de dados SQL Azure lhe permite pausar, retomar e dimensionar seu data warehouse sob demanda. Ao pausar ou dimensionar seu SQL Data Warehouse é importante entender que qualquer transações em trânsito são encerradas imediatamente; causando qualquer transações abertas ser revertido. Se sua carga de trabalho tinha emitido uma modificação de dados longo incompleta e em execução antes da operação de escala ou pausar, esse trabalho precisará ser desfeita. Isso pode afetar o tempo necessário para pausar ou dimensionar seu banco de dados de depósito de dados do SQL Azure. 

> [AZURE.IMPORTANT] Ambos `UPDATE` e `DELETE` são totalmente registradas operações e para que eles Desfazer/refazer operações podem levar significativamente mais do que o equivalente no mínimo conectado operações. 

É o melhor cenário permitir que em transações de modificação de dados de voos concluídas antes da pausa ou dimensionamento SQL Data Warehouse. No entanto, essa pode não ser sempre prática. Para atenuar o risco de uma reversão longo, considere uma das seguintes opções:

- Operações de longa duração regravar usando [CTAS][]
- Divida a operação em blocos; operando em um subconjunto das linhas

## <a name="next-steps"></a>Próximas etapas

Consulte [transações em SQL Data Warehouse][] para saber mais sobre níveis de isolamento e limites de transação.  Para obter uma visão geral de outras práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Transações em SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[partição de tabela]: ./sql-data-warehouse-tables-partition.md
[Concorrência]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RENOMEAR]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

