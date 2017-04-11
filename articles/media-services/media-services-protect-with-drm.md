<properties
    pageTitle="Usando criptografia de comuns dinâmica PlayReady e/ou Widevine | Microsoft Azure"
    description="Serviços de mídia do Microsoft Azure permitem que você forneça fluxos MPEG-traço, Streaming suave e Streaming Live Http (HLS) protegidos com Microsoft PlayReady DRM. Ele também permite que você entrega traço criptografado com Widevine DRM. Este tópico mostra como criptografar dinamicamente com PlayReady e Widevine DRM."
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
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Usando o PlayReady e/ou Widevine criptografia comuns dinâmico

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Serviços de mídia do Microsoft Azure permitem que você forneça fluxos MPEG-traço, Streaming suave e Streaming Live HTTP (HLS) protegidos com [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Ele também permite que você fornecer os fluxos de traço criptografados com Widevine DRM licenças. PlayReady e Widevine são criptografados pela especificação de criptografia comum (ISO/IEC 23001-7 CENC). Você pode usar o [SDK do .NET AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou API REST para configurar seu AssetDeliveryConfiguration para usar Widevine.

Serviços de mídia fornece um serviço para oferecer PlayReady e Widevine DRM licenças. Serviços de mídia também fornece APIs que permitem que você configure os direitos e restrições que você deseja para o runtime PlayReady ou Widevine DRM impor quando um usuário reproduz conteúdo protegido. Quando um usuário solicita um conteúdo protegido por DRM, o aplicativo de player irá solicitar uma licença do serviço de licença do AMS. O serviço de licença AMS emite uma licença para o player se ele está autorizado. Uma licença PlayReady ou Widevine contém a chave de descriptografia que pode ser usada pelo player cliente descriptografar e transmitir o conteúdo.


Você também pode usar os seguintes parceiros AMS para ajudá-lo a oferecer Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, consulte: integração com [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Serviços de mídia oferece suporte a várias maneiras de autorização de usuários que fazem solicitações de chave. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens nos [Tokens de Web simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, consulte Configurar diretiva de autorização da chave de conteúdo.

Para tirar proveito de criptografia dinâmica, você precisa ter um ativo que contém um conjunto de arquivos de MP4 multi-taxa de bits ou arquivos de origem de Streaming suave multi-taxa de bits. Você também precisa configurar as políticas de entrega do ativo (descrito neste tópico). Em seguida, com base em formato especificado na URL streaming, servidor On Demand Streaming garantirá que o fluxo seja entregue no protocolo que você escolheu. Como resultado, você só precisa armazenar e pagar para os arquivos em um formato de armazenamento único e serviços de mídia será construir e servir a resposta HTTP apropriada com base em cada solicitação de um cliente.

Este tópico seria útil para os desenvolvedores que trabalham em aplicativos que fornecem mídia protegida com vários DRMs, como PlayReady e Widevine. O tópico mostra como configurar o serviço de entrega de licença PlayReady com diretivas de autorização para que somente a clientes autorizados podem receber licenças PlayReady ou Widevine. Ele também mostra como usar a criptografia de criptografia dinâmico com PlayReady ou Widevine DRM sobre traço.

>[AZURE.NOTE]Para começar a usar a criptografia dinâmico, você deve primeiro obter pelo menos uma unidade de escala (também conhecido como unidade de streaming). Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Exemplo de download

Você pode baixar o exemplo descrito neste artigo [aqui](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configurando criptografia comuns dinâmico e serviços de entrega de licença do DRM

A seguir estão as etapas gerais que você precisará executar ao proteger seus ativos com PlayReady, usando o serviço de entrega de licença de serviços de mídia e também usando criptografia dinâmico.

1. Criar um ativo e carregar arquivos para o ativo.
1. Codifica o ativo que contém o arquivo para a taxa de bits adaptativa que MP4 definir.
1. Criar uma chave de conteúdo e associá-lo com o ativo codificado. Em serviços de mídia, a chave de conteúdo contém chave de criptografia do ativo.
1. Configure diretiva de autorização da chave de conteúdo. A política de autorização de chave conteúdo deve ser configurada por você e atendida pelo cliente para que a chave de conteúdo a ser entregue ao cliente.

Ao criar a política de autorização de chave de conteúdo, você precisará especificar o seguinte: método (PlayReady ou Widevine), restrições de entrega (abertas ou token) e informações específicas para o tipo de entrega chave que define como a chave seja entregue para o cliente (modelo de licença[PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configure a política de entrega de um ativo. A configuração de política de entrega inclui: protocolo de entrega (por exemplo, MPEG traço, HLS, HDS, Streaming suave ou todos), o tipo de criptografia dinâmico (por exemplo, comuns criptografia), PlayReady ou URL de aquisição de licença de Widevine.

Você pode aplicar diretiva diferente para cada protocolo no mesmo ativo. Por exemplo, você pode aplicar criptografia PlayReady suave/traço e AES Envelope para HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única diretiva que especifica apenas HLS como o protocolo) será impedido de streaming. A exceção é se você não tem nenhuma diretiva de entrega de ativos definida em todos os. Em seguida, todos os protocolos poderão ser criptografado.
1. Crie um localizador de OnDemand para obter uma URL de streaming.

Você encontrará um exemplo de .NET completo no final do tópico.

A imagem a seguir demonstra o fluxo de trabalho descrito acima. Veja o token é usado para autenticação.

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

O restante deste tópico oferece explicações detalhadas, exemplos de código e links para tópicos que mostram como alcançar as tarefas descritas acima.

##<a name="current-limitations"></a>Limitações atuais

Se você adicionar ou atualizar uma política de entrega de ativos, você deve excluir o localizador associado (se houver) e criar um novo localizador.

Limitação ao criptografar com Widevine com os serviços de mídia do Azure: atualmente, não há suporte para várias teclas de conteúdo.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Criar um ativo e carregar arquivos para o ativo

Para gerenciar, codificar e vídeos de fluxo, você deve primeiro carregar seu conteúdo em serviços de mídia do Microsoft Azure. Uma vez carregado, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming.

Para obter informações detalhadas, consulte [Carregar arquivos para uma conta de serviços de mídia](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codificar o ativo que contém o arquivo para a taxa de bits adaptativa que MP4 definir

Com a criptografia dinâmica tudo o que você precisa é criar um ativo que contém um conjunto de arquivos de MP4 multi-taxa de bits ou arquivos de origem de Streaming suave multi-taxa de bits. Em seguida, com base em formato especificado na solicitação de manifesto e fragmento, o servidor de Streaming On Demand garantirá que você recebe o fluxo o protocolo que você escolheu. Como resultado, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia serão construir e servir a resposta apropriada com base em solicitações de um cliente. Para obter mais informações, consulte o tópico [Visão geral de embalagem dinâmica](media-services-dynamic-packaging-overview.md) .

Para obter instruções sobre como codificar, consulte [como codificar um ativo usando mídia codificador padrão](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-lo com o ativo codificado

Em serviços de mídia, a chave de conteúdo contém a chave que você deseja criptografar um ativo com.

Para obter informações detalhadas, consulte [criar chave de conteúdo](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurar diretiva de autorização da chave de conteúdo

Serviços de mídia oferece suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave conteúdo deve ser configurada por você e atendida pelo cliente (player) para a tecla ser entregue para o cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição.

Para obter informações detalhadas, consulte [Configurar diretiva de autorização de chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurar política de entrega de ativos 

Configure a política de entrega para seu ativo. Algumas coisas que a configuração de política de entrega de ativos inclui:

- A URL de aquisição de licença DRM. 
- O protocolo de entrega de ativo (por exemplo, MPEG traço, HLS, HDS, Streaming suave ou todos). 
- O tipo de criptografia dinâmico (nesse caso, criptografia comuns). 

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

##<a id="example"></a>Exemplo


O exemplo a seguir demonstra funcionalidades que foi introduzida no SDK de serviços de mídia do Azure para .net-versão 3.5.2 (especificamente, a capacidade de definir um modelo de licença Widevine e solicitar uma licença de Widevine de serviços de mídia do Azure). O seguinte comando pacote Nuget foi usado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Crie um novo projeto de Console.
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
    
    Certifique-se de atualizar variáveis para apontar para pastas onde os arquivos de entrada estão localizados.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
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
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
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
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
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
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
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


## <a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também

[CENC com Multi-DRM e controle de acesso](media-services-cenc-with-multidrm-access-control.md)

[Configurar Widevine embalagem com AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Anunciando Google Widevine serviços de entrega de licença nos serviços de mídia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
