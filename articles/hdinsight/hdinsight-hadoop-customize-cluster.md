<properties
    pageTitle="Personalizar HDInsight Clusters usando ações de script | Microsoft Azure"
    description="Saiba como personalizar clusters HDInsight usando a ação de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personalizar clusters baseados no Windows HDInsight usando a ação de Script

**Ação de script** pode ser usado para chamar [scripts personalizados](hdinsight-hadoop-script-actions.md) durante o processo de criação de cluster para instalar o software adicional em um cluster.

As informações neste artigo são específicas para clusters HDInsight baseados no Windows. Para clusters baseados em Linux, consulte [clusters baseados em Personalizar Linux HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight clusters podem ser personalizadas em uma variedade de outras maneiras, como incluindo contas adicionais de armazenamento do Azure, alterando o Hadoop arquivos de configuração (core site.xml, seção site.xml, etc.) ou adicionando compartilhado bibliotecas (por exemplo, seção, Oozie) em locais comuns no cluster. Essas personalizações podem ser feitas através do PowerShell do Azure, o SDK do Azure HDInsight .NET ou o Portal do Azure. Para obter mais informações, consulte [Hadoop criar clusters em HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Ação de script do processo de criação de cluster

Ação de script só é usada enquanto um clusters está sendo criada. O diagrama a seguir ilustra quando a ação de Script é executada durante o processo de criação:

![Personalização de cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

Quando o script é executado, o cluster insere o estágio de **ClusterCustomization** . Neste estágio, o script é executado na conta de administrador do sistema, em paralelo em todos os nós especificado no cluster e fornece os privilégios de administrador completo em nós.

> [AZURE.NOTE] Porque você tem privilégios de administrador em nós do cluster durante o estágio de **ClusterCustomization** , você pode usar o script para realizar operações como parar e iniciar serviços, incluindo serviços relacionados a Hadoop. Portanto, como parte do script, você deve garantir que os serviços de Ambari e outros serviços relacionados Hadoop estejam em execução antes do script terminar a execução. Esses serviços são necessários para verificar com êxito a integridade e o estado do cluster enquanto ele está sendo criado. Se você alterar qualquer configuração no cluster que afeta esses serviços, você deve usar as funções do auxiliar que são fornecidas. Para obter mais informações sobre funções de auxiliar, consulte [scripts de ação de Script desenvolver para HDInsight][hdinsight-write-script].

A saída e os logs de erro do script são armazenados na conta de armazenamento padrão especificado para o cluster. Os logs estão armazenados em uma tabela com o nome **u < \cluster-name-fragment >< \time-stamp > setuplog**. Esses são os logs de agregação do script executado em todos os nós (nó principal e nós de operador) no cluster.

Cada cluster pode aceitar várias ações de script que são chamadas na ordem em que eles são especificados. Um script pode ser executado no nó principal, nós do trabalho ou ambos.

HDInsight fornece vários scripts para instalar os seguintes componentes em clusters de HDInsight:

Nome | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Consulte [instalação e uso ramo em clusters de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalação e uso R em clusters de HDInsight][hdinsight-install-r].
**Instalar o Solr** | https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte [instalação e uso Solr em HDInsight clusters](hdinsight-hadoop-solr-install.md).
- **Instalar o Giraph** | https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte [instalação e uso Giraph em HDInsight clusters](hdinsight-hadoop-giraph-install.md).
| **Pré-carregar bibliotecas de seção** | https://hdiconfigactions.blob.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Consulte [Adicionar seção bibliotecas em clusters de HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Scripts de chamada usando o Portal do Azure

**A partir do Portal do Azure**

1. Começar a criar um cluster, conforme descrito em [Hadoop criar clusters no HDInsight](hdinsight-provision-clusters.md#portal).
2. Em configuração opcional, para a lâmina de **Ações de Script** , clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Ação de Script de uso para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é invocado para personalizar o cluster. s</td></tr>
        <tr><td>Cabeça/trabalhador</td>
            <td>Especifica os nós (**cabeça** ou **trabalhador**) no qual o script de personalização é executado. </b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script.</td></tr>
    </table>

    Pressione ENTER para adicionar mais de uma ação de script para instalar vários componentes em cluster.

3. Clique em **Selecionar** para salvar a configuração de ação de script e prossiga com a criação de cluster.

## <a name="call-scripts-using-azure-powershell"></a>Scripts de chamada usando o PowerShell do Azure

Este seguinte script do PowerShell demonstra como instalar Spark em cluster de HDInsight com base do Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Para instalar outros softwares, você precisará substituir o arquivo de script no script:


Quando solicitado, digite as credenciais para o cluster. Pode levar alguns minutos antes que o cluster for criado.

## <a name="call-scripts-using-net-sdk"></a>Scripts de chamada usando o SDK do .NET 

O exemplo a seguir demonstra como instalar Spark em cluster de HDInsight com base do Windows. Para instalar outros softwares, você precisará substituir o arquivo de script no código.

**Para criar um cluster de HDInsight com Spark** 

1. Crie um aplicativo de console c# no Visual Studio.
2. No Console do Nuget Package Manager, execute o seguinte comando.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Use o seguinte usando instruções no arquivo Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Coloque o código da classe com o seguinte:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
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


4. Pressione **F5** para executar o aplicativo.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de código-fonte aberto usados em clusters de HDInsight
O serviço do Microsoft Azure HDInsight é uma plataforma flexível que permite criar aplicativos de grandes de dados na nuvem usando um ecossistema de tecnologias de código-fonte aberto formado em torno de Hadoop. Microsoft Azure fornece um nível geral de suporte para tecnologias de código-fonte aberto, conforme discutido na seção **Escopo de suporte** do <a href="http://azure.microsoft.com/support/faq/" target="_blank">site de perguntas Frequentes de suporte do Azure</a>. O serviço de HDInsight fornece um nível adicional de suporte para alguns dos componentes, conforme descrito abaixo.

Há dois tipos de componentes de código-fonte aberto que estão disponíveis no serviço de HDInsight:

- **Componentes internos** - esses componentes são pré-instalados em clusters de HDInsight e fornecer funcionalidade de núcleo do cluster. Por exemplo, ResourceManager fio COLORIDO, a linguagem de consulta de seção (HiveQL) e a biblioteca de Mahout pertencem a essa categoria. Uma lista completa de componentes de cluster está disponível no [Novidades nas versões Hadoop cluster fornecidas pelo HDInsight?](hdinsight-component-versioning.md) </a>.
- **Componentes personalizados** - você, como um usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

Componentes internos são totalmente suportados e Microsoft Support ajudarão a isolar e resolver problemas relacionados a esses componentes.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

O serviço de HDInsight oferece várias maneiras de usar componentes personalizados. Independentemente de como um componente é usado ou instalado no cluster, o mesmo nível de suporte se aplica. Abaixo está uma lista das maneiras mais comuns que componentes personalizados podem ser usados em clusters de HDInsight:

1. Envio do trabalho - Hadoop ou outros tipos de trabalhos que executar ou usam componentes personalizados pode ser enviado ao cluster.
2. Personalização de cluster - durante a criação de cluster, você pode especificar configurações adicionais e componentes personalizados que serão instalados em nós do cluster.
3. Exemplos - para populares componentes personalizados, Microsoft e outras pessoas podem fornecer exemplos de como esses componentes podem ser usados em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## <a name="develop-script-action-scripts"></a>Desenvolver scripts de ação de Script

Consulte [scripts de ação de Script desenvolver para HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Consulte também

- [Criar clusters Hadoop no HDInsight] [ hdinsight-provision-cluster] fornece instruções sobre como criar um cluster de HDInsight usando outras opções personalizadas.
- [Desenvolver scripts de ação de Script para HDInsight][hdinsight-write-script]
- [Instalar e usar Spark em clusters de HDInsight][hdinsight-install-spark]
- [Instalar e usar R em clusters de HDInsight][hdinsight-install-r]
- [Instalar e usar clusters Solr em HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e usar clusters Giraph em HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Estágios durante a criação de cluster"
