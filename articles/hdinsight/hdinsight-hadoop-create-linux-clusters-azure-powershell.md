<properties
    pageTitle="Criar clusters Hadoop, HBase, tempestade ou Spark no Linux em HDInsight usando o PowerShell do Azure | Microsoft Azure"
    description="Aprenda a criar clusters Hadoop, HBase, tempestade ou Spark no Linux para HDInsight usando o PowerShell do Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Criar clusters baseados em Linux em HDInsight usando o PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

PowerShell Azure é um poderoso ambiente de script que você pode usar para controlar e automatizar a implantação e gerenciamento de suas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster de HDInsight baseados em Linux usando o PowerShell do Azure. Ele também inclui um script de exemplo.

> [AZURE.NOTE] PowerShell Azure só está disponível em clientes do Windows. Se você estiver usando um cliente Linux, Unix ou Mac OS X, consulte [criar um cluster de HDInsight baseados em Linux usando CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obter informações sobre como usar a CLI do Azure para criar um cluster.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte antes de iniciar este procedimento:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- PowerShell Azure.
    Para obter mais informações sobre como usar o PowerShell do Azure com HDInsight, consulte [administrar HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para a lista de cmdlets do HDInsight Windows PowerShell, consulte [referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster de HDInsight usando o PowerShell do Azure, você deve concluir os procedimentos a seguir:

- Criar um grupo de recursos do Azure
- Criar uma conta de armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Criar um cluster de HDInsight

Os dois parâmetros mais importantes que devem ser definidas para criar clusters Linux são aquelas que especificam o tipo de sistema operacional e os detalhes do usuário SSH:

- Verifique se que você especificar o parâmetro **- OSType** como **Linux**.
- Para usar SSH sessões remotas em clusters, você pode especificar a senha do usuário SSH ou a chave pública SSH. Se você especificar a senha do usuário SSH e a chave pública SSH, a chave será ignorada. Se você quiser usar a chave SSH para sessões remotas, especifique uma senha SSH em branco quando solicitado por um. Para obter mais informações sobre como usar SSH com HDInsight, consulte um dos seguintes artigos:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

O seguinte script demonstra como criar um novo cluster:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Os valores especificados para **$clusterCredentials** são usados para criar a conta de usuário do Hadoop para o cluster. Use essa conta para conectar-se ao cluster.

Os valores especificados para **$sshCredentials** são usados para criar o usuário SSH para o cluster. Use essa conta para iniciar uma sessão remota do SSH no cluster e executar trabalhos.

> [AZURE.IMPORTANT] Nesse script, especifique o número de nós de trabalho que estarão no cluster. Se você planeja usar mais de 32 nós de trabalho (ou na criação de cluster ou dimensionando cluster após a criação), você também deve especificar um tamanho de nó principal com pelo menos 8 cores e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nó e os custos associados, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um cluster.

O exemplo a seguir demonstra como adicionar uma conta de armazenamento adicional:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personalizar clusters

- Consulte [Personalizar HDInsight clusters usando inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clusters baseados no Windows personalizar HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster de HDInsight, use os recursos a seguir para aprender a trabalhar com seu cluster.

### <a name="hadoop-clusters"></a>Hadoop clusters

* [Use a seção com HDInsight](hdinsight-use-hive.md)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase em HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para HBase em HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters de tempestade

* [Desenvolva topologias Java para tempestade em HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python em tempestade em HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark clusters

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster de Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com aprendizado de máquina: Spark de uso em HDInsight prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: Uso Spark no HDInsight para criar aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
