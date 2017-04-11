<properties
    pageTitle="Detectar nominal e emoções com a análise de mídia do Microsoft Azure | Microsoft Azure"
    description="Este tópico demonstra como detectar faces e emoções com análises de mídia do Azure."
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
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detectar nominal e emoções com a análise de mídia do Microsoft Azure

##<a name="overview"></a>Visão geral

O processador de mídia do **Azure mídia Face Detector** (MP) permite que você contar, acompanhar os movimentos e até mesmo medir participação de audiência e resposta por meio de expressões de faciais. Este serviço contém dois recursos: 

- **Detecção de face**

    Detecção de face localiza e rastreia faces humanos dentro de um vídeo. Várias faces podem ser detectadas e subsequentemente ser rastreados conforme eles movimentam, com os metadados de hora e local retornado em um arquivo JSON. Durante o controle, ele tentará atribuir uma ID consistente para a mesma face enquanto a pessoa é mover-se na tela, mesmo se eles são obstruídos ou deixe resumidamente o quadro.

    >[AZURE.NOTE]Este serviços não executará reconhecimento de face. Um indivíduo que deixa o quadro ou fica obstruído muito longo receberá uma nova ID quando eles retornarem.

- **Detecção de emoções**
    
    Detecção de emoções é um componente opcional do processador Face detecção de mídia que retorna análise em vários atributos emocionais de faces detectadas, incluindo felicidade, tristeza, medo, irritação e muito mais. 

O **Detector de Face de mídia do Azure** MP está no modo de visualização.

Este tópico fornece detalhes sobre **Detector de Face de mídia do Azure** e mostra como usá-lo com SDK de serviços de mídia para .NET.

##<a name="face-detector-input-files"></a>Face arquivos de entrada do Detector

Arquivos de vídeo. Atualmente, os formatos a seguir são suportados: MP4, MOV e WMV.

##<a name="face-detector-output-files"></a>Face Detector arquivos de saída

A API de detecção e acompanhamento de face fornece detecção de localização de face de alta precisão e controle que pode detectar até 64 faces humanos em um vídeo. Faces frontal fornecem os melhores resultados, enquanto faces laterais e pequenos faces (menor ou igual a 24 x 24 pixels) podem não ser tão precisos.

As faces detectadas e controladas são retornadas com coordenadas (esquerda, superior, largura e altura) que indica o local de faces na imagem em pixels, bem como um número de identificação de face indicando que o controle dessa pessoa. Números de identificação de face estão sujeitos a redefinir circunstâncias quando a face frontal é perdida ou sobreposta no quadro, resultando em algumas pessoas obtendo atribuídas várias IDs.

###<a id="output_elements"></a>Elementos do arquivo JSON de saída

Para a detecção de face e operação de rastreamento, o resultado de saída contém os metadados das faces dentro do determinado arquivo no formato JSON.

A detecção de face e controle JSON inclui os seguintes atributos:

Elemento|Descrição
---|---
Versão|Refere-se a versão da API do vídeo.
Escala de tempo|"Escalas" por segundo do vídeo.
Deslocamento|Este é o deslocamento de tempo para carimbos de hora. Na versão 1.0 de APIs de vídeo, isso sempre será 0. No futuro cenários que oferecemos suporte, esse valor pode mudar.
Taxa de quadros|Quadros por segundo do vídeo.
Fragmentos|Os metadados é blocos para cima em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalo e evento (s).
Iniciar|A hora de início do primeiro evento no 'escalas'.
Duração|O comprimento do fragmento, em "escalas".
Intervalo|O intervalo de cada entrada de evento dentro do fragmento, em "escalas".
Eventos|Cada evento contém as faces detectada e rastreada dentro dessa duração do tempo. É uma matriz de matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna consiste em 0 ou mais eventos que aconteceram nesse momento no tempo. Um [] vazio colchete significa que nenhuma faces foram detectadas.
ID| A ID da face que está sendo controlada. Esse número pode alterar inadvertidamente se uma face ficar não detectada. Uma determinada pessoa deve ter o mesmo ID em todo o vídeo geral, mas isso não é possível garantir devido a limitações do algoritmo de detecção (oclusão, etc.)
X, Y|Superior esquerda X e Y coordenadas da face caixa delimitadora em uma escala normalizada do 0,0 a 1,0. <br/>-X e Y coordenadas forem relativas para paisagem sempre, portanto se você tiver um retrato vídeo (ou de cabeça para baixo, no caso de iOS), você terá para transpor as coordenadas adequadamente.
Largura, altura|A largura e altura da face caixa delimitadora em uma escala normalizada do 0,0 a 1,0.
facesDetected|Isso é encontrado no final dos resultados JSON e resume o número de faces que o algoritmo detectado durante o vídeo. Porque as IDs podem ser redefinidas inadvertidamente se uma face ficar detectada (por exemplo, nominal vai fora da tela, aparências ausente), esse número não pode ser igual sempre o número verdadeiro de faces no vídeo.

Face Detector usa técnicas de fragmentação (onde os metadados podem ser dividido em blocos com base em tempo e você pode baixar somente o que é necessário) e segmentação (onde os eventos são divididos em caso eles obtêm muito grandes). Alguns cálculos simples podem ajudá-lo a transformar os dados. Por exemplo, se um evento iniciado em 6300 (escalas), com uma escala de tempo de 2997 (escalas/sec) e taxa de quadros de 29,97 (quadros sec), então:

- Início/escala de tempo = 2.1 segundos
- Segundos x (taxa de quadros/escala de tempo) = 63 quadros

Abaixo está um exemplo simples de extrair o JSON em um por formato de quadro para a detecção de face e acompanhamento:
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Voltado para a detecção de entrada e saída de exemplo

###<a name="input-video"></a>Entrada de vídeo

[Entrada de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuração de tarefa (predefinir)

Ao criar uma tarefa com **Detector de Face de mídia do Azure**, você deve especificar um predefinir de configuração. A seguintes predefinir configuração é apenas para a detecção de face.

    {"version":"1.0"}

###<a name="json-output"></a>Saída JSON

O exemplo a seguir de saída JSON foi truncado.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Detecção de emoções de entrada e saída exemplo


###<a name="input-video"></a>Entrada de vídeo

[Entrada de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuração de tarefa (predefinir)

Ao criar uma tarefa com **Detector de Face de mídia do Azure**, você deve especificar um predefinir de configuração. Especifica a seguir predefinir de configuração para criar JSON com base na detecção de emoções.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descrições de atributo

Nome do atributo|Descrição
---|---
Modo|Faces: Somente enfrentam detecção <br/>AggregateEmotion: Os valores de retorno emoções média de todas as faces no quadro.
AggregateEmotionWindowMs|Use se AggregateEmotion modo selecionado. Especifica o comprimento de vídeo usado para produzir cada resultado de agregação, em milissegundos.
AggregateEmotionIntervalMs|Use se AggregateEmotion modo selecionado. Especifica com que frequência para produzir resultados agregados.

####<a name="aggregate-defaults"></a>Padrões de agregação

Abaixo são recomendados valores para as configurações de janela e intervalo de agregação. AggregateEmotionWindowMs deve ser maior que AggregateEmotionIntervalMs.

   |Padrões da (s)|Max(s)|Min (s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>Saída JSON

JSON saída para agregar emoções (truncada):
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitações

- Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
- O intervalo de tamanho de face detectáveis é 24 x 24 a 2048 x 2048 pixels. As faces fora desse intervalo não serão detectadas.
- Para cada vídeo, o número máximo de faces retornado é 64.
- Algumas faces podem não ser detectados devido a desafios técnicos; Por exemplo, muito grandes ângulos de face (cabeça-pose) e oclusão grande. Faces frontal e perto frontal têm os melhores resultados.


## <a name="sample-code"></a>Código de exemplo

O seguinte programa mostra como:

1. Criar um ativo e carregar um arquivo de mídia para o ativo.
1. Cria um trabalho com uma tarefa de detecção de face com base em um arquivo de configuração que contém a seguintes predefinir json. 
                    
        {
            "version": "1.0"
        }

1. Baixa os arquivos JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Links relacionados

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)