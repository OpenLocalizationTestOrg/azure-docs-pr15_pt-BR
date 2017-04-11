<properties
    pageTitle="Criar um pool de banco de dados elástica com c# | Microsoft Azure"
    description="Use técnicas de desenvolvimento do c# banco de dados para criar um pool de banco de dados elástica scalable no banco de dados do Azure SQL para que você possa compartilhar recursos em muitos bancos de dados."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="create-an-elastic-database-pool-with-cx23"></a>Criar um pool de banco de dados elástica com C & #x 23;

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Este artigo descreve como usar c# para criar um pool de elástica banco de dados do SQL Azure com a [Biblioteca de banco de dados do SQL Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Para criar um banco de dados do SQL autônomo, consulte [usar c# para criar um banco de dados do SQL com a biblioteca de banco de dados SQL para .NET](sql-database-get-started-csharp.md).

A biblioteca de banco de dados do SQL Azure para .NET fornece um [Gerente de recursos do Azure](../azure-resource-manager/resource-group-overview.md)-com base em API que seja quebrado [Baseado em Gerenciador de recursos de API REST de banco de dados de SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Muitos recursos novos do banco de dados SQL são suportados apenas quando você estiver usando o [modelo de implantação do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md), então você sempre deve usar as últimas **biblioteca de gerenciamento de banco de dados do Azure SQL para .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. O mais antigo [bibliotecas com base em modelo clássico de implantação](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) são suportados para fins de compatibilidade somente, portanto, recomendamos que você usa as bibliotecas mais recentes do Gerenciador de recursos com base.

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Conta gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página de [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="create-a-console-app-and-install-the-required-libraries"></a>Criar um aplicativo de console e instalar as bibliotecas necessárias

1. Inicie o Visual Studio.
2. Clique em **arquivo** > **nova** > **projeto**.
3. Crie um c# **Console Application** e nomeie: *SqlElasticPoolConsoleApp*


Para criar um banco de dados do SQL com c#, carrega as bibliotecas de gerenciamento necessárias (usando o [console do Gerenciador de pacote](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Clique em **Ferramentas** > **Gerenciador de pacote NuGet** > **Package Manager Console**.
2. Tipo de `Install-Package Microsoft.Azure.Management.Sql –Pre` para instalar a [Biblioteca de gerenciamento do Microsoft Azure SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Tipo de `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` para instalar a [Biblioteca do Gerenciador de recursos do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Tipo de `Install-Package Microsoft.Azure.Common.Authentication –Pre` para instalar a [Biblioteca de autenticação comuns do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Os exemplos deste artigo usam um formulário síncrono de cada solicitação de API e bloquear até chamada de conclusão do restante do serviço subjacente. Há métodos de assíncrono disponíveis.


## <a name="create-a-sql-elastic-database-pool---c-example"></a>Criar um pool de elástica banco de dados do SQL - exemplo c#

O exemplo a seguir cria um grupo de recursos, o servidor, a regra de firewall, o pool elástica e, então, cria um banco de dados do SQL no pool. Consulte, [criar um serviço principal para acessar recursos](#create-a-service-principal-to-access-resources) para obter o `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variáveis.

Substitua o conteúdo de **Program.cs** a seguir e atualize o `{variables}` com seus valores de aplicativo (não inclua a `{}`).


```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```





## <a name="create-a-service-principal-to-access-resources"></a>Criar um serviço principal para acessar recursos

O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e o capital de serviço que precisamos para autenticar nosso aplicativo c#. O script saídas de valores que precisamos para a amostra c# anterior. Para obter informações detalhadas, consulte [Usar o PowerShell do Azure para criar um serviço principal para acessar recursos](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret


  

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar seu pool](sql-database-elastic-pool-manage-csharp.md)
- [Criar Elástico trabalhos](sql-database-elastic-jobs-overview.md): trabalhos Elástico permitem executar scripts T-SQL em relação a qualquer número de bancos de dados em um pool.
- [Dimensionamento com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para dimensionar.

## <a name="additional-resources"></a>Recursos adicionais

- [Banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de gerenciamento de recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
