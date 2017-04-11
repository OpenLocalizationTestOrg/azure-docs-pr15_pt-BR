<properties
   pageTitle="Criar clusters baseados no Windows Hadoop em HDInsight usando o SDK do .NET | Microsoft Azure"
    description="Aprenda a criar clusters de HDInsight para Azurehdinsight usando o SDK do .NET."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-net-sdk"></a>Criar clusters baseados no Windows Hadoop em HDInsight usando o SDK do .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]


Aprenda a criar clusters HDInsight usando o SDK do .NET. Para a criação de cluster outros recursos e ferramentas clique em selecionar a guia na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Visual Studio 2013 ou 2015.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters

O SDK do .NET HDInsight fornece bibliotecas de cliente .NET que tornam mais fácil trabalhar com HDInsight de um aplicativo do .NET Framework. Siga as instruções abaixo para criar um aplicativo de console do Visual Studio e cole o código para criar um cluster.

O aplicativo requer um grupo de recursos do Azure e a conta de armazenamento padrão.  O [Apêndice A](#appx-a-create-dependent-components) fornece um script PowerShell para criar os componentes dependentes.

**Para criar um aplicativo de console do Visual Studio**

1. Crie um novo aplicativo de console em c# no Visual Studio.
2. Execute o seguinte comando Nuget no console de gerenciamento de pacote do Nuget.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

6. Do Solution Explorer, clique duas vezes em **Program.cs** para abri-lo, cole o seguinte código e forneça os valores para as variáveis:

        using System;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Net.Http;
        
        namespace CreateHDInsightCluster
        {
            class Program
            {
                // The client for managing HDInsight
                private static HDInsightManagementClient _hdiManagementClient;
                // Replace with your AAD tenant ID if necessary
                private const string TenantId = UserTokenProvider.CommonTenantId; 
                private const string SubscriptionId = "<Your Azure Subscription ID>";
                // This is the GUID for the PowerShell client. Used for interactive logins in this example.
                private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
                private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
                private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Default Blob Container Name>";
                private const string NewClusterName = "<HDInsight Cluster Name>";
                private const int NewClusterNumWorkerNodes = 2;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOsType = OSType.Windows;
                private const string NewClusterType = "Hadoop";
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<HTTP User password>";
                

        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");
        
                    // Authenticate and get a token
                    var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                    // Flag subscription for HDInsight, if it isn't already.
                    EnableHDInsight(authToken);
                    // Get an HDInsight management client
                    _hdiManagementClient = new HDInsightManagementClient(authToken);

                    // Set parameters for the new cluster
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumWorkerNodes,
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        Location = NewClusterLocation,
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOsType
                    };
                    // Create the cluster
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                /// <summary>
                /// Authenticate to an Azure subscription and retrieve an authentication token
                /// </summary>
                /// <param name="TenantId">The AAD tenant ID</param>
                /// <param name="ClientId">The AAD client ID</param>
                /// <param name="SubscriptionId">The Azure subscription ID</param>
                /// <returns></returns>
                static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
                    var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                        ClientId, 
                        new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                        PromptBehavior.Always, 
                        UserIdentifier.AnyUser);
                    return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
                }
                /// <summary>
                /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
                /// </summary>
                /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
                /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
                /// <param name="authToken">An authentication token for your Azure subscription</param>
                static void EnableHDInsight(TokenCloudCredentials authToken)
                {
                    // Create a client for the Resource manager and set the subscription ID
                    var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
                    resourceManagementClient.SubscriptionId = SubscriptionId;
                    // Register the HDInsight provider
                    var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
                }
            }
        }

7. Pressione **F5** para executar o aplicativo. Uma janela do console deve abrir e exibir o status do aplicativo. Você também será solicitado a inserir suas credenciais de conta do Microsoft Azure. Pode levar alguns minutos para criar um cluster de HDInsight.



##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster de HDInsight. Para saber mais, consulte os seguintes artigos:

- [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Saiba como começar a trabalhar com seu cluster HDInsight
- [Executar trabalhos de seção em HDInsight usando o SDK do .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
- [Executar porco trabalhos em HDInsight usando o SDK do .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md)
- [Executar Sqoop trabalhos em HDInsight usando o SDK do .NET](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)
- [Executar trabalhos de Oozie em HDInsight](hdinsight-use-oozie.md)
- [Documentação do Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -Descubra o SDK do HDInsight

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx


##<a name="appx-a-create-dependent-components"></a>Componentes dependentes de criar AppX-A

O PowerShell Azure seguinte script pode ser usar para criar os componentes dependentes necessários pelo aplicativo .NET neste tutorial.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    Write-Host "Use the following names in your .NET application" -ForegroundColor Green
    Write-host "Resource Group Name: $resourceGroupName"
    Write-host "Default Storage Account Name: $defaultStorageAccountName"
    Write-host "Default Storage Account Key: $defaultStorageAccountKey"
    Write-host "Default Blob Container Name: $defaultBlobContainerName"
