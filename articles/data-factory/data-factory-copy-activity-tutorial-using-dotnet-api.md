<properties 
    pageTitle="Tutorial: Criar um pipeline com atividade de cópia usando API .NET | Microsoft Azure" 
    description="Neste tutorial, você cria um pipeline de fábrica de dados do Azure com uma atividade de cópia usando API .NET." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Criar um pipeline com atividade de cópia usando API .NET
> [AZURE.SELECTOR]
- [Visão geral e os pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistente de cópia](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [O Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gerenciador de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Este tutorial mostra como criar e monitorar uma fábrica de dados Azure usando a API do .NET. O pipeline na fábrica dados usa uma atividade de cópia para copiar dados de armazenamento de Blob do Azure para Azure SQL Database.

A atividade de cópia realiza a movimentação dos dados em fábrica de dados do Azure. A atividade estiver usando um serviço globalmente disponível que pode copiar dados entre várias fontes de dados de uma maneira segura, confiável e flexível. Consulte o artigo de [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a atividade de cópia.   

> [AZURE.NOTE] 
> Este artigo não aborda todos os dados fábrica API .NET. Consulte [Referência de API do .NET de fábrica de dados](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre dados fábrica .NET SDK. 

## <a name="prerequisites"></a>Pré-requisitos
- Dar uma olhada [Tutorial visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma visão geral do tutorial e conclua as etapas de **pré-requisito** . 
- O Visual Studio 2012 ou 2013 ou 2015
- Baixe e instale o [SDK do Azure .NET](http://azure.microsoft.com/downloads/)
- PowerShell Azure. Siga as instruções no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) instalar PowerShell do Azure em seu computador. Você usa o PowerShell do Azure para criar um aplicativo do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Active Directory do Azure
Criar um aplicativo do Azure Active Directory, crie uma entidade de serviço para o aplicativo e atribuí-la a função **Colaborador de fábrica de dados** .  

1. Abra o **PowerShell**. 
1. Execute o seguinte comando e insira o nome de usuário e senha que você usa para entrar no portal do Azure.
    
        Login-AzureRmAccount   
2. Execute o seguinte comando para exibir todas as assinaturas para essa conta.

        Get-AzureRmSubscription 
3. Execute o seguinte comando para selecionar a assinatura à qual você deseja trabalhar. Substituir ** &lt;NameOfAzureSubscription** &gt; com o nome da sua assinatura do Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Anote **SubscriptionId** e **TenantId** da saída deste comando. 
4. Crie um grupo de recursos Azure denominado **ADFTutorialResourceGroup** executando o seguinte comando do PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Se o grupo de recursos já existir, especifique se deseja atualizá-lo (Y) ou mantê-los como (N). 

    Se você usar um grupo de recursos diferentes, você precisa usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.
5. Crie um aplicativo do Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Se você receber o erro a seguir, especifique uma URL diferente e execute o comando novamente. 

        Another object with the same value for property identifierUris already exists.

6. Crie o anúncio principal do serviço. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Adicione principal do serviço na função **Colaborador de fábrica de dados** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Obter a ID de aplicativo.

        $azureAdApplication

    Anote a ID de aplicativo (**applicationID** da saída).

Você deve ter a seguir quatro valores destas etapas: 

- ID do locatário
- ID da assinatura
- ID do aplicativo 
- Senha (especificada no primeiro comando)   

## <a name="walkthrough"></a>Explicação passo a passo
1. Usando o Visual Studio 2012/2013/2015, crie um aplicativo de console c# .NET.
    1. Inicie o **Visual Studio** 2012/2013/2015.
    2. Clique em **arquivo**, aponte para **novo**e clique em **projeto**.
    3. Expanda **modelos**e selecione **Visual c#**. Este passo a passo, você pode usar c#, mas você pode usar qualquer linguagem .NET.
    4. Selecione o **Aplicativo de Console** na lista de tipos de projeto à direita.
    5. Digite **DataFactoryAPITestApp** para o nome.
    6. Selecione **C:\ADFGetStarted** para o local.
    7. Clique em **Okey** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Nuget Package Manager**e clique em **Package Manager Console**.
3.  No **Console do Gerenciador de pacote**, siga estas etapas: 
    1.  Execute o seguinte comando para instalar o pacote de fábrica de dados:`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Execute o seguinte comando para instalar o pacote do Azure Active Directory (você usar API do Active Directory no código):`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Adicione a seguinte seção **appSetttings** para o arquivo **App** . Essas configurações são usadas pelo método auxiliar: **GetAuthorizationHeader**. 

    Substituir valores para ** &lt;ID do aplicativo&gt;**, ** &lt;senha&gt;**, ** &lt;ID da assinatura&gt;**, e ** &lt;identificação de locatários&gt; ** com seus próprios valores. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Adicione as seguintes instruções **using** ao arquivo de origem (Program.cs) do projeto.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Adicione o seguinte código que cria uma instância da classe **DataPipelineManagementClient** para o método **principal** . Você pode usar esse objeto para criar uma fábrica de dados, um serviço vinculado, conjuntos de dados de entrada e saídos e um pipeline. Você também pode usar esse objeto para monitorar as fatias de um conjunto de dados em tempo de execução.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Substitua o valor de **resourceGroupName** com o nome do seu grupo de recursos Azure. 
    > 
    > Atualize o nome da fábrica dados (**dataFactoryName**) sejam exclusivos. Nome da fábrica dados deve ser exclusivo. Consulte o tópico de [Dados Factory - regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefatos de fábrica de dados. 

7. Adicione o seguinte código que cria uma **fábrica de dados** para o método **principal** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Adicione o seguinte código que cria um **armazenamento do Azure vinculado serviço** para o método **principal** . 

    > [AZURE.IMPORTANT] Substitua **storageaccountname** e **accountkey** com o nome e a chave da sua conta de armazenamento do Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Adicione o seguinte código que cria um **SQL Azure vinculado serviço** para o método **principal** .
 
    > [AZURE.IMPORTANT] Substitua **nomedoservidor**, **databasename**, **nome de usuário**e **senha** com nomes de seu servidor SQL Azure, banco de dados, usuário e senha.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Adicione o seguinte código que cria **conjuntos de dados de entrada e saídos** para o método **principal** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Adicione o seguinte código que **cria e ativa um pipeline** para o método **principal** . Esse pipeline tem um **CopyActivity** que leva **BlobSource** como uma fonte e **BlobSink** como um receptor.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Adicione o seguinte código para o método **principal** para obter o status de uma fatia de dados do conjunto de dados de saída. Há apenas fatia esperada neste exemplo.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Adicione o seguinte código para obter executar detalhes para uma fatia de dados para o método **principal** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Adicione o seguinte método auxiliar usado pelo método **principal** para a classe de **programa** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


15. No Solution Explorer, expanda o projeto (**DataFactoryAPITestApp**), **referências**de atalho e clique em **Adicionar referência**. Marque a caixa de seleção para assembly de "**System. Configuration**" e clique em **Okey**. 
16. Crie o aplicativo de console. Clique em **criar** no menu e clique em **Criar solução**. 
16. Confirme se há pelo menos um arquivo no contêiner de **adftutorial** em seu armazenamento de blob do Microsoft Azure. Caso contrário, crie o arquivo de **Emp.txt** no bloco de notas com o seguinte conteúdo e carregue-o para o contêiner de adftutorial.

        John, Doe
        Jane, Doe
     
17. Executar a amostra clicando em **Depurar** -> **Iniciar depuração** no menu. Quando você vir o **Obtendo execução detalhes de uma fatia de dados**, aguarde alguns minutos e pressione **ENTER**. 
18. Use o portal do Azure para verificar que a fábrica de dados **APITutorialFactory** é criado com os seguintes artefatos: 
    - Vinculado serviço: **LinkedService_AzureStorage** 
    - DataSet: **DatasetBlobSource** e **DatasetBlobDestination**.
    - Pipeline: **PipelineBlobSample** 
18. Verificar se os registros de dois funcionário são criados na tabela "**emp**" o banco de dados do SQL Azure especificado.

## <a name="next-steps"></a>Próximas etapas

- Leia o artigo de [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , que fornece informações detalhadas sobre a atividade de cópia que você usou no tutorial.
- Consulte [Referência de API do .NET de fábrica de dados](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre dados fábrica .NET SDK. Este artigo não aborda todos os dados fábrica API .NET. 

 
