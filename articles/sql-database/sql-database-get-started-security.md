<properties
    pageTitle="Tutorial do banco de dados SQL: primeiros passos com segurança"
    description="Aprenda a criar contas de usuário para acessar e gerenciar um banco de dados."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Tutorial do banco de dados SQL: criar contas de usuário para acessar e gerenciar um banco de dados de banco de dados SQL


> [AZURE.SELECTOR]
- [Obter tutorial de Introdução](sql-database-get-started-security.md)
- [Conceder acesso](sql-database-manage-logins.md)

Neste tutorial, você vai aprender como usar o SQL Server Management Studio (SSMS) para:

- Faça logon no banco de dados do SQL usando um logon principal do nível do servidor.
- Crie uma conta de usuário de banco de dados SQL.
- Conceda a um usuário de banco de dados SQL [permissões db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Conecte a um banco de dados do SQL com uma conta de usuário que não é uma entidade de segurança de nível de servidor.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Próximas etapas
Agora que você tiver concluído este tutorial de banco de dados SQL e criou uma conta de usuário e permissões de usuário conta dbo, você está pronto para saber mais sobre a [segurança de banco de dados SQL](sql-database-manage-logins.md).


