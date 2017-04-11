<properties
    pageTitle="Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo (PowerShell) | Microsoft Azure"
    description="Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restauração no momento: Portal Azure](sql-database-point-in-time-restore-portal.md)

Este artigo mostra como restaurar o banco de dados para um ponto anterior no tempo de [backups automatizados do banco de dados SQL](sql-database-automated-backups.md). Você pode fazer isso usando o PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>Restaurar o banco de dados para um ponto no tempo como um banco de dados autônomo

1. Obtenha o banco de dados que você deseja restaurar usando o [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurar o banco de dados a um ponto no tempo usando o [restauração-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>Restaurar o banco de dados para um ponto no tempo em um pool de banco de dados elástica

1. Obtenha o banco de dados que você deseja restaurar usando o [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurar o banco de dados a um ponto no tempo usando o [restauração-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
