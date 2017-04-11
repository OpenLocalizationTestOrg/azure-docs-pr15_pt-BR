<properties
    pageTitle="Gerenciar e solucionar problemas de banco de dados de alongar | Microsoft Azure"
    description="Saiba como gerenciar e solucionar problemas Alongar banco de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Gerenciar e solucionar problemas de banco de dados de alongar

Para gerenciar e solucionar problemas de banco de dados de alongar, use as ferramentas e os métodos descritos neste tópico.

## <a name="manage-local-data"></a>Gerenciar dados locais

### <a name="LocalInfo"></a>Obter informações sobre bancos de dados locais e tabelas habilitadas Alongar banco de dados
Abrir o catálogo exibições **Databases** e **Tables** para ver informações sobre Alongar\-habilitado tabelas e bancos de dados do SQL Server. Para obter mais informações, consulte [Databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [Tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver quanto espaço um Alongar\-tabela habilitada está usando no SQL Server, execute a seguinte instrução.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gerenciar a migração de dados

### <a name="check-the-filter-function-applied-to-a-table"></a>Verifique a função de filtro aplicada a uma tabela
Abrir o modo de exibição de catálogo **sys.remote\_dados\_arquivamento\_tabelas** e verificar o valor da **filtro\_predicado** coluna para identificar a função que estejam usando o banco de dados de Alongar para selecionar linhas a serem migradas. Se o valor for nulo, a tabela inteira é elegível a serem migradas. Para obter mais informações, consulte [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) e [Selecione as linhas a serem migradas usando uma função de filtro](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Verificar o status de migração de dados
Selecione tarefas **| Alongar | Monitor** para um banco de dados do SQL Server Management Studio para monitorar a migração de dados em Alongar Monitor de banco de dados. Para obter mais informações, consulte [Monitor e solucionar problemas de migração de dados (banco de dados de alongar)](sql-server-stretch-database-monitor.md).

Ou, abra o modo de exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migração\_status** para ver quantas lotes e linhas de dados foram migradas.

### <a name="Firewall"></a>Solucionar problemas de migração de dados
Para sugestões de solução de problemas, consulte [Monitor e solucionar problemas de migração de dados (banco de dados de alongar)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gerenciar dados remotos

### <a name="RemoteInfo"></a>Obter informações sobre bancos de dados remotos e tabelas usadas pelo Alongar banco de dados
Abra os modos de exibição de catálogo **sys.remote\_dados\_arquivamento\_bancos de dados** e **sys.remote\_dados\_arquivamento\_tabelas** para ver as informações sobre as tabelas e bancos de dados remotos no qual os dados migrados estão armazenados. Para obter mais informações, consulte [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Para ver quanto espaço está usando uma tabela habilitados para alongar Azure, execute a seguinte instrução.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Excluir dados migrados  
Se você quiser excluir dados que já tem sido migrados para o Azure, siga as etapas descritas em [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gerenciar o esquema de tabela

### <a name="dont-change-the-schema-of-the-remote-table"></a>Não alterar o esquema da tabela remota
Não altere o esquema de uma tabela de Azure remoto que está associada a uma tabela do SQL Server configurada para alongar banco de dados. Em particular, não modifique o nome ou o tipo de dados de uma coluna. O recurso de banco de dados de alongar faz vários suposições sobre o esquema da tabela remota em relação o esquema de tabela do SQL Server. Se você alterar o esquema remoto, banco de dados de alongar parar de trabalhar para a tabela alterada.

### <a name="reconcile-table-columns"></a>Reconciliar colunas da tabela  
Se você tiver excluído acidentalmente colunas da tabela remota, execute **sp_rda_reconcile_columns** para adicionar colunas à tabela remota que consta o Alongar\-habilitado para a tabela do SQL Server, mas não na tabela remota. Para obter mais informações, consulte [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Quando **sp_rda_reconcile_columns** recria colunas que acidentalmente excluídos da tabela remota, ele não restaurar os dados que estavam anteriormente nas colunas excluídas.

**sp_rda_reconcile_columns** não excluir colunas da tabela remota que existem na tabela de dados remota, mas não na Alongar\-habilitado para a tabela do SQL Server. Se houver colunas na tabela Azure remota que não existem mais na Alongar\-habilitado para SQL Server tabela, essas colunas extras não impedem banco de dados de alongar operando normalmente. Opcionalmente, você pode remover as colunas extras manualmente.  

## <a name="manage-performance-and-costs"></a>Gerenciar o desempenho e os custos  

### <a name="troubleshoot-query-performance"></a>Solucionar problemas de desempenho de consulta
Consultas que incluem Alongar\-tabelas habilitadas espera executar mais lentamente do que antes que as tabelas foram habilitadas para alongar. Se o desempenho da consulta cai consideravelmente, examine os possíveis problemas a seguintes.

-   É o seu servidor do Azure em uma região geográfica diferente que o SQL Server? Configure seu servidor do Azure para ser na mesma região geográfica como SQL Server para reduzir a latência de rede.

-   Suas condições de rede podem ter degradado. Contate o administrador de rede para obter informações sobre problemas recentes ou interrupções.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumentar nível de desempenho Azure para recurso\-intenso operações como indexação
Quando você cria, recriar ou reorganizar um índice em uma tabela grande que está configurado para alongar banco de dados, e você prevê consultando pesado dos dados migrados no Azure durante esse período, considere aumentar o nível de desempenho do correspondente remoto Azure banco de dados para a duração da operação. Para obter mais informações sobre níveis de desempenho e preços, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Não é possível pausar o serviço de banco de dados do SQL Server Alongar no Azure  
 Certifique-se de que você selecionar o desempenho apropriado e preços nível. Se você aumentar o nível de desempenho temporariamente para um recurso\-operação intenso, restaurá-lo ao nível anterior após a conclusão da operação. Para obter mais informações sobre níveis de desempenho e preços, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Alterar o escopo de consultas  
 Consultas em Alongar\-tabelas habilitadas retornam dados locais e remotos por padrão. Você pode alterar o escopo de consultas para todas as consultas por todos os usuários ou apenas para uma única consulta por um administrador.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Alterar o escopo de consultas para todas as consultas por todos os usuários  
 Para alterar o escopo de todas as consultas por todos os usuários, execute o procedimento armazenado **sys.sp_rda_set_query_mode**. Você pode reduzir o escopo para consultar apenas dados locais, desabilitar todas as consultas ou restaurar a configuração padrão. Para obter mais informações, consulte [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Alterar o escopo de consultas para uma única consulta por um administrador  
 Para alterar o escopo de uma única consulta por um membro da função db_owner, adicione a * *com \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)* * a dica de consulta de instrução SELECT. A dica de consulta REMOTE_DATA_ARCHIVE_OVERRIDE pode ter os seguintes valores.  
 -   **LOCAL_ONLY**. Consultar apenas dados locais.  

 -   **REMOTE_ONLY**. Consultar apenas dados remotos.  

 -   **STAGE_ONLY**. Consulte apenas os dados na tabela onde estágios de banco de dados de alongar linhas qualificado para migração e retém linhas migradas para o período especificado após a migração. Essa dica de consulta é a única maneira para consultar a tabela temporária.  

Por exemplo, a seguinte consulta retorna apenas resultados locais.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Fazer exclusões e atualizações administrativas  
 Por padrão, você não pode atualizar ou excluir linhas são elegíveis para migração ou linhas que já foram migradas, em um Alongar\-habilitado para a tabela. Quando você tem que corrigir um problema, um membro da função db_owner pode executar uma operação de atualização ou exclusão, adicionando a * *com \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)* * dica de consulta para a instrução. A dica de consulta REMOTE_DATA_ARCHIVE_OVERRIDE pode ter os seguintes valores.  
 -   **LOCAL_ONLY**. Atualizar ou excluir apenas dados locais.  

 -   **REMOTE_ONLY**. Atualizar ou excluir dados remotos somente.  

 -   **STAGE_ONLY**. Atualizar ou excluir somente os dados na tabela onde estágios de banco de dados de alongar linhas qualificado para migração e retém linhas migradas para o período especificado após a migração.  

## <a name="see-also"></a>Consulte também

[Alongar Monitor de banco de dados](sql-server-stretch-database-monitor.md)

[Bancos de dados habilitados para alongar backup](sql-server-stretch-database-backup.md)

[Restaurar bancos de dados habilitados para alongar](sql-server-stretch-database-restore.md)
