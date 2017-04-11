<properties
   pageTitle="Usar dados Lucerne Store .NET SDK para desenvolver aplicativos | Microsoft Azure"
   description="Usar o SDK do Azure dados Lucerne Store .NET para desenvolver aplicativos"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando o SDK do .NET

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Aprenda a usar o [SDK do Azure dados Lucerne Store .NET](https://msdn.microsoft.com/library/mt581387.aspx) para executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, etc. Para saber mais sobre Lucerne de dados, consulte [Azure dados Lucerne Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2013 ou 2015**. As instruções abaixo usam Visual Studio 2015.

* **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de armazenamento de Lucerne de dados do azure**. Para obter instruções sobre como criar uma conta, consulte [Introdução ao armazenamento de Lucerne de dados do Azure](data-lake-store-get-started-portal.md)

* **Criar um aplicativo do Active Directory do Azure**. Use o aplicativo do Azure AD para autenticar o aplicativo de armazenamento de Lucerne de dados com o Azure AD. Há diferentes abordagens para autenticar com Azure AD, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET

1. Abra o Visual Studio e criar um aplicativo de console.

2. No menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.

3. No **Novo projeto**, digite ou selecione os valores a seguir:

  	| Propriedade | Valor                       |
  	|----------|-----------------------------|
  	| Categoria | Modelos/Visual c# / Windows |
  	| Modelo | Aplicativo de console         |
  	| Nome     | CreateADLApplication        |

4. Clique em **Okey** para criar o projeto.

5. Adicione os pacotes Nuget ao seu projeto.

    1. Clique com botão direito no nome do projeto no Solution Explorer e clique em **Gerenciar pacotes do NuGet**.
    2. Na guia **Nuget Package Manager** , certifique-se de que **origem pacote** está definido como **nuget.org** e essa caixa de seleção **incluir pré-lançamento** está selecionada.
    3. Procurar e instalar os pacotes de NuGet a seguir:

        * `Microsoft.Azure.Management.DataLake.Store`-Este tutorial usa v0.12.5-visualização.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`-Este tutorial usa v0.10.6-visualização.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Este tutorial usa v2.2.8-visualização.

        ![Adicionar uma fonte de Nuget] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Criar uma nova conta de Lucerne de dados do Azure")

    4. Feche o **Gerenciador de pacote do Nuget**.

6. Abrir **Program.cs**, exclua o código existente e, em seguida, inclua as instruções a seguir para adicionar referências para namespaces.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Declare as variáveis, conforme mostrado abaixo e forneça os valores de nome de armazenamento de Lucerne de dados e o nome do grupo de recursos que já existem. Além disso, verifique se o nome do caminho e do arquivo local que você fornecer aqui deve existir no computador. Adicione o trecho de código a seguir após as declarações de namespace.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nas seções restantes do artigo, você pode ver como usar os métodos de .NET disponíveis para realizar operações como autenticação, carregamento de arquivo, etc.

## <a name="authentication"></a>Autenticação

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Se você estiver usando autenticação de usuário final (recomendado para este tutorial)

Use este comando com um aplicativo da "Native Client" AD Azure existente; é fornecida para você abaixo. Para ajudá-lo a concluir este tutorial com mais rapidez, recomendamos que você use essa abordagem.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Algumas coisas que deve saber sobre este trecho de código acima.

* Para ajudá-lo a concluir o tutorial com mais rapidez, esse trecho usa um um anúncio Azure ID do cliente e o domínio que está disponível por padrão para todas as assinaturas do Azure. Portanto, você pode **usar este trecho de código como-está em seu aplicativo**.
* No entanto, se você quiser usar sua própria identificação de cliente do Azure AD domínio e aplicativo, você deve criar um aplicativo nativo do Azure AD usar o domínio do Azure AD, ID do cliente e redirecionar URI para o aplicativo que você criou. Consulte [criar um aplicativo do Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) para obter instruções.

>[AZURE.NOTE] As instruções em links acima são para um aplicativo web do Azure AD. No entanto, as etapas são exatamente o mesmo mesmo se você optou por criar um aplicativo cliente nativo em vez disso. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Se você estiver usando o serviço de autenticação com segredo de cliente 

O trecho a seguir pode ser usado para autenticar seu aplicativo não interativa, usando o cliente de tecla para um aplicativo / serviço capital / secreta. Use este comando com um [aplicativo do Azure AD "Web App"](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Se você estiver usando o serviço de autenticação com certificado

Como uma terceira opção, o trecho a seguir pode ser usado para autenticar seu aplicativo não interativa, usando o certificado para um objeto de aplicativo / serviço. Use este comando com um [aplicativo do Azure AD "Web App"](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Criar objetos de cliente

O trecho a seguir cria o cliente de conta e sistema de arquivos de dados Lucerne Store objetos, que são usados para emitir solicitações de serviço.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Lista todas as contas de armazenamento de Lucerne dados dentro de uma assinatura

O trecho a seguir lista todas as contas de armazenamento de Lucerne dados dentro de uma determinada assinatura Azure.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Criar um diretório

O trecho de código a seguir mostra uma `CreateDirectory` método que você pode usar para criar um diretório dentro de uma conta de armazenamento de Lucerne de dados.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Carregar um arquivo

O trecho de código a seguir mostra um `UploadFile` método que você pode usar para carregar arquivos para uma conta de armazenamento de Lucerne de dados.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`suporta recursiva upload e download entre um caminho de arquivo local e um caminho de arquivo de dados Lucerne Store.    

## <a name="get-file-or-directory-info"></a>Obter informações de arquivo ou pasta

O trecho de código a seguir mostra uma `GetItemInfo` método que você pode usar para recuperar informações sobre um arquivo ou pasta disponível na loja de Lucerne de dados. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Lista de arquivos ou pastas

O trecho de código a seguir mostra uma `ListItem` método que pode usar para listar os arquivos e pastas em uma conta de armazenamento de Lucerne de dados.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenar arquivos

O trecho de código a seguir mostra uma `ConcatenateFiles` método que você usa para concatenar arquivos. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Acrescentar a um arquivo

O trecho de código a seguir mostra uma `AppendToFile` método que você usar anexe dados a um arquivo já armazenado em uma conta de armazenamento de Lucerne de dados.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Baixar um arquivo

O trecho de código a seguir mostra uma `DownloadFile` método que você usa para baixar um arquivo de uma conta de armazenamento de Lucerne de dados.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência SDK do armazenamento de Lucerne de dados .NET](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência de restante de armazenamento de Lucerne de dados](https://msdn.microsoft.com/library/mt693424.aspx)
