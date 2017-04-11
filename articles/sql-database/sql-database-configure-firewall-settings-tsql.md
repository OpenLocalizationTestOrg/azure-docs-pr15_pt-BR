<properties
    pageTitle="Regras de firewall de nível de banco de dados e de servidor de banco de dados do SQL Azure usando T-SQL | Microsoft Azure"
    description="Saiba como configurar o firewall para endereços IP que acessar bancos de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurar regras de firewall de nível de banco de dados e de servidor de banco de dados de SQL Azure usando T-SQL


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Banco de dados SQL do Microsoft Azure usa regras de firewall para permitir conexões para seus servidores e bancos de dados. Você pode definir as configurações de firewall de servidor e níveis de banco de dados para o mestre ou um banco de dados do usuário no seu servidor de banco de dados do Azure SQL para seletivamente permitir acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que aplicativos do Azure para se conectar ao seu servidor de banco de dados, conexões Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e conexões a habilitação do Azure, consulte [Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md). Se você estiver criando conexões dentro do limite de nuvem Azure, você pode precisar abrir algumas portas TCP adicionais. Para obter mais informações, consulte o **V12 do banco de dados SQL: fora vs dentro** seção de [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Regras de firewall de nível de servidor

Somente o logon principal do nível do servidor ou o administrador do Active Directory do Azure pode criar uma regra de firewall de nível de servidor usando Transact-SQL.

1. Inicie uma janela de consulta e se conectar ao banco de dados mestre virtual usando o SQL Server Management Studio.
2. Regras de firewall do nível do servidor podem ser selecionadas, criadas, atualizadas ou excluídas de dentro da janela de consulta.
3. Para criar ou atualizar regras de firewall de nível de servidor, execute o `sp_set_firewall_rule` procedimento armazenado. O exemplo a seguir permite que um intervalo de endereços IP no servidor Contoso.<br/>Comece por vendo quais regras já existem.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Em seguida, adicione uma regra de firewall.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Para excluir uma regra de firewall de nível de servidor, execute o procedimento sp_delete_firewall_rule armazenado. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Para obter mais informações sobre esses procedimentos armazenados, consulte [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regras de firewall do nível de banco de dados

Somente um usuário de banco de dados com a permissão de **controle** no banco de dados (como o proprietário do banco de dados) pode criar uma regra de firewall de nível de banco de dados.

1. Depois de criar um firewall de nível de servidor para seu endereço IP, inicie uma janela de consulta por meio do portal do clássico ou SQL Server Management Studio.
2. Conecte ao banco de dados para o qual você deseja criar uma regra de firewall de nível de banco de dados.

    Para criar um novo ou atualizar uma regra de firewall de nível de banco de dados existente, execute o `sp_set_database_firewall_rule` procedimento armazenado. O exemplo a seguir cria uma nova regra de firewall denominada ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Para excluir uma regra de firewall de nível de banco de dados existente, execute o `sp_delete_database_firewall_rule` procedimento armazenado. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
`
   Executivo sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para obter mais informações sobre esses procedimentos armazenados, consulte [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Próximas etapas

Para como artigos sobre como criar regras de firewall de nível de servidor usando outros métodos, consulte: 

- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar regras de firewall de nível de servidor de banco de dados do Azure SQL usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para um tutorial sobre como criar um banco de dados, consulte [criar um banco de dados SQL em minutos usando o portal do Azure](sql-database-get-started.md).
Para obter ajuda sobre como se conectar a um banco de dados do SQL Azure de abrir origem ou aplicativos de terceiros, consulte [exemplos de código banco de dados SQL de início rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bancos de dados, consulte [Gerenciar a segurança de acesso e logon do banco de dados](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionais

- [Proteger seu banco de dados](sql-database-security.md)
- [Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589)
