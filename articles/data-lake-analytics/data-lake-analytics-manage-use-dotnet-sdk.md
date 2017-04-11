<properties 
   pageTitle="Gerenciar Azure dados Lucerne análise usando o SDK do Azure .NET | Azure" 
   description="Saiba como gerenciar trabalhos de dados Lucerne Analytics, fontes de dados, os usuários. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gerenciar Azure dados Lucerne análise usando o SDK do Azure .NET

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas de análise de Lucerne de dados do Azure, fontes de dados, os usuários e trabalhos usando o SDK do .NET Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique em Selecionar guia acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Conectar a dados Azure Lucerne Analytics

Você precisará os pacotes de Nuget a seguir:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


O exemplo de código a seguir mostra como se conectar ao Azure e listar as contas de análise de Lucerne dados existentes em sua assinatura do Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gerenciar contas

Antes de executar qualquer trabalhos de análise de Lucerne de dados, você deve ter uma conta de dados Lucerne Analytics. Ao contrário de Azurehdinsight, você não pagar por uma conta de análise quando ele não estiver executando um trabalho.  Você paga somente a hora quando um trabalho está funcionando.  Para obter mais informações, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Criar contas

Você deve ter um grupo de gerenciamento de recursos do Azure e uma conta de armazenamento de Lucerne de dados antes de executar o exemplo a seguir.

O código a seguir mostra como criar um grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

O código a seguir mostra como criar uma conta de armazenamento de Lucerne de dados:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

O código a seguir mostra como criar uma conta de análise de Lucerne de dados:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Lista de contas

Consulte [conectar-se a dados Azure Lucerne Analytics](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Localizar uma conta

Depois que você tiver um objeto de uma lista de contas de análise de Lucerne de dados, você pode usar o seguinte para localizar uma conta:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Excluir dados Lucerne Analytics contas

O trecho de código a seguir exclui uma conta de análise de Lucerne de dados:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar fontes de dados de conta

Dados Lucerne Analytics atualmente oferece suporte a fontes de dados a seguir:

- [Dados Azure Lucerne armazenamento](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta de análise, você deve designar uma conta de armazenamento de Lucerne de dados do Azure para ser a conta de armazenamento padrão. A conta de armazenamento de dados de Lucerne padrão é usada para armazenar os logs de auditoria de trabalho e metadados de trabalho. Após ter criado uma conta de análise, você pode adicionar contas adicionais de armazenamento de Lucerne de dados e/ou conta de armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localize a conta de armazenamento de Lucerne de dados padrão

Consulte encontrar uma conta neste artigo para encontrar a conta de dados Lucerne Analytics. Em seguida, use o seguinte:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Usar grupos de Gerenciador de recursos do Azure

Aplicativos geralmente são compostos de muitos componentes, por exemplo um aplicativo web, banco de dados, servidor de banco de dados, armazenamento e 3º serviços de terceiros. Gerenciador de recursos de Azure permite que você trabalhe com os recursos em seu aplicativo como um grupo, chamado de um grupo de recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do aplicativo em uma operação única e coordenado. Usar um modelo para implantação e esse modelo pode servir para diferentes ambientes como teste, teste e produção. Você pode esclarecer cobrança para sua organização exibindo os custos acumulados para o grupo inteiro. Para obter mais informações, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de dados Lucerne Analytics pode incluir os seguintes componentes:

- Conta de análise Lucerne de dados do Azure
- Conta de armazenamento do Azure dados Lucerne padrão necessários
- Contas de armazenamento Lucerne de dados do Azure adicionais
- Contas adicionais de armazenamento do Azure

Você pode criar todos esses componentes em um grupo de gerenciamento de recursos para torná-las mais fácil de gerenciar.

![Conta de análise Lucerne de dados do azure e de armazenamento](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta de dados Lucerne análise e as contas de armazenamento dependentes devem ser colocadas no mesmo Azure data center.
O grupo de gerenciamento de recursos no entanto pode estar localizado em uma central de dados diferentes.  

##<a name="see-also"></a>Consulte também 

- [Visão geral de análise de Lucerne de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Começar a usar a análise de Lucerne dados usando o portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerenciar Azure dados Lucerne análise usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorar e solucionar problemas de trabalhos de análise de Lucerne de dados do Azure usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

