<properties 
    pageTitle="Copiar um banco de dados do SQL Azure com Transact-SQL | Microsoft Azure" 
    description="Criar cópia de um banco de dados do SQL Azure com Transact-SQL" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiar um banco de dados do SQL Azure com Transact-SQL


> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


Este etapas a seguir mostram como copiar um banco de dados do SQL com Transact-SQL para o mesmo servidor ou um servidor diferente. A operação de cópia do banco de dados utiliza a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Avaliação gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Um banco de dados do SQL Azure. Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se você não tiver SSMS, ou se os recursos descritos neste artigo não estão disponíveis, [Baixe a versão mais recente](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copie o banco de dados do SQL

Faça logon no banco de dados mestre usando o logon principal do nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Logon que não é o capital de nível de servidor deve ser membros da função dbmanager para copiar bancos de dados. Para obter mais informações sobre logon e conectar ao servidor, consulte [Gerenciar logon](sql-database-manage-logins.md).

Inicie a cópia do banco de dados de origem com a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Executando esta instrução inicia o processo de cópia do banco de dados. Como copiar um banco de dados é um processo assíncrono, na instrução CREATE DATABASE retorna antes do banco de dados for concluído copiando.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados do SQL no mesmo servidor

Faça logon no banco de dados mestre usando o logon principal do nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Logon que não é o capital de nível de servidor deve ser membros da função dbmanager para copiar bancos de dados.

Este cópias de comando Database1 para um novo banco de dados denominada Database2 no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia pode levar algum tempo para ser concluída.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino, o servidor de banco de dados do Azure SQL onde o novo banco de dados será criada. Use um logon que tenha o mesmo nome e senha como o proprietário do banco de dados (DBO) do banco de dados de origem no servidor de banco de dados do Azure SQL de origem. O login no servidor de destino também deve ser um membro da função dbmanager ou ser login principal nível do servidor.

Este comando copia Database1 server1-um novo banco de dados denominada Database2 em server2. Dependendo do tamanho do banco de dados, a operação de cópia pode levar algum tempo para ser concluída.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorar o progresso da operação de cópia

Monitore o processo de cópia consultando os modos de exibição Databases e sys.dm_database_copies. Enquanto a cópia está em andamento, a coluna state_desc da exibição Databases para o novo banco de dados está definida como copiar.


- Se a cópia falhar, a coluna de state_desc da exibição Databases para o novo banco de dados está definida como suspeito. Nesse caso, execute a instrução DROP no novo banco de dados e tentar novamente mais tarde.
- Se a cópia estiver concluído, a coluna state_desc da exibição Databases para o novo banco de dados está definida como ONLINE. Nesse caso, a cópia é concluída e o novo banco de dados é um banco de dados regular, podem ser alterados independentemente do banco de dados de origem.

> [AZURE.NOTE] -Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução de [SOLTAR banco de dados](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, executando a instrução SOLTAR banco de dados no banco de dados de origem também cancela o processo de cópia.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Resolver logon após a conclusão da operação de cópia

Depois que o novo banco de dados está online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para mapear novamente os usuários do banco de dados novo logon no servidor de destino. Para resolver usuários órfãos, consulte [Solucionar problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Veja também [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md).

Todos os usuários do banco de dados novo mantenham as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se torna o proprietário do banco de dados do banco de dados novo e é atribuído a um novo identificador de segurança (SID). Após a cópia é bem sucedida e antes de outros usuários são remapeados, somente o logon que iniciou a cópia, o proprietário do banco de dados (DBO), pode fazer logon no novo banco de dados.


## <a name="next-steps"></a>Próximas etapas

- Consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md) para uma visão geral de cópia de um banco de dados do SQL Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando o portal do Azure](sql-database-copy-portal.md) para copiar um banco de dados usando o portal do Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
- Veja [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md) para saber mais sobre gerenciamento de usuários e logon ao copiar um banco de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciar logon](sql-database-manage-logins.md)
- [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


