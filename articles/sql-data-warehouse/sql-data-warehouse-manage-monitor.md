<properties
   pageTitle="Monitorar sua carga de trabalho usando DMVs | Microsoft Azure"
   description="Saiba como monitorar sua carga de trabalho usando DMVs."
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
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Monitorar sua carga de trabalho usando DMVs

Este artigo descreve como usar exibições de gerenciamento dinâmico (DMVs) para monitorar a carga de trabalho e investigar execução da consulta no depósito de dados do SQL Azure.

## <a name="permissions"></a>Permissões

Para consultar as DMVs neste artigo, você precisa de permissão de estado de banco de dados do modo de exibição ou controle. Geralmente concedendo estado de banco de dados do modo de exibição é a permissão preferencial pois é muito mais restritivo.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Conexões de monitor

Efetuou logon todos depósito de dados do SQL [sys.dm_pdw_exec_sessions][].  Essa DMV contém a última 10.000 logon.  O session_id é a chave primária e é atribuído sequencialmente para cada novo logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Execução da consulta de monitor

Todas as consultas executadas no SQL Data Warehouse efetuou [sys.dm_pdw_exec_requests][].  Essa DMV contém as última 10.000 consultas executadas.  O request_id exclusivamente identifica cada consulta e é a chave primária para este departamento de trânsito.  O request_id é atribuído sequencialmente para cada nova consulta e tem o prefixo sobre, o que significa ID da consulta.  Consultar essa DMV para um determinado session_id mostra todas as consultas para um determinado logon.

>[AZURE.NOTE] Procedimentos armazenados usam várias IDs de solicitação.  Identificações de solicitação são atribuídas em ordem sequencial. 

Aqui estão as etapas a seguir para investigar planos de execução de consulta e horas para uma consulta específica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Etapa 1: Identificar a consulta que você deseja investigar

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Nos resultados da consulta anterior, **a identificação de solicitação de anotação** da consulta que você gostaria de investigar.

Consultas no estado **suspenso** são sendo enfileiradas devido a limites de concorrência. Essas consultas também são exibidos na consulta aguarda sys.dm_pdw_waits com um tipo de UserConcurrencyResourceType. Consulte [gerenciamento de concorrência e carga de trabalho][] para obter mais detalhes sobre os limites de concorrência. Consultas também podem esperar por outros motivos como para bloqueios de objeto.  Se sua consulta está aguardando um recurso, consulte [consultas Investigating aguardando recursos][] mais para baixo neste artigo.

Para simplificar a pesquisa de uma consulta na tabela sys.dm_pdw_exec_requests, use o [rótulo][] para atribuir um comentário à sua consulta que pode ser procurada no modo de exibição sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>Etapa 2: Investigar o plano de consulta

Use a identificação de solicitação para recuperar o plano de SQL (DSQL) distribuído da consulta de [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL está demorando mais que o esperado, a causa pode ser um plano complexo com várias etapas DSQL ou apenas uma etapa levando muito tempo.  Se o plano for várias etapas com diversas operações de movimentação, considere otimizá distribuições sua tabela para reduzir a movimentação de dados. O artigo de [distribuição de tabela][] explica por que os dados devem ser movidos para resolver uma consulta e explica algumas estratégias de distribuição para minimizar a movimentação de dados.

Para investigar ainda mais detalhes sobre uma única etapa, a coluna *operation_type* da etapa de consulta de execução demorada e observe o **Índice de etapa**:

- Continuar com etapa 3a para **operações de SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continuar com etapa 3b para **operações de movimentação de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ETAPA 3a: investigar SQL nos bancos de dados distribuídos

Use a identificação de solicitação e o índice de etapa para recuperar detalhes de [sys.dm_pdw_sql_requests][], que contém informações de execução da etapa de consulta em todos os bancos de dados distribuídos.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando a etapa de consulta está em execução, [DBCC PDW_SHOWEXECUTIONPLAN][] pode ser usado para recuperar o plano estimado do SQL Server do cache de plano do SQL Server para a etapa em execução em uma distribuição específico.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ETAPA 3b: investigar movimentação de dados nos bancos de dados distribuídos

Use a identificação de solicitação e o índice de etapa para recuperar informações sobre uma etapa de movimentação de dados em execução no cada distribuição do [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Verifique a coluna *total_elapsed_time* para ver se uma distribuição específico está levando significativamente mais de outras pessoas para movimentação de dados.
- Para a distribuição de execução demorada, verifique a coluna *rows_processed* para ver se o número de linhas sendo movido do que distribuição é significativamente maior do que as outras pessoas. Em caso afirmativo, isso pode indicar distorção dos dados subjacentes.

Se a execução da consulta, [DBCC PDW_SHOWEXECUTIONPLAN][] pode ser usado para recuperar o plano estimado do SQL Server do cache de plano do SQL Server para a etapa de SQL em execução no momento dentro de uma distribuição específico.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Consultas de espera do monitor

Se você descobrir que sua consulta não está fazendo progresso porque está aguardando para um recurso, eis uma consulta que mostra todos os recursos que uma consulta está aguardando.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta está aguardando ativamente em recursos de outra consulta, o estado será **AcquireResources**.  Se a consulta tem todos os recursos necessários, o estado será **concedido**.

## <a name="next-steps"></a>Próximas etapas
Consulte [modos de exibição do sistema][] para obter mais informações sobre DMV.
Consulte [SQL Data Warehouse práticas recomendadas][] para obter mais informações sobre as práticas recomendadas

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md
[Modos de exibição do sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribuição da tabela]: ./sql-data-warehouse-tables-distribute.md
[Gerenciamento de concorrência e carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Investigar consultas aguardando recursos]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[RÓTULO]: https://msdn.microsoft.com/library/ms190322.aspx
