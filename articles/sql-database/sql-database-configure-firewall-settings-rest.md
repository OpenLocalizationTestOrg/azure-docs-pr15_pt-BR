<properties
    pageTitle="Regras de firewall de nível de servidor de banco de dados do SQL Azure usando a API REST | Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurar regras de firewall de nível de servidor de banco de dados do Azure SQL usando a API REST


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Banco de dados SQL do Microsoft Azure usa regras de firewall para permitir conexões para seus servidores e bancos de dados. Você pode definir as configurações de firewall de servidor e níveis de banco de dados para o mestre ou um banco de dados do usuário no seu servidor de banco de dados do Azure SQL para seletivamente permitir acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que aplicativos do Azure para se conectar ao seu servidor de banco de dados, conexões Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e conexões a habilitação do Azure, consulte [Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md). Se você estiver criando conexões dentro do limite de nuvem Azure, você pode precisar abrir algumas portas TCP adicionais. Para obter mais informações, consulte o **V12 do banco de dados SQL: fora vs dentro** seção de [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gerenciar regras de firewall de nível de servidor por meio da API REST
1. Gerenciar regras de firewall por meio da API REST deve ser autenticada. Para obter informações, consulte [o guia do desenvolvedor autorização com a API do Gerenciador de recursos do Azure](../resource-manager-api-authentication.md).
2. Regras de nível de servidor podem ser criadas, atualizados ou excluídos usando API REST

    Para criar ou atualizar uma regra de firewall de nível de servidor, execute o método de colocar usando o seguinte:
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Corpo da solicitação

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Para remover uma regra de firewall de nível de servidor existente, execute o método DELETE usando o seguinte:
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gerenciar regras de firewall usando a API REST

* [Criar ou atualizar regra de Firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Excluir regra de Firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obter a regra de Firewall](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Lista todas as regras de Firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Próximas etapas

Como um artigo sobre como usar o Transact-SQL para criar regras de firewall de servidor e níveis de banco de dados, consulte [regras de firewall de nível de banco de dados e de servidor de banco de dados do SQL Azure configurar usando T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Para como artigos sobre como criar regras de firewall de nível de servidor usando outros métodos, consulte: 

- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)

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

 
