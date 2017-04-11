<properties 
    pageTitle="Codificar um ativo com o codificador Media Standard usando .NET | Microsoft Azure" 
    description="Este tópico mostra como usar o .NET para codificar um ativo usando Strandard de codificador de mídia." 
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
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com o codificador Media Standard usando .NET

Codificação de trabalhos são uma das operações de processamento mais comuns nos serviços de mídia. Criar trabalhos de codificação para converter arquivos de mídia de uma codificação para outro. Quando você codificar, você pode usar o codificador de mídia internos de serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de serviços de mídia; codificadores de terceiros estão disponíveis por meio do Azure Marketplace. 

Este tópico mostra como usar o .NET para codificar seus ativos com mídia codificador padrão (MES). Mídia codificador padrão é configurada usando um do codificador predefinições descrito [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

É recomendável sempre codificar seus arquivos mezzanine em uma taxa de bits adaptativa MP4 definir e, em seguida, converter o conjunto para o formato desejado usando na [Embalagem dinâmico](media-services-dynamic-packaging-overview.md). Para tirar proveito da embalagem dinâmico, você deve primeiro obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [como serviços de mídia de escala](media-services-portal-manage-streaming-endpoints.md).

Se seu ativo de saída for armazenamento criptografado, você deve configurar política de entrega de ativos. Para obter mais informações, consulte [Configurando política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES produz um arquivo de saída com um nome que contém os primeiros 32 caracteres do nome do arquivo de entrada. O nome é baseado em que está especificado no arquivo predefinido. Por exemplo, "NomeArquivo": "_ {Basename} {índice} {extensão}". {Basename} é substituído pelos primeiros 32 caracteres do nome do arquivo de entrada.

###<a name="mes-formats"></a>Formatos MES

[Codecs e formatos](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>Predefinições MES

Mídia codificador padrão é configurada usando um do codificador predefinições descrito [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadados de entrada e saído

Quando codificar um ativo de entrada (ou ativos) usando MES, você receberá um ativo de saída o bem-sucedido codificar de conclusão dessa tarefa. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc. com base na codificação predefinir que você usar.

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: < asset_id > _metadata.xml (por exemplo, 41114ad3-eb5e - 4c 57-8d 92-5354e2b7d4a4_metadata.xml), onde < asset_id > é o valor de idasset do ativo entrado. O esquema de entrada metadados XML é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: < source_file_name > _manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema de saída metadados XML é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se você desejar examinar um destes dois arquivos de metadados, você pode criar um localizador SAS e baixe o arquivo em seu computador local. Você pode encontrar um exemplo sobre como criar um localizador SAS e baixar um arquivo usando extensões de SDK do .NET de serviços de mídia.

##<a name="download-sample"></a>Exemplo de download

Obtenha e executar uma amostra de [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Exemplo

O exemplo de código a seguir usa o SDK do .NET de serviços de mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o codificador de mídia codificador padrão.
- Especificar para usar o "H264 taxa de vários bits 720p" predefinido. Você pode ver todas as predefinições [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Você também pode examinar o esquema ao qual essas predefinições devem estar em conformidade tópico [aqui](https://msdn.microsoft.com/library/mt269962.aspx) .
- Adicione uma única tarefa de codificação ao trabalho. 
- Especifique o entrada ativo a ser codificada.
- Crie um ativo de saída que conterá o codificado ativo.
- Adicione um manipulador de eventos para verificar o andamento do trabalho.
- Envie o trabalho.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
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


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também 

[Como gerar miniatura usando mídia codificador padrão com .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Visão geral sobre codificação de serviços de mídia](media-services-encode-asset.md)
