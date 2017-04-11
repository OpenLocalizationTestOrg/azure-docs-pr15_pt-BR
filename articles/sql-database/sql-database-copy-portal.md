<properties
    pageTitle="Copiar um banco de dados do SQL Azure usando o portal de Azure | Microsoft Azure"
    description="Criar uma cópia de um banco de dados do SQL Azure"
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



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiar um banco de dados de SQL Azure usando o portal do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

As etapas a seguir mostram como copiar um banco de dados do SQL com o [portal do Azure](https://portal.azure.com) para o mesmo servidor ou um servidor diferente.

Para copiar um banco de dados do SQL, você precisa dos seguintes itens:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Avaliação gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Um banco de dados SQL para copiar. Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Copie o banco de dados do SQL

Abra a página de banco de dados SQL do banco de dados que você deseja copiar:

1.  Acesse o [portal do Azure](https://portal.azure.com).
2.  Clique em **mais serviços** > **bancos de dados SQL**e clique em banco de dados desejado.
3.  Na página de banco de dados do SQL, clique em **Copiar**:

    ![Banco de dados SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  Na página **Copiar** , um nome de banco de dados padrão é fornecido. Digite um nome diferente se desejar (todos os bancos de dados em um servidor devem ter nomes exclusivos).
2.  Selecione um **servidor de destino**. O servidor de destino é onde a cópia do banco de dados é criada. Você pode copiar o banco de dados para o mesmo servidor, ou um servidor diferente. Você pode criar um servidor ou selecione um servidor existente na lista. 
3.  Depois de selecionar as opções de **servidor de destino**, o **pool elástica banco de dados**e **camada de preços** será habilitado. Se o servidor tiver um pool, você pode copiar o banco de dados nela.
3.  Clique em **Okey** para iniciar o processo de cópia.

    ![Banco de dados SQL](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorar o progresso da operação de cópia

- Após iniciar a cópia, clique na notificação portal para obter detalhes.

    ![notificação][3]
 
    ![Monitor][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Verifique se que o banco de dados está ao vivo no servidor

- Clique em **mais serviços** > **bancos de dados SQL** e verifique se o novo banco de dados está **Online**.


## <a name="resolve-logins"></a>Resolver logon

Para resolver logon após a conclusão da operação de cópia, consulte [resolver logon](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Próximas etapas

- Consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md) para uma visão geral de cópia de um banco de dados do SQL Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
- Consulte [Copiar um banco de dados do SQL Azure usando T-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados com Transact-SQL.
- Veja [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md) para saber mais sobre gerenciamento de usuários e logon ao copiar um banco de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciar logon](sql-database-manage-logins.md)
- [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

