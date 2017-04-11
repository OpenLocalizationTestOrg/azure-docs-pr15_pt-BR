<properties
    pageTitle="Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell | Microsoft Azure"
    description="Saiba como configurar o firewall para endereços IP que acessar bancos de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Banco de dados do SQL Azure usa regras de firewall para permitir conexões para seus servidores e bancos de dados. Você pode definir as configurações de firewall de nível de banco de dados e de servidor de banco de dados mestre ou um banco de dados do usuário no seu servidor de banco de dados SQL para seletivamente permitir acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que aplicativos do Azure para se conectar ao seu servidor de banco de dados, conexões Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e conexões a habilitação do Azure, consulte [Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md). Se você estiver criando conexões dentro do limite de nuvem Azure, você pode precisar abrir algumas portas TCP adicionais. Para obter mais informações, consulte o "V12 do banco de dados do SQL: fora vs dentro" seção de [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Criar regras de firewall de servidor

Nível de servidor firewall regras podem ser criadas, atualizados e excluídos usando o PowerShell do Azure.

Para criar uma nova regra de firewall de nível de servidor, execute o [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. O exemplo a seguir permite que um intervalo de endereços IP no servidor Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Para modificar uma regra de firewall de nível de servidor existente, execute o [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) cmdlet. O exemplo a seguir altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para excluir uma regra de firewall de nível de servidor existente, execute o [remover-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) cmdlet. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gerenciar regras de firewall usando o PowerShell

Você também pode usar o PowerShell para gerenciar regras de firewall. Para obter mais informações, consulte os tópicos a seguir:

* [Novo AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [Remover-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como usar o Transact-SQL para criar regras de firewall de servidor e níveis de banco de dados, consulte [regras de firewall de nível de banco de dados e de servidor de banco de dados do SQL Azure configurar usando T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para obter informações sobre como criar regras de firewall de nível de servidor usando outros métodos, consulte:

- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível de servidor de banco de dados do Azure SQL usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para um tutorial sobre como criar um banco de dados, consulte [criar um banco de dados SQL em minutos usando o portal do Azure](sql-database-get-started.md).
Para obter ajuda sobre como se conectar a um banco de dados do SQL Azure de abrir origem ou aplicativos de terceiros, consulte [exemplos de código banco de dados SQL de início rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bancos de dados, consulte [Gerenciar a segurança de acesso e logon do banco de dados](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionais

- [Proteger seu banco de dados](sql-database-security.md)
- [Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
