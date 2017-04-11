<properties
   pageTitle="Usar dados Lucerne Analytics Java SDK para desenvolver aplicativos | Azure"
   description="Usar o SDK do Azure dados Lucerne Analytics Java para desenvolver aplicativos"
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

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-java-sdk"></a>Tutorial: Introdução ao Azure dados Lucerne análise usando o SDK Java

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o SDK do Azure dados Lucerne Analytics Java para criar uma conta de Lucerne de dados do Azure e executar operações básicas, tais como criar pastas, carreguem e baixar arquivos de dados, excluir sua conta e trabalhar com trabalhos. Para saber mais sobre Lucerne de dados, consulte [Análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

Neste tutorial, você irá desenvolver um aplicativo de console de Java que contém exemplos de tarefas administrativas comuns, bem como a criação de dados de teste e enviar um trabalho.  Para percorrer o tutorial mesmo usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

## <a name="prerequisites"></a>Pré-requisitos

* Java Development Kit (JDK) 8 (usando Java versão 1.8).
* IntelliJ ou outro ambiente de desenvolvimento Java adequado. Isso é opcional, mas recomendado. As instruções abaixo usam IntelliJ.
* **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
* **Ativar sua assinatura do Azure** para visualização pública dados Lucerne Analytics. Consulte [as instruções](data-lake-analytics-get-started-portal.md#signup).
* Criar um aplicativo do Azure Active Directory (AAD) e recuperar sua **chave**, **Locatário ID**e **ID do cliente**. Para obter mais informações sobre aplicativos AAD e instruções sobre como obter um ID de cliente, consulte [criar Active Directory aplicativo e principal usando o portal do serviço](../resource-group-create-service-principal-portal.md). O URI de resposta e a chave também estará disponível a partir do portal depois que você tiver o aplicativo criado e chave gerada.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como autenticam usando o Azure Active Directory?

O trecho de código a seguir fornece código para **não interativo** autenticação, onde o aplicativo fornece suas próprias credenciais.

Você precisará dar permissão seu aplicativo para criar recursos no Azure para este tutorial para trabalhar. É **altamente recomendável** que você só atribua permissões de Colaborador este aplicativo a um grupo de recursos novos, não utilizada e vazias em sua assinatura do Azure para os fins deste tutorial.

## <a name="create-a-java-application"></a>Criar um aplicativo Java

1. Abra IntelliJ e crie um novo projeto de Java usando o modelo de **Aplicativo de linha de comando** .

2. Clique com botão direito no projeto no lado esquerdo da tela e clique em **Adicionar suporte de Framework**. Escolha **Maven** e clique **Okey**.

3. Abra o arquivo recém-criado **"pom.xml"** e adicione o seguinte trecho do texto entre o ** \</version >** marca e o ** \</projeto >** marca:

    Observação: Esta etapa é temporária até que o SDK do Azure dados Lucerne Analytics está disponível no Maven. Este artigo será atualizado quando o SDK está disponível no Maven. Todas as atualizações futuras este SDK estará disponível por meio de Maven.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-20160513.000802-24</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-runtime</artifactId>
                <version>1.0.0-20160513.000812-28</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.rest</groupId>
                <artifactId>client-runtime</artifactId>
                <version>1.0.0-20160513.000825-29</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-analytics</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Vá para o **arquivo**, e em seguida, **configurações**, em seguida, **criar**, **execução**, **implantação**. Selecione **Ferramentas de construção**, **Maven**, a **importação**. Marque **Importar Maven projetos automaticamente**.

5. Abra **Main.java** e substitua o bloco de código existente com o seguinte código. Além disso, forneça os valores para parâmetros destacados no trecho de código, como **localFolderPath**, **_adlaAccountName**, **_adlsAccountName**, **_resourceGroupName** e substituir espaços reservados para **ID do cliente**, **Segredo de cliente**, **LOCATÁRIO-ID**e **ID da assinatura**.

    Este código percorre o processo de criar contas de armazenamento de Lucerne de dados e a análise de dados Lucerne, criando arquivos no repositório, executando um trabalho, obtendo o status do trabalho, baixando saída de trabalho e finalmente excluir a conta.

    >[AZURE.NOTE] Há um problema conhecido com o serviço de Lucerne de dados do Azure.  Se o aplicativo de amostra for interrompido ou encontra um erro, talvez você precise excluir manualmente as contas de armazenamento de dados de Lucerne & Lucerne a análise de dados que o script cria.  Se não estiver familiarizado com o Portal, guia [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md) obterá você começar.


        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.analytics.*;
        import com.microsoft.azure.management.datalake.analytics.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.util.ArrayList;
        import java.util.UUID;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _adlaAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store and Analytics accounts
                WaitForNewline("Authenticated.", "Creating NEW accounts.");
                CreateAccounts();
                WaitForNewline("Accounts created.", "Displaying accounts.");
        
                // List Data Lake Store and Analytics accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
                List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
                for (DataLakeAnalyticsAccount acct : adlaListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Accounts displayed.", "Creating files.");
        
                // Create a file in Data Lake Store: input1.csv
                // TODO: these change order in the next patch
                byte[] bytesContents = "123,abc".getBytes();
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);
        
                WaitForNewline("File created.", "Submitting a job.");
        
                // Submit a job to Data Lake Analytics
                UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob");
                WaitForNewline("Job submitted.", "Getting job status.");
        
                // Wait for job completion and output job status
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                System.out.println("Waiting for job completion.");
                WaitForJob(jobId);
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                WaitForNewline("Job completed.", "Downloading job output.");
        
                // Download job output from Data Lake Store
                DownloadFile("/output1.csv", localFolderPath + "output1.csv");
                WaitForNewline("Job output downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/output1.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
                _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
                _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
                _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
                _adlsClient.setSubscriptionId(_subId);
                _adlaClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
        
                System.out.println(reason + "\r\nPress ENTER to continue...");
                try{System.in.read();}
                catch(Exception e){}
        
                if (!nextAction.isEmpty())
                {
                    System.out.println(nextAction);
                }
            }
        
            // Create Data Lake Store and Analytics accounts
            public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
                // Create ADLS account
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
        
                // Create ADLA account
                DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
                adlsInfo.setName(_adlsAccountName);
        
                DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
                adlsInfo.setProperties(adlsInfoProperties);
        
                List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
                adlsInfoList.add(adlsInfo);
        
                DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
                adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
                adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
        
                DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
                adlaParameters.setLocation(_location);
                adlaParameters.setName(_adlaAccountName);
                adlaParameters.setProperties(adlaProperties);
        
                    /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */
        
                _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }
        
            //todo: this changes in the next version of the API
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Submit a U-SQL job by providing script contents.
            // Returns the job ID
            public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
                UUID jobId = java.util.UUID.randomUUID();
                USqlJobProperties properties = new USqlJobProperties();
                properties.setScript(script);
                JobInformation parameters = new JobInformation();
                parameters.setName(jobName);
                parameters.setJobId(jobId);
                parameters.setType(JobType.USQL);
                parameters.setProperties(properties);
        
                JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
        
                return jobId;
            }
        
            // Wait for job completion
            public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                while (jobInfo.getState() != JobState.ENDED)
                {
                    jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
                }
                return jobInfo.getResult();
            }
        
            // Get job status
            public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                return jobInfo.getState().toValue();
            }
        }

6. Siga os prompts para executar e concluir o aplicativo.


## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial usando outras ferramentas, clique em seletores de guia no topo da página.
- Para ver uma consulta mais complexa, consulte [logs de site de análise usando análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [scripts de desenvolver U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U-SQL, consulte [Introdução ao idioma do Azure dados Lucerne Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)e [referência de linguagem de U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Tarefas de gerenciamento, consulte [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral de análise de Lucerne de dados, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).
