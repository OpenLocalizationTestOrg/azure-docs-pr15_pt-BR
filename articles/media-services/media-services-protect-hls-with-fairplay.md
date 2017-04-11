<properties 
    pageTitle="Proteger sua HLS conteúdo com Apple FairPlay e/ou Microsoft PlayReady | Microsoft Azure" 
    description="Este tópico fornece uma visão geral e mostra como usar os serviços de mídia do Azure para criptografar dinamicamente seu conteúdo de Streaming Live HTTP (HLS) com o Apple FairPlay. Ele também mostra como usar o serviço de entrega de licença de serviços de mídia para fornecer FairPlay licenças aos clientes." 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Proteger sua HLS conteúdo com Apple FairPlay e/ou Microsoft PlayReady

Serviços de mídia do Azure permite que você criptografe dinamicamente seu conteúdo de Streaming Live HTTP (HLS) usando os seguintes formatos:  

- **Chave Limpar AES-128 envelope** 

    A parte inteira está criptografada usando o modo **AES-128 CBC** . A descriptografia do fluxo é suportada pelo iOS e player OSX nativamente. Para obter mais informações, consulte [Este artigo](media-services-protect-with-aes128.md).

- **Apple FairPlay** 

    As amostras individuais de vídeo e áudio são criptografadas usando o modo **AES-128 CBC** . **FairPlay Streaming** (FPS) é integrado aos sistemas operacionais de dispositivo, com o suporte nativo no iOS e TV da Apple. Safari nos X permite que os quadros/s usando o suporte de interface de extensões de mídia criptografado (EME).
- **Microsoft PlayReady**

A imagem a seguir mostra o fluxo de trabalho **HLS + FairPlay e/ou PlayReady criptografia dinâmico** .

![Proteger com FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tópico demonstra como usar os serviços de mídia do Azure para criptografar dinamicamente o conteúdo HLS com FairPlay da Apple. Ele também mostra como usar o serviço de entrega de licença de serviços de mídia para fornecer FairPlay licenças aos clientes.

>[AZURE.NOTE] Se você também quiser criptografar conteúdo HLS por PlayReady, você precisa criar uma chave de conteúdo comuns e associá-lo com seu ativo. Você também precisa configurar política de autorização da chave de conteúdo, conforme descrito no tópico [PlayReady usando criptografia comuns dinâmico](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Requisitos e considerações

- A seguir é necessária ao usar AMS para oferecer HLS criptografados com FairPlay e para fornecer FairPlay licenças.

    - Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Uma conta de serviços de mídia. Para criar uma conta de serviços de mídia, consulte [Criar conta](media-services-portal-create-account.md).
    - Inscreva-se com o [Programa de desenvolvimento de Apple](https://developer.apple.com/).
    - Apple requer o proprietário do conteúdo obter o [pacote de implantação](https://developer.apple.com/contact/fps/). Declare a solicitação que você já implementado KSM (módulo de segurança de chave) com os serviços de mídia do Azure e que você está solicitando o pacote de quadros/s final. Haverá instruções no pacote de quadros/s final para gerar certificação e obter ASK, que você usará para configurar FairPlay. 

    - De versão do Azure Media Services .NET SDK **3.6.0** ou posterior.

- As seguintes ações devem ser definidas no lado do AMS entrega chave:
    - **Certificado de aplicativo (AC)** - arquivo. pfx contendo chave privada. Esse arquivo é criado pelo cliente e criptografado com uma senha pelo cliente mesmo. 
        
        Quando o cliente configura política de entrega chave, ele devem fornecer essa senha e a. pfx no formato base64.

        As etapas a seguir descrevem como gerar um certificado pfx para FairPlay.
        
        1. Instalar OpenSSL da https://slproweb.com/products/Win32OpenSSL.html
        
            Vá para a pasta onde o certificado de FairPlay e outros arquivos entregues pela Apple estão.
        
        2. Linha de comando para converter a cer em pem:
        
            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-no fairplay.cer-out fairplay-out.pem
        
        3. Linha de comando para converter pem pfx com a chave privada (a senha para o arquivo pfx depois é solicitada por OpenSSL).
        
            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportar - out fairplay-out.pfx-privatekey.pem inkey-no fairplay-out.pem - passagem file:privatekey-pem-pass.txt 
        
    - **Certificado de aplicativo senha** - senha do cliente para criar o arquivo. pfx.
    - **Certificado de aplicativo senha ID** - o cliente deve carregar a senha semelhante a como eles carregam outras chaves de AMS e usando o valor de enumeração **ContentKeyType.FairPlayPfxPassword** . Como resultado, elas receberão id AMS que este é o que eles precisam usar dentro a opção de política de entrega de chave.
    - **iv** - valor aleatório de 16 bytes, deve corresponder o iv na política de entrega de ativos. Cliente gera o IV e coloca em dois lugares: ativo política e chave entrega política opção de entrega. 
    - **PERGUNTAR** - ASK (aplicativo secreta chave) for recebida quando você gera a certificação usando o portal do desenvolvedor da Apple. Cada equipe de desenvolvimento receberá um ASK exclusivo. Salve uma cópia do ASK e armazená-la em um lugar seguro. Você precisará configurar ASK como FairPlayAsk aos serviços de mídia do Azure mais tarde. 
    -  **Pergunte ID** - é obtido quando o cliente carrega ASk em AMS. O cliente deve carregar ASk usando o valor de enumeração **ContentKeyType.FairPlayASk** . Como resultado, a id de AMS é retornada e isso é o que deve ser usado ao configurar a opção de política de entrega chave.

- As seguintes ações devem ser definidas pelo lado do cliente quadros/s:
    - **Certificado de aplicativo (AC)** -.cer/.der arquivo contendo chave pública que OS usa para criptografar alguns carga. AMS precisa saber sobre ele, porque ele é necessário pelo player. O serviço de entrega chave descriptografa usando a chave privada correspondente.

- Para um fluxo de criptografada FairPlay a reprodução, você precisa obter ASK real primeiro e, em seguida, gerar um certificado real. Esse processo cria todas as partes de 3:

    -  .der, 
    -  . pfx e 
    -  a senha para a. pfx.
 
- Clientes que oferecem suporte a HLS com a criptografia **AES de 128 CBC** : Safari nos X, TV da Apple, iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Etapas para configurar FairPlay dinâmico criptografia e licença serviços de entrega

A seguir estão as etapas gerais que você precisará executar ao proteger seus ativos com FairPlay, usando o serviço de entrega de licença de serviços de mídia e também usando criptografia dinâmico.

1. Criar um ativo e carregar arquivos para o ativo. 
1. Codifica o ativo que contém o arquivo para a taxa de bits adaptativa que MP4 definir.
1. Criar uma chave de conteúdo e associá-lo com o ativo codificado.  
1. Configure diretiva de autorização da chave de conteúdo. Ao criar a política de autorização de chave de conteúdo, você precisa especificar o seguinte: 
    
    - método de entrega (neste caso, FairPlay), 
    - Configuração de opções de política de FairPlay. Para obter detalhes sobre como configurar FairPlay, consulte o método de ConfigureFairPlayPolicyOptions() no exemplo abaixo.
    
        >[AZURE.NOTE] Geralmente, você gostaria de configurar as opções de política de FairPlay apenas uma vez, pois só terá um conjunto de certificação e ASK.
-restrições (abertas ou token), - e informações específicas para o tipo de entrega chave que define como a chave seja entregue ao cliente. 
    
2. Configure política de entrega de ativos. A configuração de política de entrega inclui: 

    - protocolo de entrega (HLS), 
    - o tipo de criptografia dinâmico (comuns CBC criptografia) 
    - URL de aquisição de licença. 
    
    >[AZURE.NOTE]Se você quiser distribuir um fluxo criptografado com FairPlay + DRM outro, você precisa configurar políticas de entrega separado 
    >
    >- Um IAssetDeliveryPolicy configurar traço com CENC (PlayReady + WideVine) e suave com PlayReady. 
    >- Outro IAssetDeliveryPolicy configurar FairPlay para HLS

1. Crie um localizador de OnDemand para obter uma URL de streaming.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Usando FairPlay chave entrega por aplicativos do player/cliente

Os clientes podem desenvolver aplicativos do player usando o SDK do iOS. Para poder reproduzir conteúdo de FairPlay os clientes têm que implementar o protocolo de intercâmbio de licença. O protocolo de intercâmbio de licença não está especificado pela Apple. É até cada aplicativo como enviar solicitações de entrega chave. Os serviços de entrega de chave AMS FairPlay espera SPC cheguem como mensagem de postagem codificado www-form-url da seguinte forma: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player não dá suporte a reprodução de FairPlay prontos para uso. Os clientes precisam obter o player de amostra de conta de desenvolvedor do Apple para obter FairPlay reprodução no MAC OSX. 
 
##<a name="streaming-urls"></a>URLs de streaming

Se seu ativo foi criptografado com DRM mais de um, você deve usar uma marca de criptografia na URL streaming: (formato = 'm3u8-aapl', criptografia = 'xxx').

Aplica considerando o seguinte:

- Somente zero ou um tipo de criptografia pode ser especificado.
- Tipo de criptografia não precisa ser especificado na url se apenas uma criptografia foi aplicada ao ativo.
- Tipo de criptografia diferencia maiusculas de minúsculas.
- Os seguintes tipos de criptografia podem ser especificados:  
    - **cenc**: criptografia comum (Playready ou Widevine)
    - **cbcs-aapl**: Fairplay
    - **CBC**: criptografia AES de envelope.


##<a name="net-example"></a>Exemplo de .NET


O exemplo a seguir demonstra funcionalidades que foi introduzida no SDK de serviços de mídia do Azure para .net-versão 3.6.0 (a capacidade de usar os serviços de mídia do Azure para distribuir o conteúdo criptografado com FairPlay). O seguinte comando pacote Nuget foi usado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Crie um projeto de Console.
1. Utilize o NuGet para instalar e adicionar Azure Media Services .NET SDK.
2. Adicionar referências adicionais: System. Configuration.
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

1. Obtenha pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte: [Configurar streaming de pontos de extremidade](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Substitua o código no seu arquivo Program.cs com o código mostrado nesta seção.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
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
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
        
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
        
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
        
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
        
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Open",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                                    Requirements = null
                                }
                            };
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Token Authorization Policy",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                    Requirements = tokenTemplateString,
                                }
                            };
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
                        assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


##<a name="next-steps-media-services-learning-paths"></a>Próximas etapas: Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
