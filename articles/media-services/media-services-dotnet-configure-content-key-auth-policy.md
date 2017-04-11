<properties 
    pageTitle="Configurar a diretiva de autorização de chave conteúdo usando o SDK do .NET de serviços de mídia | Microsoft Azure" 
    description="Saiba como configurar uma diretiva de autorização para uma chave de conteúdo usando o SDK do .NET de serviços de mídia." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Criptografia dinâmico: Configurar diretiva de autorização de chave de conteúdo

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure permitem que você forneça fluxos MPEG-traço, Streaming suave e Streaming Live HTTP (HLS) protegidos com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permite que você forneça fluxos de traço criptografados com Widevine DRM. PlayReady e Widevine são criptografados pela especificação de criptografia comum (ISO/IEC 23001-7 CENC).

Serviços de mídia também fornece um **Serviço de entrega de chave/licença** do qual os clientes podem obter chaves AES ou licenças de PlayReady/Widevine para reproduzir o conteúdo criptografado.

Se você quiser para serviços de mídia criptografar um ativo, você precisa associar uma chave de criptografia (**CommonEncryption** ou **EnvelopeEncryption**) com o ativo (conforme descrito [aqui](media-services-dotnet-create-contentkey.md)) e também configurar diretivas de autorização para a chave (conforme descrito neste artigo).

Quando um fluxo é solicitado por um player, serviços de mídia usa a chave especificada para criptografar dinamicamente seu conteúdo usando criptografia AES ou DRM. Para descriptografar o fluxo, o player solicitará a chave do serviço de entrega de chave. Para decidir se ou não o usuário está autorizado obter a chave, o serviço avalia as diretivas de autorização que você especificou para a chave.

Serviços de mídia oferece suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: **Abrir** ou **token** de restrição. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens nos **Tokens de Web simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formato **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Serviços de mídia não fornece serviços de Token de segurança. Você pode criar um STS personalizado ou aproveitar Microsoft Azure ACS para tokens de problema. O STS deve ser configurado para criar um token assinado com as declarações especificadas de chave e o problema que você especificou na configuração do token de restrição (conforme descrito neste artigo). O serviço de entrega de chave de serviços de mídia retornará a chave de criptografia do cliente se o token for válido e as declarações no token correspondam aqueles configurados para a chave de conteúdo.

Para obter mais informações, consulte

[JWT authenitcation de token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o Azure Media Services OWIN MVC baseado app com o Active Directory do Azure e restringir a entrega de chave conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso do Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Algumas considerações se aplicar:

- Para poder usar embalagem dinâmica e criptografia dinâmico, você deve certificar ter pelo menos uma unidade reservada de streaming. Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).
- Seu ativo deve conter um conjunto de taxa de bits adaptativa MP4s ou taxa de bits adaptativa Streaming suave arquivos. Para obter mais informações, consulte [codificar um ativo](media-services-encode-asset.md).
- Carregar e codificar seus ativos usando a opção de **AssetCreationOptions.StorageEncrypted** .
- Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, é recomendável criar uma política de autorização único e reutilizá-la com várias teclas de conteúdo.
- O serviço de entrega de chave armazena ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) por 15 minutos.  Se você cria um ContentKeyAuthorizationPolicy e especifica para usar uma restrição "Token", então testá-lo e, em seguida, atualize a política para restrição "Abrir", levará aproximadamente 15 minutos antes da política alterna para a versão "Aberta" da política.
- Se você adicionar ou atualizar política de entrega do seu ativo, você deve excluir um localizador existente (se houver) e criar um novo localizador.
- Atualmente, você não pode criptografar HDS streaming format ou progressivo downloads.


##<a name="aes-128-dynamic-encryption"></a>Criptografia de AES-128 dinâmico

###<a name="open-restriction"></a>Restrição aberta

Restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que faz uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e adiciona à chave de conteúdo.

AddOpenAuthorizationPolicy anular público estático (IContentKey contentKey) {/ / criar ContentKeyAuthorizationPolicy com restrições abrir / / e criar diretiva de autorização de política de IContentKeyAuthorizationPolicy = Context.
ContentKeyAuthorizationPolicies.
CreateAsync ("diretiva de autorização aberta"). Resultado;

Lista<ContentKeyAuthorizationPolicyRestriction> restrições = nova lista<ContentKeyAuthorizationPolicyRestriction>();
    
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


###<a name="token-restriction"></a>Restrição token

Esta seção descreve como criar uma política de autorização de chave conteúdo e associá-lo com a chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave (por exemplo, faz a lista de "chave de verificação" conter a chave que o token foi assinado com).

Para configurar a opção de restrição token, você precisa usar um XML para descrever os requisitos de autorização do token. A configuração de restrição token XML deve estar em conformidade com o esquema XML seguinte.

####<a id="schema"></a>Esquema de restrição token
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Ao configurar o **token** restrito política, especifique os parâmetros de** chave de verificação**, o **emissor** e o **público** principais. A **chave primária de verificação **contém a chave que o token foi assinado com, **emissor** é o serviço de símbolo seguro que emite o token. A **audiência** (às vezes chamado de **escopo**) descreve o propósito do token ou o recurso o token autoriza acesso a. O serviço de entrega de chave de serviços de mídia valida que esses valores no token correspondem aos valores no modelo. 

Ao usar o **SDK de serviços de mídia para .NET**, você pode usar a classe **TokenRestrictionTemplate** para gerar o token de restrição.
O exemplo a seguir cria uma diretiva de autorização com uma restrição de token. Neste exemplo, o cliente teria que apresentar um token que contém: a assinatura de chave (VerificationKey), um emissor do token e declarações necessárias.
    
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

####<a id="test"></a>Símbolo de teste

Para obter um token de teste com base na restrição token que foi usada para a política de autorização de chave, faça o seguinte.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>Criptografia de PlayReady dinâmico 

Serviços de mídia permite que você configure os direitos e restrições que você deseja para o runtime PlayReady DRM impor quando um usuário estiver tentando reproduzir conteúdo protegido. 

Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar em sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](media-services-playready-license-template-overview.md). No SDK de serviços de mídia para .NET, as classes **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** ajudará você definir o modelo de licença PlayReady.

[Este tópico](media-services-protect-with-drm.md) mostra como criptografar seu conteúdo com **PlayReady** e **Widevine**.

###<a name="open-restriction"></a>Restrição aberta
    
Restrição aberta significa que o sistema fornecerá a chave para qualquer pessoa que faz uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e adiciona à chave de conteúdo.

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
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Restrição token

Para configurar a opção de restrição token, você precisa usar um XML para descrever os requisitos de autorização do token. A configuração de restrição token XML deve estar em conformidade com o esquema XML mostrado [nesta](#schema) seção.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
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


Para obter um token de teste com base na restrição token que foi usada para a autorização chave política consulte [nesta](#test) seção. 

##<a id="types"></a>Tipos usados ao definir ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Próxima etapa
Agora que você configurou diretiva de autorização da chave de conteúdo, vá para o tópico [como configurar diretiva de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md) .
 
