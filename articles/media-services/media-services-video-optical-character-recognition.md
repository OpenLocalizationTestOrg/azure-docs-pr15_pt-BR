<properties
    pageTitle="Usar a análise de mídia do Azure para converter o conteúdo de texto em arquivos de vídeo em texto digital | Microsoft Azure"
    description="Azure OCR de análise de mídia (reconhecimento óptico de caracteres) permite que você converter o conteúdo de texto em arquivos de vídeo em texto digital pesquisável editável.  Isso permite automatizar a extração de metadados significativo do sinal de vídeo de sua mídia."
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Usar a análise de mídia do Azure para converter o conteúdo de texto em arquivos de vídeo em texto digital 

##<a name="overview"></a>Visão geral

Se você precisar extrair o conteúdo de texto de seus arquivos de vídeo e gerar um texto digital editável, pesquisável, você deve usar Azure mídia Analytics OCR (reconhecimento óptico de caracteres). Este processador de mídia do Azure detecta conteúdo de texto em que você arquivos de vídeo e gera arquivos de texto para seu uso. OCR permite automatizar a extração de metadados significativo do sinal de vídeo de sua mídia.

Quando usado em conjunto com um mecanismo de pesquisa, pode facilmente indexar sua mídia por texto e aprimorar a capacidade de descoberta do seu conteúdo. Isso é muito útil vídeo altamente textual, como uma gravação de vídeo ou a captura de tela de uma apresentação de slides. O processador de mídia do Azure OCR é otimizado para texto digital.

O processador de mídia do **Azure mídia OCR** está no modo de visualização.

Este tópico fornece detalhes sobre **OCR de mídia do Azure** e mostra como usá-lo com SDK de serviços de mídia para .NET. Para obter informações adicionais e exemplos, consulte [Este blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>Arquivos de entrada de OCR

Arquivos de vídeo. Atualmente, os formatos a seguir são suportados: MP4, MOV e WMV.

##<a name="task-configuration"></a>Configuração de tarefa 

Configuração de tarefa (predefinir). Ao criar uma tarefa com **OCR de mídia do Azure**, você deve especificar uma configuração predefinida usando JSON ou XML. 

###<a name="attribute-descriptions"></a>Descrições de atributo

Nome do atributo|Descrição
---|---
Idioma|(opcional) descreve o idioma do texto para o qual deseja pesquisar. Um dos seguintes: detecção automática (padrão), árabe, ChineseSimplified, chinês tradicional, República Tcheca dinamarquês, holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polonês, português, romeno, russo, SerbianCyrillic, SerbianLatin, eslovaco, espanhol, sueco, turco.
TextOrientation|(opcional) descreve a orientação do texto para o qual deseja pesquisar.  "Esquerda" significa que a parte superior de todas as letras é direcionado em direção à esquerda.  Texto padrão (como o que pode ser encontrado em um livro) pode ser chamado "Para cima" a usá-lo.  Um dos seguintes: detecção automática (padrão), para cima, à direita, para baixo, para a esquerda.
TimeInterval|(opcional) descreve a taxa de amostragem.  Padrão é cada segundo de 1/2.<br/>Formato JSON – hh. SSS (00:00:00.500 padrão)<br/>Formato XML – W3C XSD duração primitivo (padrão PT0.5)
DetectRegions|(opcional) Uma matriz de objetos DetectRegion especificando regiões dentro do quadro de vídeo no qual detectar texto.<br/>Um objeto de DetectRegion é feito dos seguintes valores quatro inteiros:<br/>Esquerda – pixels a partir da margem esquerda<br/>Início – pixels a partir da margem superior<br/>Largura – largura da região em pixels<br/>Altura – altura da região em pixels

####<a name="json-preset-example"></a>Exemplo de predefinido JSON
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>Exemplo de XML predefinido

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>Arquivos de saída de OCR

A saída do processador mídia OCR é um arquivo JSON.

###<a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

A saída de vídeo OCR fornece dados segmentado tempo os caracteres encontrados em seu vídeo.  Você pode usar atributos como idioma ou orientação telefônica-in exatamente as palavras que você está interessado na análise. 

A saída contém os seguintes atributos:

Elemento|Descrição
---|---
Escala de tempo|"escalas" por segundo do vídeo
Deslocamento|tempo de deslocamento para carimbos de hora. Na versão 1.0 de APIs de vídeo, isso sempre será 0.
Taxa de quadros|Quadros por segundo do vídeo
Largura|largura do vídeo em pixels
altura|altura do vídeo em pixels
Fragmentos|matriz de blocos baseada no tempo de vídeo no qual os metadados é blocos
Iniciar|Iniciar o tempo de um fragmento em "escalas"
duração|comprimento de um fragmento em "escalas"
intervalo|intervalo de cada evento dentro do fragmento determinado
eventos|matriz contendo regiões
região|objeto representando detectado palavras ou frases
idioma|idioma do texto detectado dentro de uma região
orientação|orientação do texto detectado dentro de uma região
linhas|matriz de linhas de texto detectado dentro de uma região
texto|o texto real

###<a name="json-output-example"></a>Exemplo de saída JSON

O exemplo de saída a seguir contém as informações gerais sobre o vídeo e vários fragmentos de vídeo. Em cada fragmento de vídeo, ele contém todas as regiões que é detectada por OCR MP com o idioma e a sua orientação de texto. A região também contém todas as linhas do word nesta região com o texto da linha, posição da linha e todas as informações do word (conteúdo do word, posição e confiança) nessa linha. A seguir é um exemplo e coloquei alguns embutida de comentários.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Código de exemplo

O seguinte programa mostra como:

1. Criar um ativo e carregar um arquivo de mídia para o ativo.
1. Cria um trabalho com um arquivo de configuração/predefinir OCR.
1. Baixa os arquivos JSON de saída. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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
