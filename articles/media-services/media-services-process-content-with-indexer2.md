<properties
    pageTitle="A indexação de arquivos de mídia com o Azure Media indexador 2 Preview | Microsoft Azure"
    description="Azure indexador de mídia permite que você para tornar o conteúdo dos seus arquivos de mídia pesquisável e gerar uma transcrição de texto completo para legendas codificadas ou palavras-chave. Este tópico mostra como usar mídia indexador 2 Preview."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>A indexação de arquivos de mídia com o Azure Media indexador 2 Preview

##<a name="overview"></a>Visão geral

O processador de mídia do **Azure mídia indexador 2 Preview** (MP) permite que você verifique os arquivos de mídia e conteúdo pesquisável, bem como gerar faixas de legendas fechadas. Em comparação com a versão anterior do [Indexador de mídia do Azure](media-services-index-content.md), o **Azure mídia indexador 2 Preview** executa a indexação mais rápido e oferece suporte mais amplo de idioma. Idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês, português e árabe.

O **Azure mídia indexador 2 Preview** MP está no modo de visualização.

Este tópico mostra como criar trabalhos de indexação com **Azure mídia indexador 2 Preview**.

>[AZURE.NOTE]Aplica considerando o seguinte:
>
>Não há suporte para indexador 2 na China do Azure e governo do Azure.
>
>A visualização está limitada aos cerca de 10 minutos de processamento, mas é gratuita para todos os clientes.
>
>Quando a indexação de conteúdo, verifique se usar arquivos de mídia que têm muito clara fala (sem música de fundo, ruído, efeitos ou assovio de microfone). Alguns exemplos de conteúdo apropriado são: gravado reuniões, palestras ou apresentações. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, nada com áudio misto e efeitos de som gravado mal conteúdo com ruído de fundo (assovio).


Este tópico fornece detalhes sobre a **Visualização de 2 de indexador de mídia do Azure** e mostra como usá-lo com SDK de serviços de mídia para .NET

##<a name="input-and-output-files"></a>Arquivos de entrada e saídos

###<a name="input-files"></a>Arquivos de entrada

Arquivos de áudio ou vídeo

###<a name="output-files"></a>Arquivos de saída

Um trabalho de indexação pode gerar arquivos de legenda codificada nos seguintes formatos:  

- **Sami**
- **TTML**
- **WebVTT**

Fechada legenda (CC) arquivos nestes formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

##<a name="task-configuration-preset"></a>Configuração de tarefa (predefinir)

Ao criar uma tarefa de indexação com **Azure mídia indexador 2 Preview**, especifique um valor de configuração predefinido.

O seguinte JSON define parâmetros disponíveis.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Idiomas com suporte  

Azure mídia indexador 2 Preview é compatível com fala em texto para os seguintes idiomas (quando especificando o nome do idioma na configuração da tarefa, use o código de 4 caracteres entre colchetes, conforme mostrado abaixo):

- Inglês [EnUs]
- Espanhol [EsEs]
- Chinês [ZhCn]
- Francês [FrFr]
- Alemão [DeDe]
- Italiano [ItIt]
- Português (Portugal) [PtBr]
- Árabe (egípcio) [ArEg]


## <a name="sample-code"></a>Código de exemplo

O seguinte programa mostra como:

1. Criar um ativo e carregar um arquivo de mídia para o ativo.
1. Cria um trabalho com uma tarefa de indexação com base em um arquivo de configuração que contém a seguintes predefinir json.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Baixa os arquivos de saída. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
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
        
                static void Main(string[] args)
                {
        
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        
                    progressJobTask.Wait();
        
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
                }
        
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
        
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
        
                    return asset;
                }
        
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
        
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
        
                    return processor;
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
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Links relacionados

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)