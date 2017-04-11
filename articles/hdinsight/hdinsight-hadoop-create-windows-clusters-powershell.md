<properties
   pageTitle="Criar clusters baseados no Windows Hadoop em HDInsight usando o PowerShell do Azure | Microsoft Azure"
    description="Aprenda a criar clusters para Azurehdinsight usando o PowerShell do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados no Windows Hadoop em HDInsight usando o PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a criar clusters HDInsight usando o PowerShell do Azure. PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure com o Windows PowerShell. Para a criação de cluster outros recursos e ferramentas clique em selecionar a guia na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar as instruções neste artigo, você deve ter o seguinte:

- Assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PowerShell Azure.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
PowerShell Azure é um poderoso ambiente de script que você pode usar para controlar e automatizar a implantação e gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como criar um cluster de HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar cmdlets do HDInsight Windows PowerShell, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Para obter mais informações sobre como usar o PowerShell do Azure com HDInsight, consulte [administrar HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para a lista de cmdlets do Windows PowerShell do HDInsight, consulte [referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Os procedimentos a seguir são necessárias para criar um cluster de HDInsight usando o Azure PowerShell:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Criar clusters usando o modelo ARM

Você pode usar o PowerShell do Azure para implantar um modelo ARM que cria um cluster de HDInsight.  Consulte [chamada modelos usando o PowerShell do Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personalizar clusters

- Consulte [Personalizar HDInsight clusters usando inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clusters baseados no Windows personalizar HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster de HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Saiba como começar a trabalhar com seu cluster HDInsight
* [Enviar Hadoop trabalhos programaticamente](hdinsight-submit-hadoop-jobs-programmatically.md) - Aprenda a enviar programaticamente trabalhos ao HDInsight
* [Gerenciar Hadoop clusters em HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md) - aprender a trabalhar com HDInsight usando o PowerShell do Azure
* [Documentação do Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -Descubra o SDK do HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
