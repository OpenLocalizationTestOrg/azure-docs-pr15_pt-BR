<properties
   pageTitle="Migrar seu código SQL para SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seu código SQL depósito de dados do SQL Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar o código SQL depósito de dados do SQL

Ao migrar seu código de outro banco de dados depósito de dados do SQL, provavelmente você precisará fazer alterações em sua base de código. Alguns recursos de SQL Data Warehouse significativamente podem melhorar o desempenho como foram projetados para trabalhar no modo distribuído. No entanto, para manter o desempenho e dimensionamento, alguns recursos também não estão disponíveis.

## <a name="common-t-sql-limitations"></a>Limitações de T-SQL comuns

A lista a seguir resume o recurso mais comuns que não são suportados no depósito de dados do SQL Azure. Os links levam você a soluções alternativas para o recurso sem suporte:

- [Junções ANSI atualizações][]
- [Junções ANSI em exclusões][]
- [declaração de mesclagem][]
- junções entre bancos de dados
- [cursores][]
- [SELECIONE.. EM][]
- [INSERIR.. EXECUTIVO][]
- cláusula de saída
- embutido funções definidas pelo usuário
- declaração de várias funções
- [expressões de tabela comuns](#Common-table-expressions)
- [recursiva expressões de tabela comuns (CTE)] (#Recursive-common-table-expressions-(CTE)
- Procedimentos e funções CLR
- função $partition
- variáveis de tabela
- parâmetros de valor de tabela
- transações distribuídas
- confirmação / reversão trabalho
- Salvar transação
- contextos de execução (EXECUTE AS)
- [cláusula com rollup Group by / cubo / define as opções de agrupamento][]
- [níveis de aninhamento além de 8][]
- [Atualizando por meio de exibições][]
- [uso de selecionar para atribuição de variável][]
- [o tipo de dados sem máx para cadeias de caracteres SQL dinâmicas][]

Felizmente maioria dessas limitações pode ser contornada. Explicações são fornecidas nos artigos desenvolvimento relevantes mencionados acima.

## <a name="supported-cte-features"></a>Recursos CTE com suporte

SQL Data Warehouse parcialmente são compatíveis com expressões de tabela comuns (CTEs).  Os seguintes recursos CTE são suportados:

- Uma CTE pode ser especificada em uma instrução SELECT.
- Uma CTE pode ser especificada em uma instrução CREATE VIEW.
- Uma CTE pode ser especificada em uma instrução de criar tabela como selecionar (CTAS).
- Uma CTE pode ser especificada em uma instrução de criar remoto tabela como selecionar (CRTAS).
- Uma CTE pode ser especificada em uma instrução de criar EXTERNOS tabela como selecionar (CETAS).
- Uma tabela remota pode ser referenciada a partir de uma CTE.
- Uma tabela externa pode ser referenciada a partir de uma CTE.
- Várias definições de consulta CTE podem ser definidas em uma CTE.

## <a name="cte-limitations"></a>Limitações de CTE

Expressões de tabela comuns têm algumas limitações SQL Data Warehouse, incluindo:

- Uma CTE deve ser seguida por uma única instrução SELECT. Inserir, atualizar, excluir, e demonstrativos de mesclagem não são suportados.
- Uma expressão de tabela comum que inclua referências a mesmo (uma expressão comum recursiva tabela) não é compatível (consulte abaixo seção).
- Não é permitido especificar mais de um com cláusula em uma CTE. Por exemplo, se um CTE_query_definition contiver uma subconsulta, que subconsulta não pode conter um aninhada com cláusula que define outra CTE.
- Uma cláusula ORDER BY não pode ser usada em CTE_query_definition, exceto quando uma cláusula TOP é especificada.
- Quando uma CTE é usada em uma instrução que faz parte de um lote, a instrução antes que ele deve ser seguida por um ponto e vírgula.
- Quando usada em instruções preparadas por sp_prepare, CTEs irá se comportar da mesma forma que outras instruções SELECT em PDW. No entanto, se CTEs forem usadas como parte do CETAS preparada por sp_prepare, pode adiar o comportamento do SQL Server e outras instruções PDW devido a maneira vinculação é implementada para sp_prepare. Se selecionar que referências CTE está usando uma coluna errada que não existe no CTE, o sp_prepare passará sem detectar o erro, mas o erro será lançado durante sp_execute em vez disso.

## <a name="recursive-ctes"></a>CTEs recursivas

CTEs recursivas não são suportadas no depósito de dados do SQL.  O migraion da CTE recursiva pode ser um pouco concluída e o melhor processo é dividir os em várias etapas. Normalmente, você pode usar um loop e preencher uma tabela temporária conforme você itera as consultas provisório recursivas. Depois que a tabela temporária é preenchida, em seguida, você poderá retornar os dados como um único conjunto de resultados. Uma abordagem semelhante foi usada para resolver `GROUP BY WITH CUBE` no artigo [cláusula com rollup group by / cubo / define as opções de agrupamento][] .

## <a name="unsupported-system-functions"></a>Funções de sistema sem suporte

Também existem algumas funções de sistema que não são suportadas. Estes são alguns dos principais que normalmente pode achar usados em data warehouse:

- NEWSEQUENTIALID)
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Alguns desses problemas podem ser contornado.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsolução alternativa

Para contornar a falta de suporte para @@ROWCOUNT, criar um procedimento armazenado que irá recuperar a última contagem de linhas de sys.dm_pdw_request_steps e executar `EXEC LastRowCount` após uma instrução DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista completa de todos os extratos de T-SQL com suporte, consulte [os tópicos de Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Junções ANSI atualizações]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Junções ANSI em exclusões]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[declaração de mesclagem]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERIR.. EXECUTIVO]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Tópicos de Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[cursores]: ./sql-data-warehouse-develop-loops.md
[SELECIONE.. EM]: ./sql-data-warehouse-develop-ctas.md#selectinto
[cláusula com rollup Group by / cubo / define as opções de agrupamento]: ./sql-data-warehouse-develop-group-by-options.md
[níveis de aninhamento além de 8]: ./sql-data-warehouse-develop-transactions.md
[Atualizando por meio de exibições]: ./sql-data-warehouse-develop-views.md
[uso de selecionar para atribuição de variável]: ./sql-data-warehouse-develop-variable-assignment.md
[o tipo de dados sem máx para cadeias de caracteres SQL dinâmicas]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
