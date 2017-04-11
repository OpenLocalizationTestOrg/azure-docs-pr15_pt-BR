<properties
    pageTitle="Gerenciamento de SDK .NET para a análise de fluxo | Microsoft Azure"
    description="Introdução ao fluxo de análise de gerenciamento .NET SDK. Saiba como configurar e executar a análise trabalhos: criar um projeto, entradas, saídas e transformações."
    keywords=".NET SDK, API de análise"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gerenciamento .NET SDK: Configurar e executar trabalhos de análise usando a API de análise de fluxo de Azure para .NET

Saiba como configurar um trabalhos de executar a análise usando a API de análise de fluxo para .NET usando o SDK do .NET de gerenciamento. Configurar um projeto, criar fontes de entrada e saídas, transformações e iniciar e parar trabalhos. Para os seus trabalhos de análise, você pode transmitir dados de armazenamento de Blob ou de um hub de evento.

Consulte a [documentação de referência de gerenciamento para a API de análise de fluxo para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Analytics fluxo é um serviço totalmente gerenciado fornecendo processamento de eventos de baixa latência, altamente disponível, escaláveis, complexa ao longo do fluxo de dados na nuvem. Análise de fluxo permite que os clientes configurar o streaming trabalhos para analisar fluxos de dados e permite que eles direcionam perto análise em tempo real.  


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

- Instale o Visual Studio 2012 ou 2013.
- Baixe e instale o [SDK do Azure .NET](https://azure.microsoft.com/downloads/).
- Crie um grupo de recursos do Azure em sua assinatura. O que vem a seguir é um exemplo de script do PowerShell do Azure. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Configure uma entrada de origem e de destino de saída para usar. Para mais instruções consulte [Adicionar entradas](stream-analytics-add-inputs.md) para configurar uma entrada de exemplo e [Adicionar saídas](stream-analytics-add-outputs.md) configurar um exemplo de saída.


## <a name="set-up-a-project"></a>Configurar um projeto

Para criar um trabalho de análise usar a API de análise de fluxo para .NET, primeiro configure seu projeto.

1. Crie um aplicativo de console do Visual Studio c# .NET.
2. No Console do Gerenciador de pacote, execute os seguintes comandos para instalar os pacotes do NuGet. O primeiro é o SDK do Azure fluxo Analytics gerenciamento .NET. O segundo é o cliente do Active Directory do Azure que será usado para autenticação.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Adicione a seguinte seção **appSettings** para o arquivo App:

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Substituir valores para **SubscriptionId** e **ActiveDirectoryTenantId** com sua assinatura do Azure e IDs de locatários. Você pode obter esses valores, execute o seguinte cmdlet do PowerShell do Azure:

        Get-AzureAccount

5. Adicione as seguintes instruções **using** ao arquivo de origem (Program.cs) do projeto:

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Adicione um método de auxiliar de autenticação:

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
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


## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gerenciamento de análise de fluxo

Um objeto de **StreamAnalyticsManagementClient** permite que você gerencie o trabalho e os componentes de trabalho, como entrada, saída e transformação.

Adicione o seguinte código para o início do método **principal** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Valor da variável **resourceGroupName** deve ser o mesmo que o nome do grupo de recursos que você criou ou escolheu nas etapas pré-requisito.

Para automatizar o aspecto da apresentação de credencial da criação de trabalho, consulte [autenticar um principal de serviço com o Gerenciador de recursos do Azure](../resource-group-authenticate-service-principal.md).

As seções restantes deste artigo presumem que esse código é no início do método **principal** .

## <a name="create-a-stream-analytics-job"></a>Criar uma a análise de fluxo de trabalho

O código a seguir cria um trabalho de análise de fluxo em grupo de recursos que você definiu. Você adicionará uma entrada, saída e transformação ao trabalho mais tarde.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Criar uma fonte de entrada de análise de fluxo

O código a seguir cria uma fonte de entrada de análise de fluxo com o tipo de fonte de entrada de blob e serialização CSV. Para criar uma fonte de entrada de hub de evento, use **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, você pode personalizar o tipo de serialização da fonte de entrada.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Fontes de entrada, de armazenamento de Blob ou um hub de evento, estão vinculados a um trabalho específico. Para usar a mesma fonte de entrada diferentes trabalhos, você deve chamar o método novamente e especifique um nome de trabalho diferente.


## <a name="test-a-stream-analytics-input-source"></a>Testar uma fonte de entrada de análise de fluxo

O método **TestConnection** testa se o trabalho de análise de fluxo é capaz de se conectar a fonte de entrada, bem como outros aspectos específicos para o tipo de fonte de entrada. Por exemplo, na fonte de entrada de blob criado em uma etapa anterior, o método verificará que o nome da conta de armazenamento e o par de chaves pode ser usado para conectar-se para a conta de armazenamento, bem como verificar a existência de contêiner especificado.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída de análise de fluxo

Criar um destino de saída é muito semelhante à criação de uma fonte de entrada de análise de fluxo. Como fontes de entrada, os destinos de saída estão vinculados a um trabalho específico. Para usar o mesmo destino de saída para diferentes trabalhos, você deve chamar o método novamente e especifique um nome de trabalho diferente.

O código a seguir cria um destino de saída (banco de dados do SQL Azure). Você pode personalizar o tipo de dados de destino de saída e/ou tipo de serialização.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Um destino de saída de análise de fluxo de teste

Um destino de saída de análise de fluxo também tem o método **TestConnection** para conexões de teste.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação de análise de fluxo

O código a seguir cria uma transformação de análise de fluxo com a consulta "Selecionar * de entrada" e especifica para alocar uma unidade streaming para o trabalho de análise de fluxo. Para obter mais informações sobre como ajustar as unidades de streaming, consulte [trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Como entrada e saída, um transformações também está vinculada do trabalho de análise de fluxo específico, ele foi criado em.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma a análise de fluxo de trabalho
Depois de criar um trabalho de análise de fluxo e suas entradas, saídas e transformação, você pode iniciar o trabalho chamando o método **Iniciar** .

O exemplo a seguir inicia código um trabalho de análise de fluxo com uma hora de início de saída personalizada definida como 12 de dezembro de 2012, 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Interromper uma a análise de fluxo de trabalho
Você pode interromper um trabalho em execução de análises de fluxo chamando o método **Parar** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Excluir uma a análise de fluxo de trabalho
O método **Excluir** excluirá o trabalho bem como os recursos de sub subjacentes, inclusive entradas, saídas e transformação do trabalho.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximas etapas

Que você aprender as Noções básicas de como usar um SDK do .NET para criar e executar trabalhos de análise. Para saber mais, consulte o seguinte:

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Fluxo azure Analytics gerenciamento .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
