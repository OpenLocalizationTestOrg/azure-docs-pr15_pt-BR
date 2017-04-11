<properties 
    pageTitle="Configurando diretivas de entrega de ativos usando API de REST de serviços de mídia | Microsoft Azure" 
    description="Este tópico mostra como configurar políticas de entrega de ativos diferentes usando a API de REST de serviços de mídia." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configurando diretivas de entrega de ativos

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Se você planeja fornecer ativos dinamicamente criptografados, uma das etapas do fluxo de trabalho de fornecimento de conteúdo de serviços de mídia está configurando políticas de entrega de ativos. A política de entrega de ativos informa serviços de mídia como deseja para seu ativo ser entregue: em qual streaming protocolo deve seu ativo ser dinamicamente empacotado (por exemplo, MPEG traço, HLS, Streaming suave ou todos), estando ou não desejar criptografar dinamicamente seu ativo e como (envelope ou criptografia comuns).

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
- Se você tiver um ativo com um localizador de streaming existente, você não pode vincular uma nova política ao ativo, desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo.  Primeiro você precisa remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Você pode usar o mesmo locatorId quando você recriar o localizador de streaming, mas você deve certificar-se de que não causar problemas para clientes desde que conteúdo pode ser armazenados em cache, a origem ou uma CDN downstream.

>[AZURE.NOTE] Ao trabalhar com a API de REST de serviços de mídia, as considerações a seguir se aplicam:
>
>Quando estiver acessando entidades nos serviços de mídia, você deve definir campos de cabeçalho específico e valores em suas solicitações de HTTP. Para obter mais informações, consulte [instalação para desenvolvimento de API do resto de serviços de mídia](media-services-rest-how-to-use.md).

>Depois de conectar-se a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectando-se aos serviços de mídia usando API REST](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Política de entrega de limpar ativo

###<a id="create_asset_delivery_policy"></a>Criar uma política de entrega de ativos
A solicitação HTTP a seguir cria uma política de entrega de ativos que especifica para não aplicar criptografia dinâmico e para prestar o fluxo em qualquer um dos seguintes protocolos: protocolos MPEG traço, HLS e Streaming suave. 

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .   


Solicitação:
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Resposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Ativo de link com política de entrega de ativos

A seguinte solicitação HTTP vincula o ativo especificado a política de entrega de ativos para.

Solicitação:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Resposta:

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos de DynamicEnvelopeEncryption 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Criar chave de conteúdo do tipo EnvelopeEncryption e vinculá-lo ao ativo

Ao especificar a política de entrega de DynamicEnvelopeEncryption, você precisa certificar-se de vincular seu ativo uma chave de conteúdo do tipo EnvelopeEncryption. Para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Obter a URL de entrega

Obter a URL de entrega para o método de entrega especificado da chave de conteúdo criado na etapa anterior. Um cliente usa a URL retornada para solicitar uma chave AES ou um PlayReady licença na ordem de reprodução de conteúdo protegido.

Especifique o tipo da URL para obter no corpo da solicitação HTTP. Se você estiver protegendo seu conteúdo com PlayReady, solicitar uma URL de aquisição de licença PlayReady de serviços de mídia, usando 1 para a keyDeliveryType: {"keyDeliveryType": 1}. Se você estiver protegendo seu conteúdo com a criptografia de envelope, solicitar uma URL de aquisição de chave especificando 2 para keyDeliveryType: {"keyDeliveryType": 2}.

Solicitação:
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Resposta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Criar uma política de entrega de ativos

A solicitação HTTP a seguir cria o **AssetDeliveryPolicy** que está configurado para aplicar criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para o protocolo **HLS** (neste exemplo, outros protocolos serão impedidos de streaming). 


Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .   

Solicitação:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Resposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Ativo de link com política de entrega de ativos

Consulte [ativo de Link com política de entrega de ativos](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos de DynamicCommonEncryption 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Criar chave de conteúdo do tipo CommonEncryption e vinculá-lo ao ativo

Ao especificar a política de entrega de DynamicCommonEncryption, você precisa certificar-se de vincular seu ativo uma chave de conteúdo do tipo CommonEncryption. Para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Obter a URL de entrega

Obter a URL de entrega o método de entrega PlayReady da chave de conteúdo criado na etapa anterior. Um cliente usa a URL retornada para solicitar uma licença PlayReady na ordem de reprodução de conteúdo protegido. Para obter mais informações, consulte [Obter URL de entrega](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Criar uma política de entrega de ativos

A solicitação HTTP a seguir cria o **AssetDeliveryPolicy** que está configurado para aplicar criptografia comuns dinâmico (**DynamicCommonEncryption**) para o protocolo de **Streaming suave** (neste exemplo, outros protocolos serão impedidos de streaming). 

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [tipos usados ao definir AssetDeliveryPolicy](#types) .   


Solicitação:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Se você deseja proteger seu conteúdo usando Widevine DRM, atualize os valores de AssetDeliveryConfiguration para usar WidevineLicenseAcquisitionUrl (que tem o valor 7) e especifique a URL de um serviço de entrega de licença. Você pode usar os seguintes parceiros AMS para ajudá-lo a oferecer Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Por exemplo: 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Quando a criptografia com Widevine, você só estaria capaz de fornecer usando o traço. Certifique-se de especificar traço (2) no protocolo de entrega de ativos.
  
###<a name="link-asset-with-asset-delivery-policy"></a>Ativo de link com política de entrega de ativos

Consulte [ativo de Link com política de entrega de ativos](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
