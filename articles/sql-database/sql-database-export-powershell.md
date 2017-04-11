<properties
    pageTitle="Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell"
    description="Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para arquivamento seu banco de dados do SQL Azure para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (armazenado no armazenamento de Blob do Azure) usando o PowerShell.

Quando você precisa criar um arquivo de um banco de dados do SQL Azure, você pode exportar o esquema de banco de dados e dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de .bacpac. Um arquivo BACPAC posteriormente pode ser armazenado em armazenamento de Blob do Azure ou em armazenamento local em um local no local. Ele também pode ser importado para o banco de dados do SQL Azure ou em uma instalação do SQL Server local.

**Considerações**

- Para um arquivo morto transacionalmente consistente, você não deve garantir que nenhuma gravação está ocorrendo uma atividade durante a exportação, ou que você está exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados do SQL Azure.
- O tamanho máximo de um arquivo BACPAC arquivado ao armazenamento de Blob do Azure é 200 GB. Para arquivar um arquivo BACPAC maior para o armazenamento local, use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com o Visual Studio e SQL Server. Você também pode [Baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente das ferramentas de dados do SQL Server para obter este utilitário.
- Não há suporte para arquivamento ao armazenamento de premium Azure usando um arquivo BACPAC.
- Se a operação de exportação exceder 20 horas, ela pode ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumente temporariamente o nível de serviço.
 - CESSAÇÃO todos ler e gravar atividade durante a exportação.
 - Use um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices agrupados, uma exportação pode falhar se demora mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir uma varredura de tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verifique se o *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [SHOW_STATISTICS DBCC](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPACs não devem ser usadas para backup e restaurar operações. Banco de dados do SQL Azure cria automaticamente backups para cada banco de dados do usuário. Para obter detalhes, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure.
- Um banco de dados do SQL Azure.
- Uma [conta de armazenamento padrão do Azure](../storage/storage-create-storage-account.md), com um contêiner de blob para armazenar a BACPAC em armazenamento padrão.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>Exportar seu banco de dados

O [New-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet envia uma solicitação de banco de dados de exportação para o serviço. Dependendo do tamanho do seu banco de dados, a operação de exportação pode levar algum tempo para ser concluída.

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC transacionalmente consistente, você deve primeiro [criar uma cópia de seu banco de dados](sql-database-copy-powershell.md)e exporte a cópia do banco de dados.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorar o progresso da operação de exportação

Após a execução [New-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx), você pode verificar o status da solicitação executando [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx). Executando isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: bem-sucedida** a exportação estiver concluída.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Exemplo de banco de dados do SQL de exportação

O exemplo a seguir exporta um banco de dados existente do SQL para uma BACPAC e mostra como verificar o status da operação de exportação.

Para executar o exemplo, há algumas variáveis, que você precisa substituir os valores específicos para sua conta de armazenamento e banco de dados. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter o nome da conta de armazenamento, nome de contêiner de blob e valor da chave. Você pode encontrar a chave clicando em **teclas de acesso** na sua blade de conta de armazenamento.

Substitua o seguinte `VARIABLE-VALUES` com valores para seus recursos específicos do Azure. O nome do banco de dados é o banco de dados existente que você deseja exportar.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>Próximas etapas

- Para saber como importar um banco de dados do SQL Azure usando o Powershell, consulte [Importar um BACPAC usando o PowerShell](sql-database-import-powershell.md).


## <a name="additional-resources"></a>Recursos adicionais

- [Novo AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)