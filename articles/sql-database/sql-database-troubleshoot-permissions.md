<properties
    pageTitle="Como realizar tarefas de administração, por exemplo, redefinir a senha de administrador | Microsoft Azure"
    description="Descreve como executar tarefas administrativas comuns no banco de dados SQL. Por exemplo, a redefinição de senha de administrador, concedendo e removendo o acesso."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="Redefinir senha de administrador"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Como executar tarefas administrativas comuns, como a redefinição de senha de administrador no banco de dados do SQL Azure
Use este tópico para etapas rápidas para conceder e remover o acesso a um banco de dados do SQL Azure. Para obter informações completas, consulte:

- [Gerenciar bancos de dados e logon no banco de dados do SQL Azure](sql-database-manage-logins.md)
- [Proteger seu banco de dados do SQL](sql-database-security.md)
- [Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Para redefinir a senha de administrador para um servidor lógico

- No [Portal do Azure](https://portal.azure.com) clique **SQL Servers**, selecione o servidor na lista e, em seguida, clique em **Redefinir senha**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Para ajudar a tornar IP autorizado que somente os endereços são permitidos para acessar o servidor
- Consulte [como: configurar as configurações de firewall no banco de dados do SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Para criar usuários de banco de dados independente do banco de dados do usuário
- Use a instrução [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) e veja [Contidos usuários banco de dados - tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Para autenticar usuários de banco de dados independente, usando o Active Directory do Azure
- Consulte [conectar-se ao banco de dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Criar logon adicional para usuários altamente privilegiado no banco de dados mestre virtual
- Use a instrução [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) e consulte a seção de logon Gerenciando Gerenciando bancos de [dados e logon no banco de dados do Azure SQL](sql-database-manage-logins.md) para obter mais detalhes.
