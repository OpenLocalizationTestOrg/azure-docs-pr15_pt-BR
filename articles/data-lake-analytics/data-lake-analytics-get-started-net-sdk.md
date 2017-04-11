<properties 
   pageTitle="Introdução ao Azure dados Lucerne análise usando .NET SDK | Azure" 
   description="Saiba como usar o SDK do .NET para criar contas de armazenamento de Lucerne de dados, criar trabalhos de dados Lucerne Analytics e enviar trabalhos escritos em U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>Tutorial: Introdução ao Azure dados Lucerne análise usando o SDK do .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Saiba como usar o SDK do .NET Azure para enviar trabalhos escritos em [U-SQL](data-lake-analytics-u-sql-get-started.md) para a análise de dados Lucerne. Para obter mais informações sobre a análise de dados Lucerne, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

Neste tutorial, você irá desenvolver um aplicativo de console c# para enviar um trabalho de U-SQL que lê uma guia do arquivo de valores (TSV) separados e converte-o em um arquivo CSV (valores separados) de vírgula. Para percorrer o tutorial mesmo usando outras ferramentas compatíveis, clique nas guias na parte superior deste artigo.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Visual Studio 2015, Visual Studio 2013 atualizar 4, ou Visual Studio 2012 com Visual C++ instalado**.
- **SDK do Microsoft Azure para .NET versão 2.5 ou acima**.  Instalá-lo usando o [instalador de plataforma da Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **Uma análise do Azure dados Lucerne conta**. Consulte [Gerenciar dados Lucerne análise usando o SDK do Azure .NET](data-lake-analytics-manage-use-dotnet-sdk.md).

##<a name="create-console-application"></a>Criar aplicativo de console

Neste tutorial, você processar alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no repositório de dados Lucerne ou armazenamento de Blob do Azure. 

Um log de pesquisa de exemplo pode ser encontrado em um contêiner de Blob do Azure público. No aplicativo, você irá baixar o arquivo de sua estação de trabalho e, em seguida, carregue o arquivo para a conta de armazenamento de Lucerne de dados padrão da sua conta de dados Lucerne Analytics.

**Para criar um script U-SQL**

Trabalhos de análise de dados Lucerne são gravados na linguagem SQL U. Para saber mais sobre U-SQL, consulte [referência de linguagem de U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348)e [começar a usar linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) .

Criar um arquivo de **SampleUSQLScript.txt** com o seguinte script U-SQL e coloque o arquivo na **C:\temp\* * caminho.  O caminho é codificada no aplicativo .NET que você criar no próximo procedimento.  

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

No programa de c#, você precisa preparar o arquivo de **/Samples/Data/SearchLog.tsv** e a pasta **/Output/** .    

É mais simples usar caminhos relativos para arquivos armazenavam em default dados contas Lucerne. Você também pode usar caminhos absolutos.  Por exemplo 

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
    
Você deve usar caminhos absolutos para acessar arquivos em contas de armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta vinculada de armazenamento do Azure é:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

>[AZURE.NOTE] Há um problema conhecido com o serviço de Lucerne de dados do Azure.  Se o aplicativo de amostra for interrompido ou encontra um erro, talvez você precise excluir manualmente as contas de armazenamento de dados de Lucerne & Lucerne a análise de dados que o script cria.  Se você não estiver familiarizado com o portal do Azure, o guia [Gerenciar análise de Lucerne de dados do Azure usando o portal Azure](data-lake-analytics-manage-use-portal.md) obterá você começar.       

**Para criar um aplicativo**

1. Abra o Visual Studio.
2. Crie um aplicativo de console c#.
3. Abra o console de gerenciamento de pacote do NuGet e execute os seguintes comandos:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage


       
5. No Program.cs, cole o seguinte código:

        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
        
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";

                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                SetupClients(creds, SUBSCRIPTIONID);

                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");


                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
        
                WaitForNewline("Job output downloaded. You can now exit.");
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

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }


            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

6. Pressione **F5** para executar o aplicativo. A saída é como:

    ![Saída do trabalho de análise do Azure dados Lucerne U-SQL .NET SDK](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. Verifique o arquivo de saída.  O nome de arquivo e caminho padrão é c:\Temp\SearchLog-from-Data-Lake.csv.

## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial usando outras ferramentas, clique em seletores de guia no topo da página.
- Para ver uma consulta mais complexa, consulte [logs de site de análise usando análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [scripts de desenvolver U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U-SQL, consulte [Introdução ao idioma do Azure dados Lucerne Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)e [referência de linguagem de U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Tarefas de gerenciamento, consulte [Gerenciar o Azure dados Lucerne análise usando o portal Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral de análise de Lucerne de dados, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).
