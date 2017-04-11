<properties
    pageTitle="Arquivos de mídia de Hyperlapse com Hyperlapse de mídia do Microsoft Azure | Microsoft Azure"
    description="Azure Hyperlapse de mídia cria suaves vídeos de tempo decorrido do conteúdo da primeira pessoa ou câmera de ação. Este tópico mostra como usar o indexador de mídia."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Arquivos de mídia de Hyperlapse com Hyperlapse de mídia do Microsoft Azure

Azure Hyperlapse de mídia é uma mídia processador (MP) que cria suaves vídeos de tempo decorrido do conteúdo da primeira pessoa ou câmera de ação.  Irmãos baseado em nuvem [da Microsoft Research desktop Hyperlapse Pro](http://aka.ms/hyperlapse)e baseado em telefone Hyperlapse Mobile, Microsoft Hyperlapse para serviços de mídia do Azure utiliza a escala de massa da plataforma de processamento de mídia do Azure Media Services para dimensionar e paralelo horizontalmente em massa Hyperlapse processamento.

>[AZURE.IMPORTANT]Microsoft Hyperlapse foi projetado para funcionar melhor na primeira pessoa conteúdo com uma câmera de movimentação.  Embora filmagem em câmera ainda ainda pode funcionar, o desempenho e a qualidade do Azure Media Hyperlapse mídia processador não não possível garantir para outros tipos de conteúdo.  Para saber mais sobre o Microsoft Hyperlapse para serviços de mídia do Azure e ver alguns vídeos de exemplo, confira a [postagem de blog introdutório](http://aka.ms/azurehyperlapseblog) da visualização pública.

Um Hyperlapse de mídia do Azure trabalho leva como entrada um arquivo de ativo MP4, MOV ou WMV juntamente com um arquivo de configuração que especifica quais quadros de vídeo devem ser o tempo decorrido e para quais velocidade (por exemplo, primeiro 10.000 quadros em x 2).  O resultado é uma representação estabilizada e tempo decorrido o vídeo de entrada.

Para as últimas atualizações de Hyperlapse de mídia do Azure, consulte [blogs de serviços de mídia](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse um ativo

Primeiro, você precisará carregar o arquivo de entrada desejado para serviços de mídia do Azure.  Para saber mais sobre conceitos envolvidos Carregando e gerenciamento de conteúdo, leia o [artigo de gerenciamento de conteúdo](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Predefinir de configuração para Hyperlapse

Depois que seu conteúdo estiver em sua conta de serviços de mídia, você precisará construir predefinir sua configuração.  A tabela a seguir explica os campos especificados pelo usuário:

 Campo | Descrição
-------|-------------
StartFrame|O quadro no qual o processamento de Hyperlapse Microsoft deve começar.
NumFrames|O número de quadros para processar
Velocidade|O fator com a qual acelerar o vídeo de entrada.

Este é um exemplo de um arquivo de configuração compatível em XML e JSON:

**Predefinir XML:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**Predefinir JSON:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse com o SDK do .NET AMS

O seguinte método carrega um arquivo de mídia como um ativo e cria um trabalho com o processador de mídia do Azure mídia Hyperlapse.

> [AZURE.NOTE] Você já deve ter um CloudMediaContext no escopo com o nome "contexto" para este código funcione.  Para saber mais sobre isso, leia o [artigo de gerenciamento de conteúdo](media-services-dotnet-get-started.md).

> [AZURE.NOTE] O argumento de cadeia de caracteres "hyperConfig" deve ser uma configuração compatível predefinida em JSON ou XML conforme descrito acima.

bool estático RunHyperlapseJob (entrada de cadeia de caracteres, saída de cadeia de caracteres, hyperConfig de cadeia de caracteres) {/ / criar ativo com ativo do arquivo de entrada IAsset = contexto. Ativos. CreateAssetAndUploadSingleFile (entrada, "Meu entrada Hyperlapse", AssetCreationOptions.None);

Pegue instâncias do Azure Media Hyperlapse MP IMediaProcessor mp = contexto. MediaProcessors. GetLatestMediaProcessorByName ("Azure Media Hyperlapse");

Criar trabalho com Hyperlapse IJob trabalho = contexto. Trabalhos. Criar (Format ("Hyperlapse {0}", entrada));

Se (String.IsNullOrEmpty(hyperConfig)) {/ / config não pode ser vazia false retorno;}

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = trabalho. Tasks.AddNew ("Hyperlapse tarefas", mp, hyperConfig, TaskOptions.None); hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew ("Hyperlapse saída", AssetCreationOptions.None);


trabalho. Submit();

Criar a impressão de andamento e consultando tarefas tarefa progressPrintTask = novo Task(() = > {

IJob jobQuery = null; Faça {var progressContext = contexto; jobQuery = progressContext.Jobs. Onde (j = > j.Id = = trabalho. ID). First(); Console. WriteLine (cadeia de caracteres. Formato ("{0} \t {1} \t {2}", Now, jobQuery.State, jobQuery.Tasks[0]. Andamento)); Thread.Sleep(10000); } enquanto (jobQuery.State! = JobState.Finished & & jobQuery.State! = JobState.Error & & jobQuery.State! = JobState.Canceled); }); progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Tipos de arquivo compatíveis

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Links relacionados

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
