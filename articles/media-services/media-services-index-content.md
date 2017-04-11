<properties
    pageTitle="A indexação de arquivos de mídia com indexador de mídia do Microsoft Azure"
    description="Azure indexador de mídia permite que você para tornar o conteúdo dos seus arquivos de mídia pesquisável e gerar uma transcrição de texto completo para legendas codificadas ou palavras-chave. Este tópico mostra como usar o indexador de mídia."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>A indexação de arquivos de mídia com indexador de mídia do Microsoft Azure


Azure indexador de mídia permite que você para tornar o conteúdo dos seus arquivos de mídia pesquisável e gerar uma transcrição de texto completo para legendas codificadas ou palavras-chave. É possível processar arquivos de mídia de um ou vários arquivos de mídia em um lote.  

>[AZURE.IMPORTANT] Quando a indexação de conteúdo, verifique se usar arquivos de mídia que têm muito clara fala (sem música de fundo, ruído, efeitos ou assovio de microfone). Alguns exemplos de conteúdo apropriado são: gravado reuniões, palestras ou apresentações. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, nada com áudio misto e efeitos de som gravado mal conteúdo com ruído de fundo (assovio).


Um trabalho de indexação pode gerar saídas seguintes:

- Arquivos de legenda nos seguintes formatos fechados: **SAMI**, **TTML**e **WebVTT**.

    Arquivos de legenda codificada incluir uma marca chamada Recognizability, que classifica um trabalho de indexação dependendo da possa ser reconhecida como a fala no vídeo de origem.  Você pode usar o valor de Recognizability para arquivos de saída de tela de usabilidade. Uma pontuação baixa significa má resultados indexação devido a qualidade de áudio.
- Arquivo de palavra-chave (XML).
- Áudio indexação arquivo blob (AIB) para uso com o SQL server.

    Para obter mais informações, consulte [Usando arquivos de AIB com indexador de mídia do Azure e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Este tópico mostra como criar trabalhos de indexação para **índice de um ativo** e **vários arquivos de índice**.

As atualizações mais recentes do indexador de mídia do Azure, consulte [blogs de serviços de mídia](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Usando arquivos de configuração e manifesto para tarefas de indexação

Você pode especificar mais detalhes de suas tarefas de indexação usando uma configuração de tarefa. Por exemplo, você pode especificar quais metadados para seu arquivo de mídia. Esses metadados é usado pelo mecanismo de idioma para expandir seu vocabulário e bastante melhora a precisão do reconhecimento de fala.  Você também é capazes de especificar seus arquivos de saída desejado.

Você também pode processar vários arquivos de mídia de uma vez, usando um arquivo de manifesto.

Para obter mais informações, consulte [Tarefas predefinidos para indexador de mídia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexar um ativo

O seguinte método carrega um arquivo de mídia como um ativo e cria um trabalho para indexar ativo.

Observe que se nenhum arquivo de configuração for especificado, o arquivo de mídia será indexado com todas as configurações padrão.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
<!-- __ -->
### <a id="output_files"></a>Arquivos de saída

Por padrão, um trabalho de indexação gera os seguintes arquivos de saída. Os arquivos serão armazenados no primeiro ativo de saída.

Quando houver mais de um arquivo de mídia de entrada, indexador irá gerar um arquivo de manifesto para saídas trabalho, chamado 'JobResult.txt'. Para cada arquivo de mídia de entrada, a AIB resultante, SAMI, TTML, WebVTT e arquivos de palavra-chave, são sequencialmente numerados e nomeados usando o "Alias".

Nome do arquivo | Descrição
----------|------------
__InputFileName.aib__ | Arquivo de blob indexação áudio. <br /><br /> Arquivo de áudio Blob indexação (AIB) é um arquivo binário que pode ser pesquisado no Microsoft SQL server usando a pesquisa de texto completo.  O arquivo AIB é mais avançado do que os arquivos de legenda simples, porque ele contém alternativas para cada palavra, permitindo uma experiência de pesquisa muito mais avançada. <br/> <br/>Ela requer a instalação do complemento indexador SQL em um computador executando Microsoft SQL server 2008 ou posterior. Pesquisando o AIB usando o Microsoft SQL pesquisa de texto completo do servidor fornece resultados de pesquisa mais precisos que a pesquisa os arquivos de legenda codificada gerados pelo WAMI. Isso ocorre porque o AIB contém palavras alternativas que som semelhante enquanto os arquivos de legenda codificada contêm a palavra mais alta de confiança para cada segmento do áudio. Se estiver procurando palavras faladas de importância upmost, em seguida, é recomendável usar a conjunção AIB com o Microsoft SQL Server.<br/><br/> Para baixar o complemento, clique em <a href="http://aka.ms/indexersql">Complemento de SQL Azure mídia indexador</a>. <br/><br/>Também é possível utilizar outros mecanismos de pesquisa como Apache Lucene/Solr simplesmente indexar o vídeo com base na legenda codificada e arquivos XML de palavra-chave, mas isso resultará em menos precisos resultados de pesquisa.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Arquivos de legenda (CC) fechada em formatos SAMI, TTML e WebVTT.<br/><br/>Eles podem ser usados para tornar os arquivos de áudio e vídeo acessíveis a pessoas com deficiência auditiva.<br/><br/>Arquivos de legenda fechados incluir uma marca chamada <b>Recognizability</b> que classifica um trabalho de indexação com base em possa ser reconhecida como a fala no vídeo de origem é.  Você pode usar o valor de <b>Recognizability</b> para arquivos de saída de tela de usabilidade. Uma pontuação baixa significa má resultados indexação devido a qualidade de áudio.
__InputFileName.kw.xml<br />InputFileName.info__ |Arquivos de palavra-chave e informações. <br/><br/>Arquivo de palavra-chave é um arquivo XML que contém palavras-chave extraídas do conteúdo fala, com frequência e informações de deslocamento. <br/><br/>Arquivo de informações é um arquivo de texto sem formatação que contém informações completas sobre cada termo reconhecido. A primeira linha é especial e contém a pontuação de Recognizability. Cada linha subsequente é uma lista separado por tabulação dos dados a seguir: Iniciar hora, hora de término, word/frase, confiança. Os tempos são fornecidos em segundos e a confiança é dado como um número de 0 a 1. <br/><br/>Exemplo de linha: "1,20 1,45 word 0,67" <br/><br/>Esses arquivos podem ser usados para diversos fins, tais como, para executar a análise de fala, ou expostos para mecanismos de pesquisa, como Bing, Google ou Microsoft SharePoint para tornar os arquivos de mídia mais detectáveis, ou até mesmo usadas para entregar anúncios mais relevantes.
__JobResult.txt__ |Manifesto de saída, presente somente quando a indexação de vários arquivos, que contém as seguintes informações:<br/><br/><table border="1"><tr><th>Arquivo de entrada</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

## <a name="index-multiple-files"></a>Indexar vários arquivos

O seguinte método carrega vários arquivos de mídia como um ativo e cria um trabalho para todos esses arquivos em um lote de índice.

Um arquivo de manifesto com a extensão de lst é criada e upload em ativo. O arquivo de manifesto contém a lista de todos os arquivos de ativos. Para obter mais informações, consulte [Tarefas predefinidos para indexador de mídia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Trabalho parcialmente bem-sucedida

Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).


As mesmas saídas (como trabalhos bem-sucedida) são geradas. Você pode fazer referência ao arquivo de manifesto de saída para descobrir quais arquivos entrados apresentam falha acordo com os valores da coluna de erro. Arquivos de entrada que falhou, o AIB resultante, SAMI, TTML, WebVTT e a palavra-chave arquivos não serão gerados.

### <a id="preset"></a>Predefinir de tarefa para indexador de mídia do Microsoft Azure

O processamento do indexador de mídia do Azure pode ser personalizado, fornecendo uma tarefa opcional predefinida junto com a tarefa.  A seguir descreve o formato do xml configuração.

Nome | Exigir | Descrição
----|----|---
__entrada__ | FALSO | Ativo (s) que você deseja indexar.</p><p>Azure indexador de mídia suporta os seguintes formatos de arquivo de mídia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Você pode especificar o nome de arquivo (s) no **nome** ou **lista de** atributo do elemento **input** (conforme mostrado abaixo). Se você não especificar qual arquivo ativo índice, o arquivo primário será separado. Se nenhum arquivo ativo principal estiver definido, o primeiro arquivo na entrada ativo é indexado.</p><p>Para especificar explicitamente o nome do arquivo ativo, faça:<br />`<input name="TestFile.wmv">`<br /><br />Você também pode indexar vários ativos arquivos ao mesmo tempo (até 10). Para fazer isso:<br /><br /><ol class="ordered"><li><p>Criar um arquivo de texto (arquivo manifesto) e dar a ele uma extensão de lst. </p></li><li><p>Adicione uma lista de todos os nomes de arquivo ativo no seu ativo de entrada para esse arquivo de manifesto. </p></li><li><p>Adicione (carregar) thanifest arquivo ao ativo.  </p></li><li><p>Especifique o nome do arquivo manifesto no atributo de lista de entrada.<br />`<input list="input.lst">`</li></ol><br /><br />Observação: Se você adicionar mais de 10 arquivos para o arquivo de manifesto, o trabalho de indexação falhará com o código de erro de 2006.
__metadados__ | FALSO | Metadados para arquivos ativos especificado usados para adaptação vocabulário.  É útil para preparar indexador reconhecer palavras de vocabulário não padrão como nomes próprios.<br />`<metadata key="..." value="..."/>` <br /><br />Você pode fornecer __valores__ para predefinidas __teclas__. As chaves a seguir são suportadas:<br /><br />"título" e "Descrição" - usado para adaptação vocabulário para ajustar o idioma de modelo para o seu trabalho e melhorar a precisão do reconhecimento de fala.  Os valores de propagação pesquisas na Internet para encontrar documentos relevantes contextualmente texto, usando o conteúdo para aumentar o dicionário interno para a duração da sua tarefa de indexação.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__recursos__ <br /><br /> Adicionado na versão 1.2. Atualmente, o único recurso suportado é o reconhecimento de fala ("ASR").| FALSO | O recurso de reconhecimento de fala tem as seguintes chaves de configurações:<table><tr><th><p>Chave</p></th>     <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Idioma</p></td><td><p>O idioma natural a ser reconhecida no arquivo multimídia.</p></td><td><p>Inglês, espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista separada por ponto e vírgula dos formatos de legenda de saída desejada (se houver)</p></td><td><p>ttml; sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Um sinalizador booliano especificando estando ou não um arquivo AIB é necessário (para uso com o SQL Server e o cliente indexador IFilter).  Para obter mais informações, consulte <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Usando arquivos de AIB com indexador de mídia do Azure e SQL Server</a>.</p></td><td><p>Verdadeiro; FALSO</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Um sinalizador booliano especificando estando ou não é necessário um arquivo XML de palavra-chave.</p></td><td><p>Verdadeiro; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Um sinalizador booliano especificando se deseja ou não forçar legendas completas (independentemente do nível de confiança).  </p><p>Padrão é falso, nesse caso palavras e frases que têm um menor que o nível de confiança de 50% são omitidos saídas legenda final e substituídos por reticências ("...").  Nas reticências são úteis para auditoria e controle de qualidade de legenda.</p></td><td><p>Verdadeiro; False. </p></td></tr></table>

### <a id="error_codes"></a>Códigos de erro

No caso de um erro, reporte indexador de mídia do Azure faça um dos seguintes códigos de erro:

Código | Nome | Razões possíveis
-----|------|------------------
2000 | Configuração inválida | Configuração inválida
2001 | Ativos de entrada inválidos | Faltando entrados ativos ou ativos vazio.
2002 | Manifesto inválido | Manifesto está vazio ou manifesto contém itens inválidos.
2003 | Falha no download do arquivo de mídia | URL inválido no arquivo de manifesto.
2004 | Protocolo sem suporte | Não há suporte para o protocolo de URL de mídia.
2005 | Tipo de arquivo sem suporte | Não há suporte para o tipo de arquivo de mídia de entrada.
2006 | Muitos arquivos de entrada | Há mais de 10 arquivos no manifesto de entrada.
3000 | Falha ao decodificar o arquivo de mídia | Codec de mídia sem suporte <br/>ou<br/> Arquivo de mídia corrompida <br/>ou<br/> Nenhum fluxo de áudio na mídia de entrada.
4000 | Indexação de lotes parcialmente bem-sucedida | Alguns dos arquivos de mídia de entrada são Falha ao ser indexadas. Para obter mais informações, consulte <a href="#output_files">arquivos de saída</a>.
outros | Erros internos | Entre em contato com a equipe de suporte. indexer@microsoft.com


## <a id="supported_languages"></a>Idiomas com suporte

Atualmente, há suporte para os idiomas inglês e espanhol. Para obter mais informações, consulte [a postagem no blog v 1.2 lançamento](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Links relacionados

[Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md)

[Usando arquivos AIB com indexador de mídia do Microsoft Azure e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[A indexação de arquivos de mídia com o Azure Media indexador 2 Preview](media-services-process-content-with-indexer2.md)
