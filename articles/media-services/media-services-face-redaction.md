<properties
    pageTitle="Face redação com a análise de mídia do Microsoft Azure | Microsoft Azure"
    description="Este tópico demonstra como redigir faces com a análise de mídia do Microsoft Azure."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Face redação com a análise de mídia do Microsoft Azure

##<a name="overview"></a>Visão geral

**Redactor de mídia do Azure** é um [Análise de mídia do Azure](media-services-analytics-overview.md) mídia processador () que oferece redação de face scalable na nuvem. Face redação permite que você modifique o vídeo para desfoque faces de pessoas selecionadas. Talvez você queira usar o serviço de redação de face em cenários de segurança e mídia de notícias públicos. Alguns minutos de filmagem em que contém várias faces podem levar horas para redigir manualmente, mas com esse serviço, o processo de redação face exigirão apenas algumas etapas simples. Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Este tópico fornece detalhes sobre **Redactor de mídia do Azure** e mostra como usá-lo com SDK de serviços de mídia para .NET.

O **Redactor de mídia do Azure** MP está no modo de visualização.

## <a name="face-redaction-modes"></a>Modos de redação nominal

Face redação trabalha detectar faces em cada quadro do vídeo e controlando o objeto de face ambas as frente e para trás em tempo, para que o mesmo indivíduo pode ser tênues de outros ângulos também. O processo automatizado de redação é muito complexo e não sempre produção 100% de saída desejada, por esse motivo a análise de mídia oferece com algumas maneiras para modificar a saída final.

Além de um modo totalmente automático, há um fluxo de trabalho de duas passagens que permite a seleção/de-selection de faces encontrados por meio de uma lista de IDs. Além disso, para tornar aleatório por ajustes de quadro MP usa um arquivo de metadados no formato JSON. Este fluxo de trabalho é dividido em modos de **Analisar** e **Redact** . Você pode combinar os dois modos em uma única passagem que executa ambas as tarefas em um trabalho; Esse modo é chamado **combinada**.

###<a name="combined-mode"></a>Modo combinado

Esse procedimento produzirá um mp4 redigida automaticamente sem qualquer manual de entrada.

Estágio|Nome do arquivo|Anotações
---|---|---
Entrada de ativos|foo.bar|Vídeo em formato WMV, MOV ou MP4
Configuração de entrada|Configuração de trabalho predefinidos|{'versão':'1.0 ', 'Opções': {'modo': 'combinados'}}
Ativos de saída|foo_redacted.mp4|Vídeo com Desfoque aplicado

####<a name="input-example"></a>Exemplo de entrada:

[assistir a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Exemplo de saída:

[assistir a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Analisar modo

A fase de **análise** do fluxo de trabalho duas passagens leva uma entrada de vídeo e produz um arquivo JSON dos locais de face e as imagens jpg de cada face detectado.

Estágio|Nome do arquivo|Anotações
---|---|----
Entrada de ativos|foo.bar|Vídeo em formato WMV, MPV ou MP4
Configuração de entrada|Configuração de trabalho predefinidos|{'versão':'1.0 ', 'Opções': {'modo': 'analisar'}}
Ativos de saída|foo_annotations.JSON|Dados de anotação dos locais de face no formato JSON. Isso pode ser editado pelo usuário para modificar a suavidade delimitadora caixas. Consulte o exemplo abaixo.
Ativos de saída|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|Jpg cortada de cada detectado nominal, onde o número indica a labelId da face

####<a name="output-example"></a>Exemplo de saída:

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… truncados


###<a name="redact-mode"></a>Redigir modo

A segunda fase do fluxo de trabalho leva um grande número de entradas que devem ser combinados em um único ativo.

Isso inclui uma lista de IDs de desfoque, vídeo original e as anotações JSON. Esse modo usa as anotações para aplicar desfoque sobre o vídeo de entrada.

A saída da fase de análise não inclui vídeo original. O vídeo precisa ser carregada para o ativo de entrada para a tarefa de modo Redact e selecionado como o arquivo principal.

Estágio|Nome do arquivo|Anotações
---|---|---
Entrada de ativos|foo.bar|Vídeo em formato WMV, MPV ou MP4. Mesmo vídeo como em etapa 1.
Entrada de ativos|foo_annotations.JSON|arquivo de metadados de anotações da fase um, com modificações opcionais.
Entrada de ativos|foo_IDList.txt (opcional)|Nova linha opcional lista separada de face IDs para redigir. Se deixado em branco, isso Desfoca todas as faces.
Configuração de entrada|Configuração de trabalho predefinidos|{'versão':'1.0 ', 'Opções': {'modo': 'Redigir'}}
Ativos de saída|foo_redacted.mp4|Vídeo com Desfoque aplicado com base em anotações

####<a name="example-output"></a>Exemplo de saída

Esta é a saída de um IDList com uma identificação selecionada.

[assistir a este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Descrições de atributo

O MP redação fornece detecção de localização de face de alta precisão e controle que pode detectar até 64 faces humanos em um quadro de vídeo. Faces frontal fornecem os melhores resultados, enquanto faces laterais e pequenos faces (menor ou igual a 24 x 24 pixels) desafiadoras.

As faces detectadas e controladas são retornadas com coordenadas que indica o local do faces, bem como uma face número de identificação, indicando que o controle dessa pessoa. Números de identificação de face estão sujeitos a redefinir circunstâncias quando a face frontal é perdida ou sobreposta no quadro, resultando em algumas pessoas obtendo atribuídas várias IDs.

Para obter explicações detalhadas dos atributos, consulte o tópico de [Face detectar e emoções com análises de mídia do Azure](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Código de exemplo

O seguinte programa mostra como:

1. Criar um ativo e carregar um arquivo de mídia para o ativo.
1. Crie um trabalho com uma tarefa de redação nominal com base em um arquivo de configuração que contém a seguintes predefinir json. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Baixe os arquivos JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Links relacionados

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
