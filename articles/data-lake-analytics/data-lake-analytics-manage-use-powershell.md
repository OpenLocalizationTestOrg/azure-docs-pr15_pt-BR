<properties 
   pageTitle="Gerenciar Azure dados Lucerne análise usando o PowerShell do Azure | Azure" 
   description="Saiba como gerenciar trabalhos de dados Lucerne Analytics, fontes de dados, os usuários. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerenciar Azure dados Lucerne análise usando o PowerShell do Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas de análise de Lucerne de dados do Azure, fontes de dados, os usuários e trabalhos usando o PowerShell do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique em Selecionar guia acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##<a name="install-azure-powershell-10-or-greater"></a>Instalar o Azure PowerShell 1.0 ou maior

Consulte a seção pré-requisito [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](powershell-azure-resource-manager.md#prerequisites).
    
## <a name="manage-accounts"></a>Gerenciar contas

Antes de executar qualquer trabalhos de análise de Lucerne de dados, você deve ter uma conta de dados Lucerne Analytics. Ao contrário de Azurehdinsight, você não pagar por uma conta de análise quando ele não estiver executando um trabalho.  Você paga somente a hora quando um trabalho está funcionando.  Para obter mais informações, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Criar contas

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"
    
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
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName
    
    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

Você também pode usar um modelo de grupo de recursos do Azure. É um modelo para criar uma conta de dados Lucerne Analytics e a conta de armazenamento de Lucerne dados dependente no [Apêndice A](#appendix-a). Salvar o modelo para um arquivo com .json modelo e use o seguinte script do PowerShell para chamá-lo:


    $AzureSubscriptionID = "<Your Azure Subscription ID>"
    
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
    
    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
    
    Login-AzureRmAccount
    
    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
    
    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###<a name="list-account"></a>Conta de lista

Contas de análise de lista dados Lucerne na assinatura atual

    Get-AzureRmDataLakeAnalyticsAccount
    
A saída:

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

Contas de análise de lista dados Lucerne dentro de um grupo de recursos específicos

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obter detalhes de uma conta de dados Lucerne Analytics específica

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Testar a existência de uma conta de dados Lucerne Analytics específica

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

O cmdlet retornará **True** ou **False**.

###<a name="delete-data-lake-analytics-accounts"></a>Excluir dados Lucerne Analytics contas

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Conta de análise de excluir dados Lucerne não irá excluir a conta de armazenamento de Lucerne dados dependente. O exemplo a seguir exclui a conta de dados Lucerne Analytics e a conta de armazenamento de Lucerne de dados padrão

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar fontes de dados de conta

Dados Lucerne Analytics atualmente oferece suporte a fontes de dados a seguir:

- [Armazenamento de Lucerne de dados do Azure](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento do Azure](storage-introduction.md)

Quando você cria uma conta de análise, você deve designar uma conta de armazenamento de Lucerne de dados do Azure para ser a conta de armazenamento padrão. A conta de armazenamento de dados de Lucerne padrão é usada para armazenar os logs de auditoria de trabalho e metadados de trabalho. Após ter criado uma conta de análise, você pode adicionar contas adicionais de armazenamento de Lucerne de dados e/ou conta de armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localize a conta de armazenamento de Lucerne de dados padrão

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Adicionar contas adicionais de armazenamento de Blob do Azure

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar contas adicionais de armazenamento de Lucerne de dados

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Lista de fontes de dados:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
    


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gerenciar trabalhos

Você deve ter uma conta de dados Lucerne Analytics antes de criar um trabalho.  Para obter mais informações, consulte [Gerenciar a análise de dados Lucerne contas](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Lista de trabalhos

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"   

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Obter detalhes de trabalho

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
    
### <a name="submit-jobs"></a>Enviar trabalhos

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [AZURE.NOTE] A prioridade padrão de um trabalho é 1000 e o grau de padrão de paralelismo para um trabalho é 1.


### <a name="cancel-jobs"></a>Cancelar trabalhos

    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Gerenciar itens de catálogo

O catálogo de U-SQL é usado para estruturar dados e código, para que eles podem ser compartilhados por scripts U-SQL. O catálogo permite o melhor desempenho possível com dados em Lucerne de dados do Azure. Para obter mais informações, consulte [Catálogo de uso U-SQL](data-lake-analytics-use-u-sql-catalog.md).

###<a name="list-catalog-items"></a>Itens de catálogo de lista

    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database
    
    
    
    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

###<a name="get-catalog-item-details"></a>Obter detalhes do item de catálogo 

    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"
    
    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

###<a name="test-existence-of--catalog-item"></a>Testar a existência de item de catálogo

    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

###<a name="create-catalog-secret"></a>Criar segredo de catálogo
    New-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")

### <a name="modify-catalog-secret"></a>Modificar segredo de catálogo
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



###<a name="delete-catalog-secret"></a>Excluir segredo de catálogo
    Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master"


## <a name="use-azure-resource-manager-groups"></a>Usar grupos de Gerenciador de recursos do Azure

Aplicativos geralmente são compostos de muitos componentes, por exemplo um aplicativo web, banco de dados, servidor de banco de dados, armazenamento e 3º serviços de terceiros. Azure Resource Manager (ARM) permite que você trabalhe com os recursos em seu aplicativo como um grupo, chamado de um grupo de recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do aplicativo em uma operação única e coordenado. Usar um modelo para implantação e esse modelo pode servir para diferentes ambientes como teste, teste e produção. Você pode esclarecer cobrança para sua organização exibindo os custos acumulados para o grupo inteiro. Para obter mais informações, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de dados Lucerne Analytics pode incluir os seguintes componentes:

- Conta de análise Lucerne de dados do Azure
- Conta de armazenamento do Azure dados Lucerne padrão necessários
- Contas de armazenamento Lucerne de dados do Azure adicionais
- Contas adicionais de armazenamento do Azure

Você pode criar todos esses componentes em um grupo ARM para torná-las mais fácil de gerenciar.

![Conta de análise Lucerne de dados do azure e de armazenamento](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta de dados Lucerne análise e as contas de armazenamento dependentes devem ser colocadas no mesmo Azure data center.
O grupo de BRAÇO Entretanto pode estar localizado em uma central de dados diferentes.  

##<a name="see-also"></a>Consulte também 

- [Visão geral de análise de Lucerne de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Começar a usar a análise de Lucerne dados usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorar e solucionar problemas de trabalhos de análise de Lucerne de dados do Azure usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##<a name="appendix-a---data-lake-analytics-arm-template"></a>Apêndice A - modelo de dados Lucerne Analytics BRAÇO

O modelo ARM seguinte pode ser usado para implantar uma conta de dados Lucerne Analytics e sua conta de armazenamento de Lucerne dados dependente.  Salvá-lo como um arquivo de json e, em seguida, use o script do PowerShell para chamar o modelo. Para obter mais informações, consulte [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md)e [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](../resource-group-template-deploy.md#deploy-with-powershell) .

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }

