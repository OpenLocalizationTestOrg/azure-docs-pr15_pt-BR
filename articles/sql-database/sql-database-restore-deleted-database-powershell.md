<properties
    pageTitle="Restaurar um banco de SQL Azure excluídos (PowerShell) | Microsoft Azure"
    description="Restaure um banco de SQL Azure excluídos (PowerShell)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>Restaurar um banco de dados do SQL Azure excluído usando o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restaurar excluídos DB: Portal](sql-database-restore-deleted-database-portal.md)
- [**Restaurar excluídos DB: PowerShell**](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="get-a-list-of-deleted-databases"></a>Obter uma lista de bancos de dados excluídos

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-standalone-database"></a>Restaurar o banco de dados excluído em um banco de dados autônomo

Obtenha o backup do banco de dados excluídos que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) . Inicie a restauração do backup do banco de dados excluídos usando o cmdlet [AzureRmSqlDatabase de restauração](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-database-pool"></a>Restaurar o banco de dados excluído em um pool de banco de dados elástica

Obtenha o backup do banco de dados excluídos que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) . Inicie a restauração do backup do banco de dados excluídos usando o cmdlet [AzureRmSqlDatabase de restauração](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
