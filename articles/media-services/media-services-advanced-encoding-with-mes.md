<properties 
    pageTitle="Advanced codificação com mídia codificador padrão" 
    description="Este tópico mostra como executar a codificação avançada personalizando mídia codificador padrão predefinições de tarefa. O tópico mostra como usar o SDK do .NET de serviços de mídia para criar uma tarefa de codificação e trabalho. Ele também mostra como fornecer predefinições personalizadas para o trabalho de codificação." 
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
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Advanced codificação com mídia codificador padrão

##<a name="overview"></a>Visão geral

Este tópico mostra como executar tarefas avançadas de codificação com o codificador de mídia padrão. O tópico mostra [como usar o .NET para criar uma tarefa de codificação e um trabalho que executa essa tarefa](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Ele também mostra como fornecer predefinições personalizadas para a tarefa de codificação. Para descrição dos elementos que são usadas pelo predefinições, consulte [Este documento](https://msdn.microsoft.com/library/mt269962.aspx). 

As predefinições personalizadas que executam as seguintes tarefas de codificação são demonstrou:

- [Gerar miniaturas](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Cortar um vídeo (recorte)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Criar uma sobreposição](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Inserir uma faixa de áudio silenciosa quando entrada tiver sem áudio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Desabilitar automático eliminação entrelaçamento](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Predefinições somente de áudio](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concatenar dois ou mais arquivos de vídeo](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Vídeos de corte com mídia codificador padrão](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Inserir uma faixa de vídeo quando entrada não tem vídeo](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Girar um vídeo](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Codificação com serviços de mídia SDK .NET

O exemplo de código a seguir usa o SDK do .NET de serviços de mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o codificador de mídia codificador padrão.
- Carregar o XML personalizado ou JSON predefinidos. Você pode salvar o XML ou JSON (por exemplo, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) ou [JSON](media-services-custom-mes-presets-with-dotnet.md#json) em um arquivo e use o seguinte código para carregar o arquivo.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Adicione uma tarefa de codificação ao trabalho. 
- Especifique o entrada ativo a ser codificada.
- Crie um ativo de saída que contém o ativo codificado.
- Adicione um manipulador de eventos para verificar o andamento do trabalho.
- Envie o trabalho.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>Suporte para tamanhos relativos

Ao gerar miniaturas dele, você não precisa sempre especificar saída largura e altura em pixels. Você pode especificá-las em porcentagens, no intervalo [% de 1,..., 100%].

###<a name="json-preset"></a>Predefinir JSON 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>Predefinir XML
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Gerar miniaturas

Esta seção mostra como personalizar um predefinir que gera miniaturas. O predefinir definido abaixo contém informações sobre como você deseja codificar seu arquivo bem como informações necessárias para gerar miniaturas. Você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e adicionar código que gera miniaturas.  

>[AZURE.NOTE]A configuração de **SceneChangeDetection** no seguinte predefinido só poderá ser definida como true, se você estiver codificando para uma taxa de bits única vídeo. Se você estiver codificando um vídeo multi-taxa de bits e defina **SceneChangeDetection** como true, o codificador retornará um erro.  


Para obter informações sobre o esquema, consulte [Este](https://msdn.microsoft.com/library/mt269962.aspx) tópico.

Certifique-se examine a seção [Considerações](media-services-custom-mes-presets-with-dotnet.md#considerations) .

###<a id="json"></a>Predefinir JSON


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>Predefinir XML


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Considerações

Aplica considerando o seguinte:

- O uso de explícitos carimbos de hora de início/etapa/intervalo pressupõe que a fonte de entrada é pelo menos 1 minuto.
- Elementos de Png/jpg/BmpImage tem início, etapa e atributos de cadeia de caracteres de intervalo – estes podem ser interpretadas como:

    - Número de quadros se estiverem inteiros não negativos, por exemplo "começar": "120,"
    - Relativo à fonte duração se expresso como sufixo %, por exemplo "começar": "15%", ou
    - Carimbo de hora se expresso como hh... Formatar, por exemplo "começar": "00: 01:00"

    Você pode misturar e corresponder notações durante a envie.
    
    Além disso, iniciar também dá suporte a uma Macro especial: {melhor}, que tenta determinar o primeiro quadro "interessante" da nota de conteúdo: (etapa e intervalo são ignoradas quando iniciar é definida como {melhor})
    
    - Padrões: Iniciar: {melhor}
- Formato de saída precisa ser fornecida explicitamente para cada formato de imagem: Png/Jpg/BmpFormat. Quando elas estiverem presentes, MES corresponde JpgVideo para JpgFormat e assim por diante. OutputFormat introduz uma nova Macro específico de imagem-codec: {índice}, que precisa ser apresentam (uma vez e apenas uma vez) para formatos de saída de imagem.

##<a id="trim_video"></a>Cortar um vídeo (recorte)

Esta seção fala sobre modificando predefinições codificador para cortar ou aparar o vídeo de entrada onde a entrada é um arquivo de mezzanine chamados ou sob demanda. O codificador também pode ser usado para cortar ou aparar um ativo, o que é capturado ou arquivado a partir de um fluxo ao vivo – os detalhes para isso estão disponíveis [neste](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)blog.

Para aparar vídeos, você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e modifique o elemento de **fontes** (conforme mostrado abaixo). O valor da hora de início precisa coincidir com os carimbos de hora absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro do vídeo de entrada tem um carimbo de hora do 12:00:10.000, em seguida, hora de início deve ter pelo menos 12:00:10.000 e maior. No exemplo abaixo, vamos supor que o vídeo de entrada tem um carimbo de hora inicial igual a zero. **Fontes** devem ser colocadas no início do valor predefinido. 
 
###<a id="json"></a>Predefinir JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>Predefinir XML
    
Para aparar vídeos, você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx) e modifique o elemento de **fontes** (conforme mostrado abaixo).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Criar uma sobreposição

O padrão de codificador de mídia permite uma imagem em um vídeo existente de sobreposição. Atualmente, há suporte para os seguintes formatos: png, jpg, gif e bmp. O predefinir definido abaixo é um exemplo básico de uma sobreposição de vídeo.

Além de definir um arquivo predefinido, você também precisa informar serviços de mídia do qual arquivo nesse ativo é a imagem de sobreposição e qual é o vídeo de origem no qual você deseja a imagem de sobreposição. O arquivo de vídeo deve ser o arquivo **principal** . 

O exemplo de .NET acima define duas funções: **UploadMediaFilesFromFolder** e **EncodeWithOverlay**. A função UploadMediaFilesFromFolder carregamentos de arquivos de uma pasta (por exemplo, BigBuckBunny.mp4 e Image001.png) e define o arquivo mp4 a ser o arquivo principal no ativo. A função **EncodeWithOverlay** usa arquivo predefinido personalizado que foi passado para ele (por exemplo, o valor de predefinido que segue) para criar a tarefa de codificação. 

>[AZURE.NOTE]Limitações atuais:
>
>Não há suporte para a configuração de opacidade de sobreposição.
>
>O arquivo de vídeo de origem e o arquivo de imagem de sobreposição têm que estar no mesmo ativo e o arquivo de vídeo precisa ser definido como o arquivo principal neste ativo.

###<a name="json-preset"></a>Predefinir JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>Predefinir XML
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Inserir uma faixa de áudio silenciosa quando entrada tiver sem áudio

Por padrão, se você enviar uma entrada com o codificador que contenha apenas vídeo e nenhum áudio, em seguida, o ativo de saída contém de arquivos que contêm dados somente vídeo. Alguns players podem não ser capazes de lidar com tais fluxos de saída. Você pode usar esta configuração para forçar o codificador para adicionar uma faixa de áudio silenciosa na saída nesse cenário.

Para forçar o codificador para produzir um ativo que contém uma faixa de áudio silenciosa quando entrada não tem áudio, especifique o valor de "InsertSilenceIfNoAudio".

Você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e verifique a modificação a seguir:

###<a name="json-preset"></a>Predefinir JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>Predefinir XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Desabilitar automático eliminação entrelaçamento

Clientes não precisará fazer mais nada se desejarem o conteúdo de entrelaçamento sejam automaticamente eliminação entrelaçado. Quando o automático eliminação entrelaçamento estiver ativado (padrão) a MES faz a detecção automática de quadros entrelaçados e somente eliminação as interfaces quadros marcados como entrelaçado.

Você pode desativar o auto eliminação entrelaçamento. Esta opção não é recomendável.

###<a name="json-preset"></a>Predefinir JSON
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>Predefinir XML
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Predefinições somente de áudio

Esta seção demonstra duas predefinições MES somente de áudio: áudio AAC e AAC boa qualidade de áudio.

###<a name="aac-audio"></a>Áudio AAC 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>Áudio de boa qualidade AAC

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Concatenar dois ou mais arquivos de vídeo

O exemplo a seguir ilustra como você pode gerar um Predefinir para concatenar duas ou mais arquivos de vídeo. O cenário mais comum é quando você deseja adicionar um cabeçalho ou um rodapé para o vídeo principal. O uso pretendido é quando os arquivos de vídeo que está sendo editados juntos compartilham propriedades (resolução de vídeo, taxa de quadro, contagem de faixa de áudio, etc.). Você deve ter cuidado para não misturar vídeos de taxas de quadro diferente ou com um número diferente de faixas de áudio.

###<a name="requirements-and-considerations"></a>Requisitos e considerações

- Vídeos de entrada devem ter apenas uma faixa de áudio.
- Vídeos de entrada devem ter a mesma taxa de quadro.
- Você deve carregar vídeos em ativos separados e definir os vídeos como o arquivo principal em cada ativo.
- Você precisa saber a duração de vídeos.
- Os exemplos predefinidos abaixo supõe que todos os vídeos de entrada começa com um carimbo de hora igual a zero. Você precisa modificar os valores de hora de início se os vídeos tem carimbo de hora inicial diferente, como normalmente é o caso com arquivos ao vivo.
- Predefinir o JSON torna referências explícitas aos valores idasset dos ativos de entrada.
- O código de exemplo pressupõe que o predefinir JSON foi salva em um arquivo local, como "C:\supportFiles\preset.json". Ele também pressupõe que dois ativos foram criados pelo carregando dois arquivos de vídeo e que você sabe os valores de idasset resultantes.
- O trecho de código e JSON predefinir mostra um exemplo de concatenar dois arquivos de vídeo. Você pode estendê-lo para mais de dois vídeos por:

    1. Tarefa de chamada. InputAssets.Add() repetidamente para adicionar mais vídeos, em ordem.
    2. Tornar correspondente edita ao elemento "Fontes" em JSON, adicionando mais entradas, na mesma ordem. 


###<a name="net-code"></a>Código .NET

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>Predefinir JSON

Atualize seu personalizado predefinido com ids de ativos que você deseja concatenar e o segmento de tempo apropriado para cada vídeo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Vídeos de corte com mídia codificador padrão

Consulte o tópico de [vídeos de corte com mídia codificador padrão](media-services-crop-video.md) .

##<a id="no_video"></a>Inserir uma faixa de vídeo quando entrada não tem vídeo

Por padrão, se você enviar uma entrada com o codificador que contenha apenas áudio e nenhum vídeo, em seguida, o ativo de saída contém de arquivos que contêm dados somente áudio. Alguns players, incluindo Azure Media Player (consulte [Este](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) não poderá lidar com esses fluxos. Você pode usar esta configuração para forçar o codificador para adicionar uma faixa de vídeo monocromática na saída nesse cenário. 

>[AZURE.NOTE]Forçar o codificador para inserir um controle de vídeo de saída aumenta o tamanho da saída ativo, e, portanto, os custos incorridos para a tarefa de codificação. Você deve executar testes para confirmar que esse aumento resultante tem apenas um pouco mais impacto sobre suas cobranças mensais.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Inserir vídeo no somente a taxa de bits mais baixa

Suponha que você está usando uma taxa de bits vários codificação predefinidos como ["H264 taxa de vários bits 720p"](https://msdn.microsoft.com/library/mt269960.aspx) para codificar seu catálogo de entrada inteiro do streaming, que contém uma mistura de arquivos de vídeo e somente de áudio. Neste cenário, quando a entrada não tem vídeo, você talvez queira forçar o codificador inserir uma faixa de vídeo monocromática apenas a menor taxa de bits, em vez de inserir vídeo em cada taxa de bits de saída. Para fazer isso, você precisa especificar o sinalizador de "InsertBlackIfNoVideoBottomLayerOnly".

Você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e verifique a modificação a seguir:

#### <a name="json-preset"></a>Predefinir JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Predefinir XML

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Inserção de vídeo em todos os bitrates de saída

Suponha que você está usando uma taxa de bits vários codificação predefinidos como ["H264 taxa de vários bits 720p](https://msdn.microsoft.com/library/mt269960.aspx) para codificar seu catálogo de entrada inteiro do streaming, que contém uma mistura de arquivos de vídeo e somente de áudio. Neste cenário, quando a entrada não tem vídeo, você talvez queira forçar o codificador para inserir uma faixa de vídeo monocromática todo o bitrates de saída. Isso garante que a saída ativos são todos homogêneos relacionadas com número de faixas de áudio e vídeo faixas. Para fazer isso, você precisa especificar o sinalizador de "InsertBlackIfNoVideo".

Você pode executar qualquer uma da MES predefinições documentadas [aqui](https://msdn.microsoft.com/library/mt269960.aspx)e verifique a modificação a seguir:

#### <a name="json-preset"></a>Predefinir JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Predefinir XML
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Girar um vídeo

O [Codificador de mídia padrão](media-services-dotnet-encode-with-media-encoder-standard.md) é compatível com rotação por ângulos de 0/90/180/270. O comportamento padrão é "Automático", onde ele tenta detectar os metadados de rotação no arquivo de vídeo entrado e compensar a ele. Inclua o seguinte elemento de **fontes** para uma das predefinições definido [aqui](http://msdn.microsoft.com/library/azure/mt269960.aspx):

### <a name="json-preset"></a>Predefinir JSON

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>Predefinir XML

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Consulte também [deste](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) tópico para obter mais informações sobre como o codificador interprete as configurações de largura e altura no Predefinir, quando for disparada remuneração de rotação.

Você pode usar o valor "0" para indicar com o codificador por ignorar metadados de rotação, se estiver presente, o vídeo de entrada. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também 

[Visão geral de codificação de serviços de mídia](media-services-encode-asset.md)
