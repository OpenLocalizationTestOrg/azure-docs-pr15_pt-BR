<properties
    pageTitle="Usar o PowerShell para fazer backup e restaurar aplicativos de serviço de aplicativo"
    description="Aprenda a usar o PowerShell para fazer backup e restaurar um aplicativo no serviço de aplicativo do Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Usar o PowerShell para fazer backup e restaurar aplicativos de serviço de aplicativo

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Aprenda a usar o PowerShell do Azure para fazer backup e restaurar [aplicativos de serviço de aplicativo](https://azure.microsoft.com/services/app-service/web/). Para obter mais informações sobre backups do aplicativo da web, incluindo requisitos e restrições, consulte [fazer backup de um aplicativo web no serviço de aplicativo do Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o PowerShell para gerenciar os backups de aplicativo, você precisa do seguinte:

- **A URL de SAS** que permite o acesso de leitura e gravação a um contêiner de armazenamento do Azure. Consulte [Compreendendo o modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para obter uma explicação de URLs de SAS. Consulte [Usando o PowerShell do Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md) para obter exemplos de gerenciamento de armazenamento do Azure usando o PowerShell.
- **A cadeia de conexão de banco de dados** se você quiser fazer backup de um banco de dados junto com seu aplicativo web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Como gerar uma URL de SAS para usar com os cmdlets de backup de aplicativo web
Uma URL de SAS pode ser gerada com o PowerShell. Aqui está um exemplo de como gerar um que pode ser usado com os cmdlets abordados neste artigo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Instalar o Azure PowerShell 1.3.2 ou maior

Consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-install-configure.md) para obter instruções sobre como instalar e usar o PowerShell do Azure.

## <a name="create-a-backup"></a>Criar um backup

Use o cmdlet New-AzureRmWebAppBackup para criar um backup de um aplicativo web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Isso cria um backup com um nome gerado automaticamente. Se você quiser fornecer um nome para o backup, use o parâmetro opcional BackupName.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Para incluir um banco de dados no backup, primeiro crie uma configuração de backup do banco de dados usando o cmdlet New-AzureRmWebAppDatabaseBackupSetting, em seguida, fornecer essa configuração no parâmetro bancos de dados do cmdlet New-AzureRmWebAppBackup. O parâmetro de bancos de dados aceita uma matriz de configurações de banco de dados, permitindo que você faça backup mais de um banco de dados.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obter backups

O cmdlet Get-AzureRmWebAppBackupList retorna uma matriz de todos os backups para um aplicativo web. Você deve fornecer o nome do aplicativo web e seu grupo de recursos.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obter um backup específico, use o cmdlet Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Você também pode enviar um objeto de aplicativo da web em qualquer um dos cmdlets de gerenciamento de backup para sua conveniência.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Agendar backups automáticos

Você pode agendar backups ocorram automaticamente em um intervalo especificado. Para configurar um agendamento de backup, use o cmdlet AzureRmWebAppBackupConfiguration editar. Esse cmdlet leva vários parâmetros:

- **Nome** - o nome do aplicativo web.
- **ResourceGroupName** - o nome do grupo de recursos que contém o web app.
- **Slot** - opcional. O nome do slot do aplicativo web.
- **StorageAccountUrl** - a URL de SAS para o contêiner de armazenamento do Azure usado para armazenar os backups.
- **FrequencyInterval** - valor numérico para frequência os backups devem ser feitos. Deve ser um inteiro positivo.
- **FrequencyUnit** - unidade de tempo para frequência os backups devem ser feitos. Opções estão hora e dia.
- **RetentionPeriodInDays** - quantos dias os backups automáticos devem ser salvas antes de serem automaticamente excluídos.
- **Hora de início** - opcional. A hora em que os backups automáticos devem começar. Backups começam imediatamente se for nulo. Deve ser um DateTime.
- **Bancos de dados** - opcional. Uma matriz de DatabaseBackupSettings para os bancos de dados para backup.
- **KeepAtLeastOneBackup** - opcional trocados parâmetro. Fornecer este se um backup sempre deve ser mantido na conta de armazenamento, independentemente de como antigo é.

A seguir é um exemplo de como usar esse cmdlet.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Para obter o agendamento de backup atual, use o cmdlet Get-AzureRmWebAppBackupConfiguration. Isso pode ser útil para modificar uma agenda que já foi configurada.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Restaurar um aplicativo web de um backup

Para restaurar um aplicativo web a partir de um backup, use o cmdlet AzureRmWebAppBackup de restauração. A maneira mais fácil de usar esse cmdlet é a barra vertical em um objeto de backup recuperado o cmdlet Get-AzureRmWebAppBackup ou o cmdlet Get-AzureRmWebAppBackupList.

Quando você tiver um objeto de backup, você pode inseri-lo para o cmdlet AzureRmWebAppBackup de restauração. Especifique o parâmetro de opção de substituir para indicar que você pretende substituir o conteúdo de seu aplicativo web com o conteúdo do backup. Se o backup contém bancos de dados, os bancos de dados são restaurados também.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

A seguir é um exemplo de como usar a restauração-AzureRmWebAppBackup especificando todos os parâmetros.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Excluir um backup

Para excluir um backup, use o cmdlet Remove-AzureRmWebAppBackup. Isso remove o backup da sua conta de armazenamento. Especifique o nome do aplicativo, seu grupo de recursos e a ID do backup que você deseja excluir.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Você também pode enviar um objeto de backup para o cmdlet AzureRmWebAppBackup remover para excluí-la.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
