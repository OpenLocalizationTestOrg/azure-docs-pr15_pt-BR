<properties
    pageTitle="Usando criptografia dinâmico AES de 128 e o serviço de entrega chave | Microsoft Azure"
    description="Serviços de mídia do Microsoft Azure permite distribuir o conteúdo criptografado com chaves de criptografia AES de 128 bits. Serviços de mídia também fornece o serviço de entrega de chave que fornece chaves de criptografia aos usuários autorizados. Este tópico mostra como dinamicamente criptografar com AES de 128 e usar o serviço de entrega de chave."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Usando criptografia dinâmico AES de 128 e o serviço de entrega de chave

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Visão geral

>[AZURE.NOTE] Consulte [Este](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vídeo para obter uma visão geral de como proteger seu conteúdo de mídia com a criptografia AES.

Serviços de mídia do Microsoft Azure permite fornecer Http Live-Streaming (HLS) e suaves fluxos criptografados com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits). Serviços de mídia também fornece o serviço de entrega de chave que fornece chaves de criptografia aos usuários autorizados. Se você quiser para serviços de mídia criptografar um ativo, você precisa associar uma chave de criptografia do ativo e também configurar diretivas de autorização para a chave. Quando um fluxo é solicitado por um player, serviços de mídia usa a chave especificada para criptografar dinamicamente seu conteúdo usando criptografia AES. Para descriptografar o fluxo, o player solicitará a chave do serviço de entrega de chave. Para decidir se ou não o usuário está autorizado obter a chave, o serviço avalia as diretivas de autorização que você especificou para a chave.

Serviços de mídia oferece suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens nos [Tokens de Web simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, consulte [Configurar diretiva de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para tirar proveito de criptografia dinâmica, você precisa ter um ativo que contém um conjunto de arquivos de MP4 multi-taxa de bits ou arquivos de origem de Streaming suave multi-taxa de bits. Você também precisa configurar a política de entrega para o ativo (descrito neste tópico). Em seguida, com base em formato especificado na URL streaming, servidor On Demand Streaming garantirá que o fluxo seja entregue no protocolo que você escolheu. Como resultado, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia serão construir e servir a resposta apropriada com base em solicitações de um cliente.

Este tópico seria útil para os desenvolvedores que trabalham em aplicativos que fornecem mídia protegido. O tópico mostra como configurar o serviço de entrega de chave com diretivas de autorização para que somente a clientes autorizados podem receber as chaves de criptografia. Ele também mostra como usar a criptografia dinâmico.

>[AZURE.NOTE]Para começar a usar a criptografia dinâmico, você deve primeiro obter pelo menos uma unidade de escala (também conhecido como unidade de streaming). Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Criptografia dinâmico AES de 128 e fluxo de trabalho de serviço de entrega de chave

A seguir estão as etapas gerais que você precisará executar ao criptografar seus ativos com AES, usando o serviço de entrega de chave de serviços de mídia e também usando criptografia dinâmico.

1. [Criar um ativo e carregar arquivos para o ativo](media-services-protect-with-aes128.md#create_asset).
1. [Codificar o ativo que contém o arquivo para a taxa de bits adaptativa que MP4 definir](media-services-protect-with-aes128.md#encode_asset).
1. [Criar uma chave de conteúdo e associá-lo com o ativo codificado](media-services-protect-with-aes128.md#create_contentkey). Em serviços de mídia, a chave de conteúdo contém chave de criptografia do ativo.
1. [Configurar diretiva de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). A política de autorização de chave conteúdo deve ser configurada por você e atendida pelo cliente para que a chave de conteúdo a ser entregue ao cliente.
1. [Configurar a política de entrega de um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração de política de entrega inclui: URL de aquisição e vetor de inicialização (IV)-chave (AES 128 requer o mesmo IV a serem fornecidos ao criptografar e descriptografar), protocolo de entrega (por exemplo, MPEG traço, HLS, HDS, Streaming suave ou todos), o tipo de criptografia dinâmico (por exemplo, envelope ou nenhuma criptografia dinâmico).

Você pode aplicar diretiva diferente para cada protocolo no mesmo ativo. Por exemplo, você pode aplicar criptografia PlayReady suave/traço e AES Envelope para HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única diretiva que especifica apenas HLS como o protocolo) será impedido de streaming. A exceção é se você não tem nenhuma diretiva de entrega de ativos definida em todos os. Em seguida, todos os protocolos poderão ser criptografado.

1. [Criar um localizador de OnDemand](media-services-protect-with-aes128.md#create_locator) para obter uma URL de streaming.

O tópico também mostra [como um aplicativo cliente solicitar uma chave do serviço de entrega de chave](media-services-protect-with-aes128.md#client_request).

Você encontrará um.NET completo [exemplo](media-services-protect-with-aes128.md#example) no final do tópico.

A imagem a seguir demonstra o fluxo de trabalho descrito acima. Veja o token é usado para autenticação.

![Proteger com AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste tópico oferece explicações detalhadas, exemplos de código e links para tópicos que mostram como alcançar as tarefas descritas acima.

##<a name="current-limitations"></a>Limitações atuais

Se você adicionar ou atualizar política de entrega do seu ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.

##<a id="create_asset"></a>Criar um ativo e carregar arquivos para o ativo

Para gerenciar, codificar e vídeos de fluxo, você deve primeiro carregar seu conteúdo em serviços de mídia do Microsoft Azure. Uma vez carregado, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming. 

Para obter informações detalhadas, consulte [Carregar arquivos para uma conta de serviços de mídia](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificar o ativo que contém o arquivo para a taxa de bits adaptativa que MP4 definir

Com a criptografia dinâmica tudo o que você precisa é criar um ativo que contém um conjunto de arquivos de MP4 multi-taxa de bits ou arquivos de origem de Streaming suave multi-taxa de bits. Em seguida, com base no formato especificado na solicitação de manifesto ou fragmento, o Streaming de On Demand servidor garantirá que você recebe o fluxo no protocolo que você optou. Como resultado, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia serão construir e servir a resposta apropriada com base em solicitações de um cliente. Para obter mais informações, consulte o tópico [Visão geral de embalagem dinâmica](media-services-dynamic-packaging-overview.md) .

Para obter instruções sobre como codificar, consulte [como codificar um ativo usando mídia codificador padrão](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-lo com o ativo codificado

Em serviços de mídia, a chave de conteúdo contém a chave que você deseja criptografar um ativo com.

Para obter informações detalhadas, consulte [criar chave de conteúdo](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurar diretiva de autorização da chave de conteúdo

Serviços de mídia oferece suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave conteúdo deve ser configurada por você e atendida pelo cliente (player) para a tecla ser entregue para o cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir, símbolo restrição ou restrição de IP.

Para obter informações detalhadas, consulte [Configurar diretiva de autorização de chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurar política de entrega de ativos 

Configure a política de entrega para seu ativo. Algumas coisas que a configuração de política de entrega de ativos inclui:

- A URL de aquisição de chave. 
- A inicialização vetor (IV) a ser usado para a criptografia de envelope. AES 128 requer o mesmo IV a serem fornecidos ao criptografar e descriptografar. 
- O protocolo de entrega de ativo (por exemplo, MPEG traço, HLS, HDS, Streaming suave ou todos).
- O tipo de criptografia dinâmico (por exemplo, envelope AES) ou nenhum criptografia dinâmico. 

Para obter informações detalhadas, consulte [Configurar política de entrega de ativos ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Criar um OnDemand streaming localizador para obter uma URL de streaming

Você precisará fornecer seu usuário com a URL de streaming de suavização, traço ou HLS.

>[AZURE.NOTE]Se você adicionar ou atualizar política de entrega do seu ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.

Para obter instruções sobre como publicar um ativo e criar uma URL streaming, consulte [criar uma URL de streaming](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Obter um teste token

Obtenha um teste token com base na restrição token que foi usada para a política de autorização de chave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Você pode usar o [Player de AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar o fluxo.

##<a id="client_request"></a>Como seu cliente solicitar uma chave do serviço de entrega chave?

Na etapa anterior, você construído a URL que aponta para um arquivo de manifesto. Seu cliente precisa extrair as informações necessárias dos arquivos de manifesto streaming para fazer uma solicitação para o serviço de entrega de chave.

###<a name="manifest-files"></a>Arquivos de manifesto

O cliente precisa extrair a URL (que também contém conteúdo chave Id (crianças)) valor do arquivo de manifesto. O cliente tentará obter a chave de criptografia do serviço de entrega de chave. O cliente também precisa extrair o valor de IV e use-descriptografar o fluxo. O trecho de código a seguir mostra o <Protection> elemento do manifesto Streaming suave.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

No caso de HLS, o manifesto raiz é dividido em arquivos de segmento. 

Por exemplo, o manifesto raiz é: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) e ela contém uma lista de nomes de arquivo de segmento.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se você abrir um dos arquivos de segmento no editor de texto (por exemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), ele deve conter #EXT X-chave que indica que o arquivo está criptografado.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Solicitar a chave do serviço de entrega de chave

O código a seguir mostra como enviar uma solicitação para o serviço de entrega de chave de serviços de mídia usando uma chave entrega Uri (que foi extraída de manifesto) e um token (deste tópico não falar sobre como obter Tokens de Web simples de um serviço de Token de segurança).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Exemplo

1. Crie um novo projeto de Console.
1. Utilize o NuGet para instalar e adicionar extensões de SDK do Azure mídia serviços .NET. Instalar este pacote, também instala o SDK do .NET de serviços de mídia e adiciona todos os outras dependências necessárias.
2. Adicione o arquivo de configuração que contém o nome da conta e informações importantes:

    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Substitua o código no seu arquivo Program.cs com o código mostrado nesta seção.
    
    Certifique-se de atualizar variáveis para apontar para pastas onde os arquivos de entrada estão localizados.
            
        
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
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
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
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
                    Console.ReadLine();
                }
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
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
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
        
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
