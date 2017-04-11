<properties
    pageTitle="Nova configuração de banco de dados SQL com o PowerShell | Microsoft Azure"
    description="Saiba como criar um banco de dados do SQL com o PowerShell. Tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell."
    keywords="criar um novo banco de dados sql, configuração de banco de dados"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Criar um banco de dados do SQL e executar tarefas comuns de configuração de banco de dados com os cmdlets do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Saiba como criar um banco de dados do SQL usando cmdlets do PowerShell. (Para a criação de bancos de dados Elástico, consulte [criar um novo banco de dados elástica pool com o PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Configuração de banco de dados: criar um grupo de recursos, servidor e regras de firewall

Assim que tiver acesso para executar cmdlets em relação a sua assinatura do Azure selecionada, a próxima etapa é estabelecer o grupo de recursos que contém o servidor onde o banco de dados será criado. Você pode editar o próximo comando usar qualquer local válido que você escolher. Executar **(Get-AzureRmLocation | Where-Object {$_. Provedores - eq "Microsoft.Sql"}). Local** para obter uma lista de locais válidos.

Execute o seguinte comando para criar um grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Criar um servidor

Bancos de dados SQL são criados dentro de servidores do Azure SQL Database. Executar a [New-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) para criar um servidor. O nome do seu servidor deve ser exclusivo para todos os servidores do Azure SQL Database. Se o nome do servidor já está sendo usado, você receber um erro. Também vale a pena observar que esse comando pode demorar vários minutos para ser concluída. Você pode editar o comando usar qualquer local válida que você escolher, mas você deve usar o mesmo local em que você usou para o grupo de recursos criado na etapa anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando você executa este comando, você será solicitado para o seu nome de usuário e senha. Não insira suas credenciais do Azure. Em vez disso, insira o nome de usuário e senha para criar como o administrador do servidor. O script na parte inferior deste artigo mostra como configurar as credenciais do servidor no código.

Os detalhes do servidor aparecem depois que o servidor é criado com êxito.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurar uma regra de firewall do servidor para permitir acesso ao servidor

Para acessar o servidor, você precisa estabelecer uma regra de firewall. Executar a [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) comando, substituindo os endereços IP de início e término com valores válidos para o seu computador.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall aparecem depois que a regra é criada com êxito.

Para permitir que outros serviços do Azure acessar o servidor, adicione uma regra de firewall e defina a StartIpAddress e o EndIpAddress como 0.0.0.0. Essa regra permite o tráfego Azure qualquer assinatura do Azure para acessar o servidor.

Para obter mais informações, consulte [Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Criar um banco de dados do SQL

Agora você tem um grupo de recursos, um servidor e uma regra de firewall configurado para poder acessar o servidor.

O seguinte [New-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) comando cria um banco de dados SQL (em branco) na camada de serviço padrão, com um nível de desempenho de S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes de banco de dados aparecem após o banco de dados foi criado com êxito.

## <a name="create-a-sql-database-powershell-script"></a>Criar um banco de dados do SQL script do PowerShell

O seguinte script do PowerShell cria um banco de dados do SQL e todos os seus recursos dependentes. Substituir tudo `{variables}` com valores específicos para sua assinatura e recursos (remover a **{}** ao definir seus valores).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Próximas etapas
Depois de criar um banco de dados do SQL e realizar tarefas de configuração de banco de dados básicos, você está pronto para o seguinte:

- [Gerenciar o banco de dados do SQL com o PowerShell](sql-database-manage-powershell.md)
- [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Recursos adicionais

- [Cmdlets de banco de dados do SQL azure] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Banco de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
