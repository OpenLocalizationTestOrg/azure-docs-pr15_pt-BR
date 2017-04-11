<properties
    pageTitle="Gerenciar clusters Hadoop em HDInsight com o SDK do .NET | Microsoft Azure"
    description="Saiba como executar tarefas administrativas para clusters Hadoop no HDInsight usando o SDK do .NET HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gerenciar clusters Hadoop em HDInsight usando o SDK do .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como gerenciar clusters HDInsight usando [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).


**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Conectar ao Azure HDInsight

Você precisará os pacotes de Nuget a seguir:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

O exemplo de código a seguir mostra como conectar ao Azure antes de você pode administrar clusters HDInsight em sua assinatura do Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
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

Você deverá ver um aviso quando você executar este programa.  Se você não quiser ver o prompt, consulte [criar aplicativos de HDInsight .NET de autenticação não interativo](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Criar clusters

Consulte [baseados em Linux criar clusters em HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Clusters de lista

O trecho de código a seguir lista clusters e algumas propriedades:

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Excluir clusters

Use o trecho de código a seguir para excluir um cluster sincronia ou assíncrona: 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Clusters de escala
O cluster dimensionamento recurso permite que você altere o número de nós de trabalho usado por um cluster que é executado em Azurehdinsight sem precisar recriar o cluster.

>[AZURE.NOTE] Somente clusters com HDInsight versão 3.1.3 ou superior são suportadas. Se você não tiver certeza da versão do seu cluster, você pode verificar a página de propriedades.  Consulte [clusters de lista e mostrar](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

O impacto das alterando o número de nós de dados para cada tipo de cluster suportado pelo HDInsight:

- Hadoop

    Perfeita, você pode aumentar o número de nós de trabalho em um cluster de Hadoop que está em execução sem afetar os trabalhos em execução ou pendentes. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento normalmente são tratadas de forma que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster de Hadoop será dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos funcionando e trabalhos pendentes falha após a conclusão da operação de escala. No entanto, você pode reenviar os trabalhos quando a operação for concluída.

- HBase

    Perfeita, você pode adicionar ou remover nós ao cluster HBase enquanto ele é executado. Servidores regionais são automaticamente equilibrados em poucos minutos de concluir a operação de escala. No entanto, você também manualmente pode comparar os servidores regionais efetuando login a headnode de cluster e executando os seguintes comandos em uma janela do prompt de comando:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Tempestade

    Perfeitamente pode adicionar ou remover nós de dados para o seu cluster tempestade enquanto ele é executado. Mas após a conclusão bem-sucedida da operação de escala, você precisará rebalancear a topologia.

    Redistribuição pode ser feito de duas maneiras:

    * Web de tempestade interface do usuário
    * Ferramenta de interface de linha (comando)

    Consulte a [documentação de tempestade Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    Web tempestade UI está disponível no cluster HDInsight:

    ![hdinsight reequilíbrio dos escala de tempestade](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Eis um exemplo de como usar o comando CLI para rebalancear a topologia de tempestade:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

O trecho de código a seguir mostra como redimensionar um cluster sincronia ou assíncrona:

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Acesso de Grant/revoke

HDInsight clusters têm os seguintes serviços de web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por padrão, esses serviços são concedidos acesso. Você pode revogar/conceder acesso. Para revogar:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Para conceder:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Por concedendo/revogar o acesso, você irá redefinir cluster nome de usuário e senha.

Isso também pode ser feito através do Portal. Consulte [Administrar HDInsight usando o Portal do Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP

É o mesmo procedimento como [Grant/revoke HTTP acesso](#grant/revoke-access). Se o cluster concedeu o acesso HTTP, você deve primeiro revogá-lo.  E, em seguida, conceda o acesso com novas credenciais de usuário HTTP.


##<a name="find-the-default-storage-account"></a>Localize a conta de armazenamento padrão

O trecho de código a seguir demonstra como obter o nome de conta de armazenamento do padrão e a chave de conta de armazenamento padrão para um cluster.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Enviar trabalhos

**Para enviar trabalhos MapReduce**

Consulte [exemplos de executar Hadoop MapReduce em HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Para enviar trabalhos de seção** 

Consulte a [seção executar consultas usando o SDK do .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Para enviar trabalhos de porco**

Consulte [trabalhos de executar porco usando o SDK do .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Para enviar trabalhos de Sqoop**

Consulte [usar Sqoop com HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Para enviar trabalhos de Oozie**

Consulte [Usar Oozie com Hadoop a definir e executar um fluxo de trabalho em HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Carregar dados ao armazenamento de Blob do Azure
Consulte [carregar dados ao HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Consulte também
* [Documentação de referência de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrar HDInsight usando o Portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight usando uma interface de linha][hdinsight-admin-cli]
* [Criar clusters de HDInsight][hdinsight-provision]
* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


