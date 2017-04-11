<properties 
    pageTitle="Carregar arquivos para uma conta de serviços de mídia usando .NET | Microsoft Azure" 
    description="Saiba como obter o conteúdo de mídia em serviços de mídia criando e carregando ativos." 
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
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar arquivos para uma conta de serviços de mídia usando .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTANTE](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

Em serviços de mídia, você carrega (ou inclusão) os arquivos digitais até um ativo. A entidade de **ativos** pode conter vídeo, áudio, imagens, conjuntos de miniaturas, texto tracks e legenda codificada arquivos (e os metadados sobre esses arquivos.)  Depois que os arquivos são carregados, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming.

Os arquivos em ativo são chamados **Arquivos de ativos**. A instância de **AssetFile** e o arquivo de mídia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

>[AZURE.NOTE]As seguintes considerações se aplicam ao escolher um nome de arquivo ativo:
>
>- Serviços de mídia usa o valor da propriedade IAssetFile.Name quando estiver criando URLs para ver o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, a codificação de porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres de porcentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver apenas um '.' para a extensão de nome de arquivo.
>
>- O comprimento do nome não deve ser maior do que 260 caracteres.

Quando você cria ativos, você pode especificar as seguintes opções de criptografia. 

- **Nenhum** - sem criptografia é usado. Este é o valor padrão. Observe que ao usar essa opção seu conteúdo não está protegido em trânsito ou inativos em armazenamento.
Se você planeja fornecer um MP4 usando o download progressivo, use essa opção. 
- **CommonEncryption** - Use essa opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comuns ou PlayReady DRM (por exemplo, suave Streaming protegido com PlayReady DRM).
- **EnvelopeEncrypted** – Use esta opção se você estiver carregando HLS criptografados com AES. Observe que os arquivos devem foram codificados e criptografados por Gerenciador de transformar.
- **StorageEncrypted** - criptografa seu Limpar conteúdo localmente usando criptografia AES-256 bits e, em seguida, carregamentos ele para o armazenamento do Azure onde ele está armazenado criptografado inativos. Ativos protegidos com criptografia de armazenamento são automaticamente não criptografados e colocados em um sistema de arquivos criptografados antes de codificação e, opcionalmente, criptografados novamente antes de carregar volta como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte inativos no disco.

    Serviços de mídia fornece criptografia de armazenamento em disco para os ativos, não sobredurante a transmissão como o Gerenciador de direitos digitais (DRM).

    Se seu ativo for armazenamento criptografado, você deve configurar política de entrega de ativos. Para obter mais informações, consulte [Configurando política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

Se você especificar para seu ativo sejam criptografados com uma opção de **CommonEncrypted** ou uma opção de **EnvelopeEncypted** , você precisará associar seu ativo um **ContentKey**. Para obter mais informações, consulte [como criar um ContentKey](media-services-dotnet-create-contentkey.md). 

Se você especificar para seu ativo sejam criptografados com uma opção de **StorageEncrypted** , o SDK de serviços de mídia para .NET criará uma **StorateEncrypted** **ContentKey** para seu ativo.


Este tópico mostra como usar o SDK do .NET de serviços de mídia bem como extensões de SDK do .NET de serviços de mídia para carregar arquivos em um ativo de serviços de mídia.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Carregar um único arquivo com o SDK do .NET de serviços de mídia 

O código de exemplo a seguir usa o SDK do .NET para executar as seguintes tarefas: 

- Cria um ativo vazio.
- Cria uma instância de AssetFile que queremos associar o ativo.
- Cria uma instância de AccessPolicy que define as permissões e a duração do acesso ao ativo.
- Cria uma instância de localizador que fornece acesso ao ativo.
- Carrega um arquivo de mídia único em serviços de mídia. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Carregar vários arquivos com o SDK do .NET de serviços de mídia 

O código a seguir mostra como criar um ativo e carregar vários arquivos.

O código faz o seguinte:
    
-   Cria um ativo vazio usando o método CreateEmptyAsset definido na etapa anterior.
    
-   Cria uma instância de **AccessPolicy** que define as permissões e a duração do acesso ao ativo.
    
-   Cria uma instância de **localizador** que fornece acesso ao ativo.
    
-   Cria uma instância de **BlobTransferClient** . Esse tipo representa um cliente que funciona com os Azure blobs. Neste exemplo, usamos o cliente para monitorar o progresso de carregamento. 
    
-   Enumera através de arquivos na pasta especificada e cria uma instância de **AssetFile** para cada arquivo.
    
-   Carrega os arquivos em serviços de mídia usando o método **UploadAsync** . 
    
>[AZURE.NOTE] Use o método UploadAsync para garantir que as chamadas não estão bloqueando e os arquivos são carregados em paralelo.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Ao carregar um grande número de ativos, considere o seguinte.

- Crie um novo objeto de **CloudMediaContext** por thread. A classe de **CloudMediaContext** não é thread-safe.
 
- Aumente NumberOfConcurrentTransfers do valor padrão de 2 para um valor maior como 5. Definir esta propriedade afeta todas as instâncias de **CloudMediaContext**. 
 
- Manter ParallelTransferThreadCount no valor padrão de 10.
 
##<a id="ingest_in_bulk"></a>A inclusão de ativos em massa usando o SDK do .NET de serviços de mídia 

Carregando arquivos grandes de ativos pode ser um gargalo durante a criação de ativos. A inclusão de ativos em massa ou "Em massa inclusão", envolve a criação de ativos do processo de carregamento de separação. Para usar uma abordagem a inclusão de massa, crie um manifesto (IngestManifest) que descreva o ativo e os arquivos associados. Use o método de carregamento de sua preferência para carregar os arquivos associados ao contêiner de blob do manifesto. Serviços de mídia do Microsoft Azure inspeções contêiner de blob associado com o manifesto. Depois que um arquivo é carregado ao contêiner de blob, serviços de mídia do Microsoft Azure conclui a criação de ativos com base na configuração do ativo no manifesto (IngestManifestAsset).


Para criar uma nova chamada IngestManifest o método Create exposto pela coleção IngestManifests na CloudMediaContext. Esse método criará uma nova IngestManifest com o manifesto nome que você fornece.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Crie os ativos que serão associados a IngestManifest em massa. Configure as opções de criptografia desejado no ativo para a inclusão de massa.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Um IngestManifestAsset associa a uma massa IngestManifest para a inclusão de massa de um ativo. Ele também associa a AssetFiles que irão compor o cada ativo. Para criar um IngestManifestAsset, use o método Create no contexto do servidor.

O exemplo a seguir demonstra adicionando duas IngestManifestAssets novo que associa os dois ativos criados anteriormente para em massa inclusão manifesto. Cada IngestManifestAsset também associa um conjunto de arquivos que serão carregados para cada ativo durante a inclusão de massa.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Você pode usar qualquer aplicativo de cliente de alta velocidade capaz de carregar os arquivos de ativos ao contêiner de armazenamento de blob URI fornecido pela propriedade **IIngestManifest.BlobStorageUriForUpload** a IngestManifest. Um serviço de carregamento de alta velocidade notáveis é [Aspera sob demanda para o aplicativo do Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Você também pode escrever código para carregar os arquivos de ativos, conforme mostrado no exemplo de código a seguir.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

O código para carregar os arquivos de ativos para o exemplo usado neste tópico é mostrado no exemplo de código a seguir.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Você pode determinar o andamento da inclusão em massa para todos os ativos associados com uma **IngestManifest** sondagem a propriedade de estatísticas do **IngestManifest**. Para atualizar informações sobre o andamento, você deve usar um novo **CloudMediaContext** sempre que você pesquisar a propriedade de estatísticas.

O exemplo a seguir demonstra um IngestManifest pela sua **Id**de sondagem.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Carregar arquivos usando as extensões de SDK .NET 

O exemplo a seguir mostra como carregar um único arquivo usando .NET SDK extensões. Nesse caso o método de **CreateFromFile** é usado, mas a versão assíncrona também está disponível (**CreateFromFileAsync**). O método **CreateFromFile** permite especificar o nome do arquivo, opção de criptografia e um retorno de chamada para relatar o progresso de carregamento do arquivo.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

O exemplo a seguir chama UploadFile função e especifica a criptografia de armazenamento como a opção de criação de ativos.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Próxima etapa

Agora que você carregou um ativo com relação aos serviços de mídia, vá para o tópico de [como obter um processador de mídia][] .

[Como obter um processador de mídia]: media-services-get-media-processor.md
 
