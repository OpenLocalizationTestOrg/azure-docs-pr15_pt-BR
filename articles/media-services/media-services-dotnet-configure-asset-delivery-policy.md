<properties 
    pageTitle="Configurar políticas de entrega de ativos com .NET SDK | Microsoft Azure" 
    description="Este tópico mostra como configurar políticas de entrega de ativo diferente com o Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de ativos com o SDK do .NET
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Visão geral

Se você planeja ativos entrega criptografada, uma das etapas do fluxo de trabalho de fornecimento de conteúdo de serviços de mídia está configurando políticas de entrega de ativos. A política de entrega de ativos informa serviços de mídia como deseja para seu ativo ser entregue: em qual streaming protocolo deve seu ativo ser dinamicamente empacotado (por exemplo, MPEG traço, HLS, Streaming suave ou todos), estando ou não desejar criptografar dinamicamente seu ativo e como (envelope ou criptografia comuns).

Este tópico aborda porquê e como criar e configurar diretivas de entrega de ativos.

>[AZURE.NOTE]Para poder usar embalagem dinâmica e criptografia dinâmico, você deve certificar ter pelo menos uma unidade de escala (também conhecido como streaming unidade). Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).
>
>Além disso, seu ativo deve conter um conjunto de taxa de bits adaptativa MP4s ou taxa de bits adaptativa Streaming suave arquivos.

Você pode aplicar políticas diferentes para o mesmo ativo. Por exemplo, você pode aplicar criptografia PlayReady à criptografia Streaming suave e AES Envelope MPEG traço e HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única diretiva que especifica apenas HLS como o protocolo) será impedido de streaming. A exceção é se você não tem nenhuma diretiva de entrega de ativos definida em todos os. Em seguida, todos os protocolos poderão ser criptografado.

Se você quiser fornecer um ativo criptografada do armazenamento, você deve configurar política de entrega do ativo. Antes de seu ativo pode ser transmitido, o servidor de streaming remove a criptografia de armazenamento e fluxos seu conteúdo usando a política de entrega especificado. Por exemplo, para entregar seu ativo criptografado com a chave de criptografia de envelope avançado criptografia AES (padrão), defina o tipo de política para **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e fluxo ativo em Limpar, defina o tipo de política **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo da maneira como você configurar a política de entrega de ativos que você poderá dinamicamente empacotar, dinamicamente criptografar e transmitir seguintes protocolos de streaming: fluxos de Streaming suave, HLS, MPEG traço e HDS.

A lista a seguir mostra os formatos que você usar para transmitir suave, HLS, traço e HDS.

Streaming suave:

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG TRAÇO

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obter instruções sobre como publicar um ativo e criar uma URL streaming, consulte [criar uma URL de streaming](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Considerações

- Você não pode excluir um AssetDeliveryPolicy associado a um ativo durante um localizador de OnDemand (streaming) existe para esse ativo. A recomendação é remover a política de ativo antes de excluir a política.
- Um localizador de streaming não pode ser criado em um ativo de armazenamento criptografados quando nenhuma política de entrega de ativos é definida.  Se o ativo não armazenamento criptografado, o sistema permitirá que você criar um localizador e fluxo ativo em Limpar sem uma política de entrega de ativos.
- Você pode ter várias diretivas de entrega de ativos associadas a um único ativo, mas você só pode especificar uma maneira de lidar com um determinado AssetDeliveryProtocol.  O que significa que se você tentar vincular duas diretivas de entrega que especificam o protocolo AssetDeliveryProtocol.SmoothStreaming que resultará em um erro porque o sistema não souber qual que você deseja que ele se aplicam quando um cliente faz uma solicitação de Streaming suave.
- Se você tiver um ativo com um localizador de streaming existente, você não pode vincular uma nova política ao ativo (você pode desvincular uma política existente do ativo ou atualizar uma diretiva de entrega associada ao ativo).  Primeiro você precisa remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Você pode usar o mesmo locatorId quando você recriar o localizador de streaming, mas você deve certificar-se de que não causar problemas para clientes desde que conteúdo pode ser armazenados em cache, a origem ou uma CDN downstream.


##<a name="clear-asset-delivery-policy"></a>Política de entrega de limpar ativo

O seguinte método **ConfigureClearAssetDeliveryPolicy** especifica para não aplicar criptografia dinâmico e para prestar o fluxo em qualquer um dos seguintes protocolos: protocolos MPEG traço, HLS e Streaming suave. Talvez você queira aplicar esta política a seus ativos de armazenamento criptografado.

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .

ConfigureClearAssetDeliveryPolicy anular público estático (IAsset ativo) {IAssetDeliveryPolicy política = Context. AssetDeliveryPolicies.Create ("Política Limpar", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, nulo);

ativo. DeliveryPolicies.Add(policy); }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos de DynamicCommonEncryption


O seguinte método **CreateAssetDeliveryPolicy** cria o **AssetDeliveryPolicy** que está configurado para aplicar criptografia comuns dinâmico (**DynamicCommonEncryption**) a um protocolo de streaming suave (outros protocolos serão impedidos de streaming). O método leva dois parâmetros: **ativo** (ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption** , para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .


estático CreateAssetDeliveryPolicy anular pública (IAsset ativo, IContentKey chave) {Uri acquisitionUrl = chave. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

AssetDeliveryPolicyConfiguration dicionário < AssetDeliveryPolicyConfigurationKey, string > = novo dicionário < AssetDeliveryPolicyConfigurationKey, string > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Serviços de mídia do Azure também permite que você adicione Widevine criptografia. O exemplo a seguir demonstra PlayReady e Widevine sendo adicionado à política de entrega de ativos.


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Quando a criptografia com Widevine, você só estaria capaz de fornecer usando o traço. Certifique-se de especificar traço no protocolo de entrega de ativos.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos de DynamicEnvelopeEncryption 

O seguinte método **CreateAssetDeliveryPolicy** cria o **AssetDeliveryPolicy** que está configurado para aplicar criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) a protocolos Streaming suave, HLS e traço (se você decidir não especificar alguns protocolos, eles serão bloqueados do streaming). O método leva dois parâmetros: **ativo** (ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption** , para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


