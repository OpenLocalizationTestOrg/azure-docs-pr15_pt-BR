<properties
    pageTitle="Restaurar bancos de dados habilitados para alongar | Microsoft Azure"
    description="Saiba como restaurar Alongar\-habilitado para bancos de dados."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Restaurar bancos de dados habilitados para alongar

Restaure um backup do banco de dados quando necessário para recuperar muitos tipos de falhas, erros e desastres.

Para obter mais informações sobre backup, consulte [habilitados para alongar Backup bancos de dados](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] O backup é apenas uma parte de uma alta disponibilidade completa e a solução de continuidade de negócios. Para obter mais informações sobre alta disponibilidade, consulte [Soluções de disponibilidade de alta](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Restaurar os dados do SQL Server
Para recuperar falha de hardware ou corrupção, restaurar o Alongar\-banco de dados do SQL Server de um backup habilitado. Você pode continuar a usar os métodos de restauração do SQL Server que você usa no momento. Para obter mais informações, consulte [Visão geral de recuperação e restauração](https://msdn.microsoft.com/library/ms191253.aspx).

Depois de restaurar o banco de dados do SQL Server, você precisa executar o procedimento armazenado **sys.sp_rda_reauthorize_db** para restabelecer a conexão entre o Alongar\-habilitado para o banco de dados do SQL Server e o Azure remoto. Para obter mais informações, consulte [restaurar a conexão entre o banco de dados do SQL Server e o banco de dados remoto Azure](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurar os dados do Azure remotos

### <a name="recover-a-live-azure-database"></a>Recuperar um banco de dados do Azure ao vivo
O banco de dados do SQL Server Alongar serviço no Azure instantâneos todos os dados ao vivo pelo menos a cada 8 horas usando instantâneos de armazenamento do Azure. Esses instantâneos são mantidos por 7 dias. Isso permite que você restaure os dados em um dos pontos de 21 pelo menos em tempo dentro dos últimos 7 dias até a hora quando foi feito o último instantâneo.

Para restaurar um banco de dados do Azure ao vivo para um ponto anterior no tempo usando o portal do Azure, faça o seguinte.

1. Faça logon no portal do Azure.
2. No lado esquerdo da tela, selecione **Procurar** e selecione **Bancos de dados SQL**.
3. Navegue até o banco de dados e selecioná-lo.
4. Na parte superior da lâmina banco de dados, clique em **Restaurar**.
5. Especificar um novo **nome de banco de dados**, selecione um **Ponto de restauração** e clique em **criar**.
6. O processo de restauração do banco de dados será iniciado e possível monitorar usando **notificações**.

### <a name="recover-a-deleted-azure-database"></a>Recuperar um banco de dados do Azure excluído
O serviço de banco de dados do SQL Server Alongar no Azure tira um instantâneo de banco de dados antes de um banco de dados é descartado e retém-la por 7 dias. Depois que isso acontecer, ele não está mais retém instantâneos do banco de dados ao vivo. Isso permite restaurar um banco de dados excluído ao ponto quando foi excluído.

Para restaurar um banco de dados do Azure excluído ponto quando foi excluído usando o portal do Azure, faça o seguinte.

1. Faça logon no portal do Azure.
2. No lado esquerdo da tela, selecione **Procurar** e selecione **SQL Servers**.
3. Navegue até seu servidor e selecioná-lo.
4. Role para baixo até operações em lâmina do seu servidor, clique no bloco de **Bancos de dados excluídos** .
5. Selecione o banco de dados excluído que você deseja restaurar.
5. Especificar um novo **nome de banco de dados** e clique em **criar**.
6. O processo de restauração do banco de dados será iniciado e possível monitorar usando **notificações**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurar a conexão entre o banco de dados do SQL Server e o banco de dados remoto Azure

1.  Se você vai se conectar para restaurado Azure banco de dados com um nome diferente ou em uma região diferente, execute o procedimento armazenado [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) Desconecte o banco de dados do Azure anterior.  

2.  Executar o procedimento armazenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para se reconectar a Alongar local\-banco de dados no banco de dados do Azure habilitado.  

    -   Fornecer a credencial de banco de dados com escopo existente como um sysname ou um varchar\(128\) valor. \(Não use varchar\(máx\).\) Você pode procurar o nome de credencial no modo de exibição **sys.database\_escopo\_credenciais**.  

    -   Especifique se deseja fazer uma cópia dos dados remotos e se conecte a cópia (recomendada).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Consulte também

[Gerenciar e solucionar problemas de banco de dados de alongar](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Fazer backup e restauração de bancos de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
