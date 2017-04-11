<properties 
   pageTitle="Introdução ao Azure dados Lucerne análise usando o PowerShell do Azure | Azure" 
   description="Saiba como usar o PowerShell do Azure para criar uma conta de armazenamento de Lucerne de dados, criar um trabalho de dados Lucerne análise usando U-SQL e enviar o trabalho. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: Introdução ao Azure dados Lucerne análise usando o PowerShell do Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o PowerShell do Azure para criar contas de análise de Lucerne de dados do Azure, definir trabalhos de dados Lucerne Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos a contas de dados Lucerne analítica. Para obter mais informações sobre a análise de dados Lucerne, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

Neste tutorial, você irá desenvolver um trabalho que lê uma guia do arquivo de valores (TSV) separados e converte-o em um arquivo CSV (valores) de separados por vírgulas. Para percorrer o tutorial mesmo usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **Uma estação de trabalho com o PowerShell do Azure**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Criar conta de dados Lucerne Analytics

Você deve ter uma conta de dados Lucerne Analytics antes de poder executar todos os trabalhos. Para criar uma conta de dados Lucerne Analytics, especifique o seguinte:

- **Grupo de recursos do Azure**: A dados Lucerne Analytics conta deve ser criada em um grupo de recursos do Azure. [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permite que você trabalhe com os recursos em seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma operação única e coordenado.  

    Enumerar os grupos de recursos em sua assinatura:
    
        Get-AzureRmResourceGroup
    
    Para criar um novo grupo de recursos:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nome de conta de análise de dados Lucerne**
- **Local**: um dos Azure data centers que ofereça suporte a análise de dados Lucerne.
- **Conta padrão dados Lucerne**: cada conta dados Lucerne Analytics tem uma conta de dados Lucerne padrão.

    Para criar uma nova conta de Lucerne de dados:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] O nome da conta Lucerne de dados deve conter apenas letras minúsculas e números.



**Para criar uma conta de dados Lucerne Analytics**

1. Abra o PowerShell ISE de sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Carregar dados Lucerne de dados

Neste tutorial, você irá processar alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no repositório de dados Lucerne ou armazenamento de Blob do Azure. 

Um arquivo de log de pesquisa de amostra foi copiado para um público contêiner de Blob do Azure. Use o seguinte script do PowerShell para baixar o arquivo sua estação de trabalho e carregue o arquivo para a conta de armazenamento de Lucerne de dados padrão da sua conta de dados Lucerne Analytics.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

O seguinte script do PowerShell mostra como obter o nome de armazenamento de Lucerne de dados padrão para uma conta de análise de Lucerne de dados:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Portal do Azure fornece uma interface de usuário para copiar os arquivos de dados de exemplo para a conta de armazenamento de Lucerne de dados padrão. Para obter instruções, consulte [Introdução ao Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Lucerne a análise de dados também pode acessar o armazenamento de Blob do Azure.  Para carregar dados para o armazenamento de Blob do Azure, consulte [Usando o PowerShell do Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Enviar dados Lucerne Analytics trabalhos

Os trabalhos de dados Lucerne Analytics são gravados na linguagem SQL U. Para saber mais sobre U-SQL, consulte [referência de linguagem de U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348)e [começar a usar linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) .

**Para criar um script de trabalho de dados Lucerne Analytics**

- Criar um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**e, então, cria um arquivo csv usando **Outputters.Csv()**. 
    
    Não modifique os dois caminhos, a menos que você copia o arquivo de origem para um local diferente.  A análise de dados Lucerne criará a pasta de saída, se ele não existir.
    
    É mais simples usar caminhos relativos para arquivos armazenavam em default dados contas Lucerne. Você também pode usar caminhos absolutos.  Por exemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Você deve usar caminhos absolutos para acessar arquivos em contas de armazenamento vinculadas.  A sintaxe para arquivos armazenados em conta vinculada de armazenamento do Azure é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contêiner de Blob Azure com permissões de acesso de contêineres público ou públicos blobs não são suportados no momento.    
    
    
**Para enviar o trabalho**

1. Abra o PowerShell ISE de sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    No script, o arquivo de script U-SQL é armazenado em c:\tutorials\data-lake-analytics\copyFile.usql. Atualize o caminho do arquivo de acordo.
 
Depois que o trabalho for concluído, você pode usar os seguintes cmdlets para o arquivo de lista e baixar o arquivo:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial usando outras ferramentas, clique em seletores de guia no topo da página.
- Para ver uma consulta mais complexa, consulte [logs de site de análise usando análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [scripts de desenvolver U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U-SQL, consulte [Introdução ao idioma do Azure dados Lucerne Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Tarefas de gerenciamento, consulte [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral de análise de Lucerne de dados, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

