<properties
    pageTitle="Importar um arquivo BACPAC para criar um banco de dados do SQL Azure usando o PowerShell | Microsoft Azure"
    description="Importar um arquivo BACPAC para criar um banco de dados do SQL Azure usando o PowerShell"
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
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importar um arquivo BACPAC para criar um banco de dados do SQL Azure usando o PowerShell

**Banco de dados único**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artigo fornece instruções para criar um banco de dados do SQL Azure importando um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) com o PowerShell.

O banco de dados é criado a partir de um arquivo BACPAC (.bacpac) importado de um contêiner de blob do armazenamento do Azure. Se você não tiver um arquivo BACPAC no armazenamento do Azure, consulte [arquivamento um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell](sql-database-export-powershell.md). Se você já tiver um arquivo BACPAC que não esteja no armazenamento do Azure, [use AzCopy facilmente carregá-la à sua conta de armazenamento do Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Banco de dados do SQL Azure automaticamente cria e mantém backups para cada banco de dados do usuário que você pode restaurar. Para obter detalhes, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md).


Para importar um banco de dados do SQL, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Avaliação gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Um arquivo BACPAC do banco de dados que você deseja importar. O BACPAC precisa estar em um contêiner de blob de [conta de armazenamento do Azure](../storage/storage-create-storage-account.md) .



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Configurar as variáveis para seu ambiente

Há algumas variáveis onde você precisa substituir os valores de exemplo com os valores específicos para seu banco de dados e sua conta de armazenamento.

O nome do servidor deve ser um servidor que existe atualmente na assinatura selecionada na etapa anterior. Ele deve ser o banco de dados a ser criado no servidor. Não há suporte para a importação de um banco de dados diretamente em um pool de Elástico. Mas você pode importar primeiro para um único banco de dados e em seguida, mova o banco de dados em um pool.

O nome do banco de dados é o nome desejado para o novo banco de dados.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


As seguintes variáveis são da conta de armazenamento onde seu BACPAC está localizado. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para acessar esses valores. Você pode encontrar a chave primária de acesso clicando em **todas as configurações** e, em seguida, **chaves** da lâmina da sua conta de armazenamento.

O nome de blob é o nome de um arquivo BACPAC existente que você deseja criar o banco de dados. Você precisa incluir a extensão .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Executando o [Get-Credential] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) o cmdlet abre uma janela pedir seu nome de usuário e senha. Insira o logon de administrador e a senha para o servidor de banco de dados SQL ($ServerName do acima) e não o nome de usuário e senha da sua conta do Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importar o banco de dados

Este comando envia uma solicitação de banco de dados de importação para o serviço. Dependendo do tamanho do seu banco de dados, a operação de importação pode levar algum tempo para ser concluída.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Monitorar o progresso da operação

Após a execução [New-AzureRmSqlDatabaseImport] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), você pode verificar o status da solicitação executando o [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Importar script do PowerShell do banco de dados SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Próximas etapas

- Para saber se conectem ao e um banco de dados importados do SQL da consulta, consulte [conectar ao banco de dados do SQL com o SQL Server Management Studio e executar um exemplo de consulta de T-SQL](sql-database-connect-query-ssms.md)
