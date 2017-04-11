<properties
    pageTitle="Gerenciar o banco de dados do SQL Azure com o PowerShell | Microsoft Azure"
    description="Gerenciamento de banco de dados do SQL Azure com o PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Gerenciar o banco de dados do SQL Azure com o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Este tópico mostra os cmdlets do PowerShell que são usados para realizar várias tarefas de Azure SQL Database. Para uma lista completa, consulte [Azure SQL banco de dados Cmdlets] (https://msdn.microsoft.com/library/mt574084(v=azure.300\).aspx).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos para nosso banco de dados SQL e recursos Azure relacionados com o [New-AzureRmResourceGroup] (https://msdn.microsoft.com/library/azure/mt759837(v=azure.300\).aspx) cmdlet.

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obter mais informações, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).
Para um exemplo de script, consulte [criar um banco de dados do SQL script do PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL

Criar um servidor de banco de dados SQL com o [New-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) cmdlet. Substitua o nome do seu servidor *server1* . Nomes de servidor devem ser exclusivos em todos os servidores do Azure SQL Database. Se o nome do servidor já está sendo usado, você receber um erro. Este comando pode levar alguns minutos para ser concluída. O grupo de recursos já deve existir em sua assinatura.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Para obter mais informações, consulte [o que é o banco de dados SQL](sql-database-technical-overview.md). Para um exemplo de script, consulte [criar um banco de dados do SQL script do PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Criar uma regra de firewall do banco de dados do SQL server

Criar uma regra de firewall para acessar o servidor com o [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. Execute o seguinte comando, substituindo os endereços IP de início e término com valores válidos para seu cliente. O grupo de recursos e o servidor já devem existir em sua assinatura.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir que outros serviços Azure acessem seu servidor, crie uma regra de firewall e defina ambas as `-StartIpAddress` e `-EndIpAddress` como **0.0.0.0**. Essa regra de firewall especial permite que todo o tráfego de Azure acessar o servidor.

Para obter mais informações, consulte [Firewall de banco de dados do SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para um exemplo de script, consulte [criar um banco de dados do SQL script do PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Criar um banco de dados do SQL (em branco)

Criar um banco de dados com o [New-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) cmdlet. O grupo de recursos e o servidor já devem existir em sua assinatura. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obter mais informações, consulte [o que é o banco de dados SQL](sql-database-technical-overview.md). Para um exemplo de script, consulte [criar um banco de dados do SQL script do PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Alterar o nível de desempenho de um banco de dados do SQL

Dimensionar seu banco de dados para cima ou para baixo com o [Set-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet. O grupo de recursos, o servidor e o banco de dados já devem existir em sua assinatura. Definir o `-RequestedServiceObjectiveName` para um único espaço (como o trecho a seguir) para camada básica. Configurá-lo para *S0*, *S1*, *P1*, *P6*, etc., como no exemplo anterior para outros níveis.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obter mais informações, consulte [desempenho e opções de banco de dados SQL: entender o que está disponível em cada nível de serviço](sql-database-service-tiers.md). Para um exemplo de script, consulte [script do PowerShell de exemplo para alterar o nível de desempenho e nível de serviço de seu banco de dados do SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados do SQL no mesmo servidor

Copiar um banco de dados do SQL no mesmo servidor com o [New-AzureRmSqlDatabaseCopy] (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx) cmdlet. Definir o `-CopyServerName` e `-CopyResourceGroupName` para os mesmos valores seu grupo de recursos e de servidor de banco de dados fonte.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para obter mais informações, consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md). Para um exemplo de script, consulte [Copiar um banco de dados do SQL script do PowerShell](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Excluir um banco de dados do SQL

Excluir um banco de dados do SQL com o [remover-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619368(v=azure.300\).aspx) cmdlet. O grupo de recursos, o servidor e o banco de dados já devem existir em sua assinatura.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Excluir um servidor de banco de dados SQL

Excluir um servidor com o [remover-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603488(v=azure.300\).aspx) cmdlet.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Criar e gerenciar pools elástica banco de dados usando o PowerShell

Para obter detalhes sobre como criar pools elástica banco de dados usando o PowerShell, consulte [criar um novo banco de dados elástica pool com o PowerShell](sql-database-elastic-pool-create-powershell.md).

Para obter detalhes sobre como gerenciar pools elástica banco de dados usando o PowerShell, consulte [Monitor e gerenciar um pool de banco de dados elástica com PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Informações relacionadas

- [Cmdlets de banco de dados do SQL azure] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Referência de Cmdlet azure] (https://msdn.microsoft.com/library/azure/dn708514(v=azure.300\).aspx)
