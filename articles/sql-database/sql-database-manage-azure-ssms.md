<properties 
    pageTitle="Gerenciar um banco de dados do SQL com SSMS | Microsoft Azure" 
    description="Saiba como usar o SQL Server Management Studio para gerenciar bancos de dados e servidores de banco de dados SQL." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gerenciando o banco de dados do SQL Azure usando o SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Você pode usar o SQL Server Management Studio (SSMS) para administrar bancos de dados e servidores do Azure SQL Database. Este tópico apresenta tarefas comuns com SSMS. Você já deve ter um servidor e o banco de dados criado no banco de dados do Azure SQL antes de começar. Consulte [criar seu primeiro banco de dados do Azure SQL](sql-database-get-started.md) e [conectar e consulta usando o SSMS](sql-database-connect-query-ssms.md) para obter mais informações.

É recomendável que você use a versão mais recente do SSMS sempre que trabalhar com o Azure SQL Database. 

> [AZURE.IMPORTANT] Use sempre a versão mais recente do SSMS porque ele continuamente aprimorado para trabalhar com as atualizações mais recentes do Azure e banco de dados do SQL. Para obter a versão mais recente, consulte [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Criar e gerenciar bancos de dados do SQL Azure

Enquanto estiver conectado ao banco de dados **mestre** , você pode criar bancos de dados no servidor e modificar ou soltar bancos de dados existentes. As etapas a seguir descrevem como realizar várias tarefas de gerenciamento de banco de dados comuns por meio de Management Studio. Para executar essas tarefas, verifique se que você está conectado ao banco de dados **mestre** com o logon principal de nível de servidor que você criou ao configurar o seu servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

-   Use a instrução **CREATE DATABASE** para criar um banco de dados. Para obter mais informações, consulte [Criar banco de dados (banco de dados do SQL)](https://msdn.microsoft.com/library/dn268335.aspx). A instrução a seguir cria um banco de dados chamado **myTestDB** e especifica que se trata de um banco de dados de Standard Edition S0 com um tamanho máximo padrão de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Clique em **Executar** para executar a consulta.

-   Use a instrução **ALTER DATABASE** para modificar um banco de dados existente, por exemplo, se você quiser alterar o nome e a edição do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (banco de dados do SQL)](https://msdn.microsoft.com/library/ms174269.aspx). A instrução a seguir modifica o banco de dados que você criou na etapa anterior para alterar edition para S1 padrão.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Use **O banco de dados SOLTAR** instrução para excluir um banco de dados existente. Para obter mais informações, consulte [SOLTAR banco de dados (banco de dados do SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução a seguir exclui o banco de dados de **myTestDB** , mas não solte-agora porque você usará para criar o logon na próxima etapa.

        DROP DATABASE myTestBase;

-   Banco de dados mestre tem o modo de exibição de **Databases** que você pode usar para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes, execute a seguinte instrução:

        SELECT * FROM sys.databases;

-   No banco de dados SQL, a política de **uso** não há suporte para alternar entre os bancos de dados. Em vez disso, você precisa estabelecer uma conexão diretamente para o banco de dados de destino.

>[AZURE.NOTE] Muitos das instruções Transact-SQL que criar ou modificam um banco de dados devem ser executados em seu próprio lote e não podem ser agrupados com outras instruções de Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução.

## <a name="create-and-manage-logins"></a>Criar e gerenciar logon

Banco de dados **mestre** contém logon e quais logon tenha permissão para criar bancos de dados ou outro logon. Gerencie logon conectando-se ao banco de dados **mestre** com o logon principal de nível de servidor que você criou ao configurar o seu servidor. Você pode usar as instruções de **Logon de criar**, **Alterar logon**ou **LOGIN SOLTAR** para executar consultas em banco de dados mestre que gerencia logon em todo o servidor. Para obter mais informações, consulte [Gerenciar bancos de dados e logon no banco de dados do SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Use a instrução **CREATE LOGIN** para criar um logon de nível de servidor. Para obter mais informações, consulte [Criar LOGIN (banco de dados do SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A instrução a seguir cria um logon chamado **login1**. Substitua **Senha1** com a senha de sua escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Use a instrução **CREATE USER** para conceder permissões no nível do banco de dados. Todos os logons devem ser criados no banco de dados **mestre** . Para um logon para se conectar a um banco de dados diferentes, você deve conceder a ele permissões no nível do banco de dados usando a instrução **CREATE USER** no banco de dados. Para obter mais informações, consulte [Criar usuário (banco de dados do SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Para conceder login1 permissões para um banco de dados chamado **myTestDB**, conclua as seguintes etapas:

 1.  Para atualizar o Explorador de objetos para exibir o banco de dados de **myTestDB** que você criou, o nome do servidor no Pesquisador de objetos de atalho e clique em **Atualizar**.  

     Se você fechou a conexão, você pode reconectar selecionando **Conectar Object Explorer** no menu arquivo.

 2. Banco de dados de **myTestDB** de atalho e selecione **Nova consulta**.

    3.  Execute a seguinte instrução no banco de dados de myTestDB para criar um usuário de banco de dados denominado **login1User** que corresponde à nível do Servidor login **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use o **sp\_addrolemember** procedimento armazenado para dar a conta de usuário o nível apropriado de permissões no banco de dados. Para obter mais informações, consulte [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). A instrução a seguir fornece **login1User** permissões somente leitura ao banco de dados adicionando **login1User** para o **db\_datareader** função.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use a instrução **ALTER LOGIN** para modificar um logon existente, por exemplo, se você quiser alterar a senha de login. Para obter mais informações, consulte [Alterar LOGIN (banco de dados do SQL)](https://msdn.microsoft.com/library/ms189828.aspx). A instrução **ALTER LOGIN** deve ser executada no banco de dados **mestre** . Alterne para a janela de consulta que está conectada ao banco de dados. A instrução a seguir modifica o logon **login1** para redefinir a senha. Substitua **novasenha** com a senha de sua escolha e **Senha_atual** com a senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use a instrução de **LOGIN SOLTAR** para excluir um logon existente. Excluir um logon no nível do servidor também exclui quaisquer contas de usuário de banco de dados associado. Para obter mais informações, consulte [SOLTAR banco de dados (banco de dados do SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução **DROP LOGIN** deve ser executada no banco de dados **mestre** . A instrução exclui login **login1** .

        DROP LOGIN login1;

-   Banco de dados mestre tem o **sys.sql\_logon** exibir que você pode usar para exibir logon. Para exibir todos os logins existentes, execute a seguinte instrução:

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Monitorar usando exibições de gerenciamento dinâmico de banco de dados do SQL

Banco de dados SQL é compatível com várias exibições de gerenciamento dinâmico que você pode usar para monitorar a um banco de dados individual. Alguns exemplos do tipo de dados do monitor que você pode recuperar através esses modos de exibição estiverem acompanhando. Para obter detalhes completos e mais exemplos de uso, consulte [monitoramento de banco de dados SQL usando exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Consultar um modo de exibição de gerenciamento dinâmico requer permissões de **Estado de banco de dados do modo de exibição** . Para conceder a permissão de **Estado de banco de dados de exibição** para um usuário de banco de dados específico, conectar-se ao banco de dados e execute a seguinte instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcular o tamanho do banco de dados usando o **sys.dm\_db\_partição\_estatísticas** modo de exibição. O **sys.dm\_db\_partição\_estatísticas** exibição retorna informações de página e a contagem de linhas para cada partição no banco de dados, que você pode usar para calcular o tamanho do banco de dados. A seguinte consulta retorna o tamanho do seu banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use o **sys.dm\_executivo\_conexões** e **sys.dm\_executivo\_sessões** modos de exibição para recuperar informações sobre tarefas internas associadas ao banco de dados e conexões de usuário atual. A seguinte consulta retorna informações sobre a conexão atual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Use o **sys.dm\_executivo\_consulta\_estatísticas** cache de modo de exibição para recuperar estatísticas de desempenho agregado para planos de consulta. A seguinte consulta retorna informações sobre as principais cinco consultas classificados por tempo médio de CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
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
 
 
