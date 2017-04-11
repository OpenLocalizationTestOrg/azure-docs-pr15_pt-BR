<properties
    pageTitle="Restaurar um banco de dados do SQL Azure de um backup geográfica redundantes (PowerShell) | Microsoft Azure"
    description="Restaurar um banco de dados do SQL Azure em um novo servidor de um backup geográfica redundantes"
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

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurar um banco de dados do SQL Azure de um backup geográfica redundante usando o PowerShell


> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Geográfica restauração: Portal Azure](sql-database-geo-restore-portal.md)

Este artigo mostra como restaurar o banco de dados em um novo servidor usando restauração localização geográfica. Isso pode ser feito através do PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Localização geográfica-restaurar seu banco de dados em um banco de dados autônomo

1. Obtenha o backup redundantes de localização geográfica do seu banco de dados que você deseja restaurar usando o [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar a restauração do backup geográfica redundante usando o [restauração-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Localização geográfica-restaurar seu banco de dados em um pool de banco de dados elástica

1. Obtenha o backup redundantes de localização geográfica do seu banco de dados que você deseja restaurar usando o [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar a restauração do backup geográfica redundante usando o [restauração-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet. Especifique o nome do pool que você deseja restaurar o banco de dados em.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md).
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md).
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md).
- Para saber sobre as opções de recuperação mais rápidas, consulte [Replicação de localização geográfica ativo](sql-database-geo-replication-overview.md).  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md).
