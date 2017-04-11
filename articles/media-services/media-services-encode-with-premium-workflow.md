<properties 
    pageTitle="Advanced codificação com fluxo de trabalho do Media codificador Premium | Microsoft Azure" 
    description="Saiba como codificar com fluxo de trabalho do Media codificador Premium. Exemplos de código escritos em c# e usam o SDK de serviços de mídia para .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>

#<a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Advanced codificação com fluxo de trabalho do Media codificador Premium

>[AZURE.NOTE] Processador de mídia de fluxo de trabalho do Media codificador Premium abordado neste tópico não está disponível na China.

Para perguntas de codificador premium, email mepd no Microsoft.com.

##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure está apresentando o processador de mídia de **Fluxo de trabalho do Media codificador Premium** . Esse avanço de ofertas de processador codificação recursos dos fluxos de trabalho sob demanda premium. 

Os tópicos a seguir descrevem detalhes relacionados ao **Fluxo de trabalho do Media codificador Premium**: 

- [Formatos compatíveis com o fluxo de trabalho de Premium de codificador de mídia](media-services-premium-workflow-encoder-formats.md) – presentationDiscute os codecs e formatos de arquivo suportados pelo **Fluxo de trabalho do Media codificador Premium**.

- A seção [Comparar codificadores](media-services-encode-asset.md#compare_encoders) compara os recursos de codificação do **Fluxo de trabalho do Media codificador Premium** e **Padrão de codificador de mídia**.

Este tópico demonstra como codificar com o **Fluxo de trabalho do Media codificador Premium** usando .NET.

Codificação tarefas para o **Fluxo de trabalho do Media codificador Premium** exigem um arquivo de configuração separado, chamado de um arquivo de fluxo de trabalho. Esses arquivos têm uma extensão de .workflow e são criados usando a ferramenta [Designer de fluxo de trabalho](media-services-workflow-designer.md) .

##<a name="encode"></a>Codificar

Codificação tarefas para o **Fluxo de trabalho do Media codificador Premium** exigem um arquivo de configuração separado, chamado de um arquivo de fluxo de trabalho. Esses arquivos têm uma extensão de .workflow e são criados usando a ferramenta [Designer de fluxo de trabalho](media-services-workflow-designer.md) .


Você também pode obter o padrão de arquivos de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Os arquivos de fluxo de trabalho precisam ser carregado à sua conta de serviços de mídia como um ativo, e este ativo deve ser transmitido para a tarefa de codificação.

O exemplo a seguir demonstra como codificar com **Fluxo de trabalho do Media codificador Premium**. 

As seguintes etapas são executadas: 
 
1. Criar um ativo e carregar um arquivo de fluxo de trabalho. 
2. Criar um ativo e carregar um arquivo de mídia de origem.
3. Obtenha o processador de mídia "Mídia fluxo de trabalho codificador Premium".
4. Crie um trabalho e uma tarefa. 

    Na maioria dos casos, a cadeia de caracteres de configuração para a tarefa está vazia (como no exemplo a seguir). Existem alguns cenários avançados (que exigem definir propriedades de tempo de execução dinamicamente) caso em que você poderia fornecer uma cadeia de caracteres XML para a tarefa de codificação. São exemplos dessas situações: Criando uma sobreposição, mídia paralelas ou sequencial de imagens, colocação de legenda.
5. Adicione dois ativos de entrada à tarefa.
    
    a. 1O – o ativo de fluxo de trabalho.

    b. 2º – o vídeo ativo.
    
    **Observação**: O recurso de fluxo de trabalho deve ser adicionado à tarefa antes dos ativos de mídia. A cadeia de caracteres de configuração para essa tarefa deve estar vazia. 

6. Envie o trabalho de codificação.

A seguir está um exemplo completo. Para obter informações sobre como configurar o desenvolvimento de .NET de serviços de mídia, consulte [desenvolvimento de serviços de mídia com .NET](media-services-dotnet-how-to-use.md).


    using System; 
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace MediaEncoderPremiumWorkflowSample
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");
    
            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
            
            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
    
            }
    
            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
    
                var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                    AccessPermissions.Write | AccessPermissions.List);
    
                var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
    
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);
    
                locator.Delete();
                accessPolicy.Delete();
    
                return asset;
            }
    
            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish. 
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();
    
                // If job state is Error the event handling 
                // method for job progress should log errors.  Here we check 
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }
    
                return job.OutputMediaAssets[0];
            }
    
            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
    
                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
    
                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
    
                Console.Write(builder.ToString());
            }
    
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }


Para perguntas de codificador premium, email mepd no Microsoft.com.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
