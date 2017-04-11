<properties
   pageTitle="Banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico de monitoramento | Microsoft Azure"
   description="Saiba como detectar e diagnosticar problemas de desempenho comuns usando modos de exibição de gerenciamento dinâmico para monitorar a Microsoft Azure SQL Database."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitoramento de banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico

Banco de dados do Microsoft Azure SQL permite que um subconjunto dos modos de exibição de gerenciamento dinâmico para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou de execução demorada, gargalos de recurso, planos de consulta baixa e assim por diante. Este tópico fornece informações sobre como detectar problemas de desempenho comuns usando modos de exibição de gerenciamento dinâmico.

Banco de dados SQL parcialmente dá suporte a três categorias de exibições de gerenciamento dinâmico:

- Exibições de gerenciamento dinâmico relacionados bancos de dados.
- Exibições de gerenciamento dinâmico relacionados a execução.
- Exibições de gerenciamento dinâmico relacionados à transação.

Para obter informações detalhadas sobre modos de exibição de gerenciamento dinâmico, consulte [funções (Transact-SQL) e exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms188754.aspx) nos Manuais Online do SQL Server.

## <a name="permissions"></a>Permissões

No banco de dados SQL, consultar um modo de exibição de gerenciamento dinâmico requer permissões de **Estado de banco de dados do modo de exibição** . A permissão de **Estado de banco de dados de exibição** retorna informações sobre todos os objetos de banco de dados atual.
Para conceder a permissão de **Estado de banco de dados de exibição** para um usuário de banco de dados específico, execute a seguinte consulta:

```GRANT VIEW DATABASE STATE TO database_user; ```

Uma instância do SQL Server de locais, exibições de gerenciamento dinâmico retornam informações de estado do servidor. No banco de dados SQL, elas retornam informações sobre lógico banco de dados atual apenas.

## <a name="calculating-database-size"></a>Calcular tamanho do banco de dados

A seguinte consulta retorna o tamanho do seu banco de dados (em megabytes):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

A seguinte consulta retorna o tamanho dos objetos individuais (em megabytes) em seu banco de dados:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorando conexões

Você pode usar o modo de exibição de [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) para recuperar informações sobre as conexões estabelecidas para um servidor de banco de dados do Azure SQL específico e os detalhes de cada conexão. Além disso, o modo de exibição de [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) é útil quando recuperar informações sobre todas as conexões de usuário ativo e tarefas internas.
A seguinte consulta recupera informações sobre a conexão atual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Ao executar o **sys.dm_exec_requests** e **modos de exibição de sys.dm_exec_sessions**, se você tiver permissão de **Estado de banco de dados de exibição** no banco de dados, consulte sessões tudo em execução no banco de dados; Caso contrário, você pode ver apenas a sessão atual.

## <a name="monitoring-query-performance"></a>Monitorar o desempenho de consulta

Lenta ou por tempo executando consultas pode consumir recursos significativos do sistema. Esta seção demonstra como usar modos de exibição de gerenciamento dinâmico para detectar alguns problemas comuns de desempenho de consulta. Uma referência mais antiga, mas ainda útil para solução de problemas, é o artigo de [Solução de problemas de desempenho no SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizando consultas primeiras N

O exemplo a seguir retorna informações sobre as principais cinco consultas classificados por tempo médio de CPU. Este exemplo agrega as consultas de acordo com sua hash de consulta, para que as consultas logicamente equivalentes são agrupadas por seu consumo de recurso acumulado.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitoramento consultas bloqueadas

Consultas lentas ou de execução demorada podem contribuir para consumo excessivo de recursos e ser a consequência de consultas bloqueadas. A causa do bloqueio pode ser design ruim do aplicativo, planos de consulta incorreta, a falta de índices útil e assim por diante. Você pode usar o modo de exibição de sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual em seu banco de dados do SQL Azure. Por exemplo código, consulte [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nos Manuais Online do SQL Server.

### <a name="monitoring-query-plans"></a>Monitoramento planos de consulta

Um plano de consulta ineficaz também pode aumentar o consumo de CPU. O exemplo a seguir usa o modo de exibição de [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar qual consulta usa a CPU mais acumulada.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Consulte também

[Introdução ao banco de dados SQL](sql-database-technical-overview.md)
