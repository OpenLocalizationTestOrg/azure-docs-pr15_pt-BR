<properties
   pageTitle="Restaurar um depósito de dados do SQL Azure (PowerShell) | Microsoft Azure"
   description="Tarefas do PowerShell para restaurar um depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar um depósito de dados do SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [RESTANTE][]

Neste artigo, você aprenderá como restaurar um depósito de dados do SQL Azure usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada depósito de dados do SQL é hospedado por um SQL server (por exemplo, myserver.database.windows.net) que possui uma cota DTU padrão.  Antes de você pode restaurar um depósito de dados do SQL, verifique se o SQL server tem cota suficiente DTU restante do banco de dados que está sendo restaurado. Para saber como calcular DTU necessário ou para solicitar mais DTU, consulte [solicitar uma alteração de cota DTU][].

### <a name="install-powershell"></a>Instale o PowerShell

Para poder usar o PowerShell do Azure com o SQL Data Warehouse, você precisará instalar o Azure PowerShell versão 1.0 ou posterior.  Você pode verificar sua versão executando **módulo de Get - ListAvailable-nome AzureRM**.  Pode ser instalada a versão mais recente do [Microsoft Web Platform Installer][].  Para obter mais informações sobre como instalar a versão mais recente, veja [como instalar e configurar o Azure PowerShell][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados a partir de um instantâneo use o cmdlet do PowerShell [AzureRmSqlDatabase de restauração][] .

1. Abrir o Windows PowerShell.
2. Conectar-se a sua conta do Azure e todas as assinaturas associadas a uma conta da lista.
3. Selecione a assinatura que contém o banco de dados a serem restaurados.
4. Liste os pontos de restauração do banco de dados.
5. Escolha o ponto de restauração desejado usando o RestorePointCreationDate.
6. Restaure o banco de dados para o ponto de restauração desejado.
7. Verifique se o banco de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Concluída a restauração, você pode configurar o seu banco de dados recuperado pela seguinte [configurar seu banco de dados após a recuperação][].


## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído, use o cmdlet [AzureRmSqlDatabase de restauração][] .

1. Abrir o Windows PowerShell.
2. Conectar-se a sua conta do Azure e todas as assinaturas associadas a uma conta da lista.
3. Selecione a assinatura que contém o banco de dados excluído a serem restaurados.
4. Obtenha o banco de dados excluído específico.
5. Restaure o banco de dados excluído.
6. Verifique se o banco de dados restaurado está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Concluída a restauração, você pode configurar o seu banco de dados recuperado pela seguinte [configurar seu banco de dados após a recuperação][].


## <a name="restore-from-an-azure-geographical-region"></a>Restaurar a partir de uma determinada área geográfica Azure

Para recuperar um banco de dados, use o cmdlet [AzureRmSqlDatabase de restauração][] .

1. Abrir o Windows PowerShell.
2. Conectar-se a sua conta do Azure e todas as assinaturas associadas a uma conta da lista.
3. Selecione a assinatura que contém o banco de dados a serem restaurados.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação do banco de dados.
6. Verifique o status do banco de dados restaurado localização geográfica.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Para configurar seu banco de dados após a conclusão da restauração, consulte [Configurar o seu banco de dados após a recuperação][]. 


O banco de dados recuperado serão habilitados para TDE se o banco de dados de origem é habilitado para TDE.


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Azure SQL Database, leia a [Visão geral de continuidade de negócios do Azure SQL Database][].

<!--Image references-->

<!--Article references-->
[Visão geral sobre o continuidade de negócios banco de dados do SQL Azure]: sql-database-business-continuity.md
[Solicitar uma alteração de cota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar seu banco de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Como instalar e configurar o PowerShell do Azure]: powershell-install-configure.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTANTE]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar seu banco de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restaurar-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
