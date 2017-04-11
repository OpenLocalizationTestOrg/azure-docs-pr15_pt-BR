<properties
    pageTitle="Detectar movimentos com a análise de mídia do Microsoft Azure | Microsoft Azure"
    description="O Detector de movimento de mídia do Azure mídia processador () permite que você identifique com eficiência seções de interesse dentro de um vídeo caso contrário normal e por extenso."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Detectar movimentos com a análise de mídia do Microsoft Azure

##<a name="overview"></a>Visão geral

O **Detector de movimento de mídia do Azure** mídia processador () permite que você identifique com eficiência seções de interesse dentro de um vídeo caso contrário normal e por extenso. Detecção de movimento pode ser usada em filmagem em câmera estático para identificar seções do vídeo onde o movimento ocorre. Ele gera um arquivo JSON que contém um metadados com carimbos de hora e a região delimitadora onde o evento ocorreu.

Dirigido feeds de vídeo de segurança, essa tecnologia é capaz de categorizar movimento em eventos relevantes e falsos positivos como sombras e alterações de iluminação. Isso permite gerar alertas de segurança de feeds de câmera sem recebam com eventos sem importância infinitas, e ser capaz de extrair momentos de interesse de vídeos de vigilância muito longa.

O **Detector de movimento de mídia do Azure** MP está no modo de visualização.

Este tópico fornece detalhes sobre **Detector de movimento de mídia do Azure** e mostra como usá-lo com SDK de serviços de mídia para .NET


##<a name="motion-detector-input-files"></a>Arquivos de entrada de Detector de movimento

Arquivos de vídeo. Atualmente, os formatos a seguir são suportados: MP4, MOV e WMV.

##<a name="task-configuration-preset"></a>Configuração de tarefa (predefinir)

Ao criar uma tarefa com **Detector de movimento de mídia do Azure**, você deve especificar um predefinir de configuração. 

###<a name="parameters"></a>Parâmetros

Você pode usar os seguintes parâmetros:

Nome|Opções|Descrição|Padrão
---|---|---|---
sensitivityLevel|Cadeia de caracteres: 'baixa', 'Média', 'alta'|Define o nível de sensibilidade pelo quais movimentos é informado. Ajuste dessa maneira para ajustar a quantidade de falsos positivos.|'Média'
frameSamplingValue|Inteiro positivo|Define a frequência na qual o algoritmo é executado. cada quadro igual a 1, 2 significa que cada quadro 2a e assim por diante.|1
detectLightChange|Booliana: 'true', 'false'|Define se alterações light são relatadas nos resultados|'False'
mergeTimeThreshold|X-hora: Hh<br/>Exemplo: 00:00:03|Especifica a janela de tempo entre eventos de movimento onde 2 eventos serão combinados e relatados como 1.|00:00:00
detectionZones|Uma matriz de zonas de detecção:<br/>-Zona de detecção é uma matriz de 3 ou mais pontos<br/>-Ponto é um x e y coordenadas de 0 a 1.|Descreve a lista de zonas de detecção em forma de polígono a ser usado.<br/>Os resultados serão relatados com zonas como uma ID, com a primeira sendo 'id': 0|Única zona que abrange todo o quadro.

###<a name="json-example"></a>Exemplo JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Arquivos de saída de Detector de movimento

Um trabalho de detecção de movimento retornará um arquivo JSON o ativo de saída que descreve os alertas de movimento e suas categorias, dentro do vídeo. O arquivo conterá informações sobre a hora e a duração de movimento detectado no vídeo.

A API de Detector de movimento fornece indicadores quando há objetos em movimento em um plano fixo vídeo (por exemplo, um vídeo de inspeção). O Detector de movimento é treinamento reduzir alarmes falsos, como iluminação e alterações de sombra. Limitações atuais dos algoritmos incluem noite visão vídeos, objetos semitransparentes e pequenos objetos.

###<a id="output_elements"></a>Elementos do arquivo JSON de saída

>[AZURE.NOTE]Na versão mais recente, o formato de saída JSON foi alterado e pode representar uma alteração significativa para alguns clientes.

A tabela a seguir descreve os elementos do arquivo JSON de saída.

Elemento|Descrição
---|---
Versão|Refere-se a versão da API do vídeo. A versão atual é 2.
Escala de tempo|"Escalas" por segundo do vídeo.
Deslocamento|O deslocamento de tempo para carimbos de hora em "escalas". Na versão 1.0 de APIs de vídeo, isso sempre será 0. No futuro cenários que oferecemos suporte, esse valor pode mudar.
Taxa de quadros|Quadros por segundo do vídeo.
Largura, altura|Refere-se para a largura e altura do vídeo em pixels.
Iniciar|O carimbo de hora de início em "escalas".
Duração|O comprimento do evento, em "escalas".
Intervalo|O intervalo de cada entrada no evento, em "escalas".
Eventos|Cada fragmento de evento contém o movimento detectado dentro dessa duração do tempo.
Tipo|Na versão atual, isso é sempre '2' para movimento genérico. Isso dá rótulo vídeo APIs flexibilidade para categorizar futuramente movimento versões.
RegionID|Conforme explicado acima, isso sempre será 0 nesta versão. Este rótulo oferece a flexibilidade para localizar o movimento em várias regiões em versões futuras de API de vídeo.
Regiões|Refere-se para a área no vídeo onde que me preocupar com movimento. <br/><br/>-"id" representa a área de região – nesta versão há apenas um, ID 0. <br/>-"tipo" representa a forma da região que me preocupar com para animação. Atualmente, "retângulo" e "polígono" são suportados.<br/> Se você especificou "retângulo", a região tem dimensões no X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY de superior esquerdo da região em uma escala normalizada de 0,0 a 1,0. A largura e altura representam o tamanho da região em uma escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, largura e altura são sempre fixos em 0, 0 e 1, 1. <br/>Se você especificou "polígono", a região tem dimensões em pontos. <br/>
Fragmentos|Os metadados é blocos para cima em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalo e evento (s). Um fragmento com nenhum eventos significa que nenhuma movimento foi detectado durante essa hora de início e a duração.
Colchetes]|Cada colchete representa um intervalo no evento. Colchetes vazios para intervalo não significa que nenhuma animação foi detectado.
locais|Essa nova entrada em eventos de lista o local onde o movimento ocorreu. Isso é mais específico do que as zonas de detecção.

O exemplo a seguir é um exemplo de saída JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitações

- Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
- Detecção de movimento é otimizada para vídeos de plano de fundo parado. O algoritmo se concentra nos reduzindo alarmes falsos, como alterações de iluminação e sombras.
- Alguns movimento pode não ser detectado devido a desafios técnicos; Por exemplo, noite visão vídeos, objetos semitransparentes e pequenos objetos.


## <a name="sample-code"></a>Código de exemplo

O seguinte programa mostra como:

1. Criar um ativo e carregar um arquivo de mídia para o ativo.
1. Cria um trabalho com uma tarefa de detecção de movimento de vídeo com base em um arquivo de configuração que contém a seguintes predefinir json. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Baixa os arquivos JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Blog do Azure Detector de movimento de serviços de mídia](https://azure.microsoft.com/blog/motion-detector-update/)

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
