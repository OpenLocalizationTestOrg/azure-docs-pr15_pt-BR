<properties
    pageTitle="Configurar uma regra de firewall de nível de servidor de banco de dados SQL | Microsoft Azure"
    description="Saiba como configurar o firewall para endereços IP que acessar o Azure SQL server."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurar uma regra de firewall de nível de servidor de banco de dados de SQL Azure usando o Portal do Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Servidor do SQL Azure usa regras de firewall para permitir conexões para seus servidores e bancos de dados. Você pode definir as configurações de firewall de servidor e níveis de banco de dados para o mestre ou um banco de dados do usuário no seu servidor de lógica de servidor do SQL Azure para seletivamente permitir acesso ao banco de dados. Este tópico aborda as regras de firewall do nível do servidor.

> [AZURE.IMPORTANT] Para permitir que aplicativos do Azure para se conectar ao seu servidor do SQL Azure, conexões Azure devem estar habilitadas. Para compreender como as regras de firewall do trabalho, consulte [como configurar um firewall de servidor SQL Azure \- visão geral](sql-database-firewall-configure.md). Se você estiver criando conexões dentro do limite de nuvem Azure, você pode precisar abrir algumas portas TCP adicionais. Para obter mais informações, consulte o **V12 do banco de dados SQL: fora vs dentro** seção de [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recomendação:** Use regras de firewall de nível de servidor para administradores e quando você tem muitos bancos de dados que tenham os mesmos requisitos de acesso, e você não quer perder tempo configurando cada banco de dados individualmente. Recomendamos utilizar regras de firewall do nível de banco de dados sempre que possível, para melhorar a segurança e para tornar seu banco de dados mais portátil.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall de nível de servidor existentes por meio do portal do Azure

Repita as etapas para gerenciar as regras de firewall do nível do servidor.

- Para adicionar o computador atual, clique em Adicionar IP do cliente.
- Para adicionar endereços IP adicionais, digite o nome da regra, Start IP Address e endereço IP final.
- Modificar uma regra existente, clique em qualquer um dos campos na regra e modificar.
- Para excluir uma regra existente, passe o mouse sobre a regra até o X aparece no final da linha. Clique no X para remover a regra.

Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas

Como um artigo sobre como usar o Transact-SQL para criar regras de firewall de servidor e níveis de banco de dados, consulte [regras de firewall de nível de banco de dados e de servidor de banco de dados do SQL Azure configurar usando T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Para como artigos sobre como criar regras de firewall de nível de servidor usando outros métodos, consulte: 

- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
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

 
