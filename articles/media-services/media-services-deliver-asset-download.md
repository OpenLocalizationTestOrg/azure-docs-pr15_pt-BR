<properties 
    pageTitle="Baixar ativos de mídia" 
    description="Saiba sobre baixar ativos no seu computador. Exemplos de código escritos em c# e usam o SDK de serviços de mídia para .NET." 
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

#<a name="how-to-deliver-an-asset-by-download"></a>Como: fornecer um ativo para download

Este tópico aborda opções para fornecer ativos de mídia carregados aos serviços de mídia. Você pode fornecer conteúdo de serviços de mídia em vários cenários de aplicativos. Você pode fazer o download de ativos de mídia ou acessá-los usando um localizador. Você pode enviar o conteúdo de mídia para outro aplicativo ou para outro provedor de conteúdo. Para melhor desempenho e escalabilidade, você também pode fornecer conteúdo usando uma rede de entrega de conteúdo (CDN).

Este exemplo mostra como baixar ativos de mídia de serviços de mídia para seu computador local. O código de consulta os trabalhos associados à conta de serviços de mídia pela identificação de trabalho e acessa sua coleção de **OutputMediaAssets** (que é o conjunto de um ou mais ativos de mídia de saída que resulta da executando um trabalho). Este exemplo mostra como fazer o download de ativos de mídia de saída de um trabalho, mas você pode aplicar a mesma abordagem para baixar outros ativos.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Consulte também 

[Fornecer conteúdo streaming](media-services-deliver-streaming-content.md)

