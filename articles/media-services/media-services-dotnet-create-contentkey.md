<properties 
    pageTitle="Criar ContentKeys com .NET" 
    description="Saiba como criar chaves de conteúdo que fornecem acesso seguro ao ativos." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Criar ContentKeys com .NET

> [AZURE.SELECTOR]
- [RESTANTE](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Serviços de mídia permite criar e fornecer ativos criptografados. Um **ContentKey** fornece acesso seguro para seu s **ativo**. 

Quando você cria um novo ativo (por exemplo, antes de de [carregar arquivos](media-services-dotnet-upload-files.md)), você pode especificar as seguintes opções de criptografia: **StorageEncrypted**, **CommonEncryptionProtected**ou **EnvelopeEncryptionProtected**. 

Quando você entregar ativos para seus clientes, você pode [Configurar para ativos dinamicamente sejam criptografados](media-services-dotnet-configure-asset-delivery-policy.md) com um da criptografia de dois procedimento: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Ativos criptografados precisam ser associadas a **ContentKey**s. Este artigo descreve como criar uma chave de conteúdo.

>[AZURE.NOTE] Ao criar um novo ativo **StorageEncrypted** usando o SDK do .NET de serviços de mídia, o **ContentKey** será criado automaticamente e vinculado com o ativo.

##<a name="contentkeytype"></a>ContentKeyType

Um dos valores que você deve definir quando criar um conteúdo de chave é o tipo de conteúdo de chave. Escolha um dos seguintes valores. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Criar o tipo de envelope ContentKey

O trecho de código a seguir cria uma chave de conteúdo do tipo de criptografia de envelope. Ele então associa a tecla o ativo especificado.

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

        asset.ContentKeys.Add(key);

        return key;
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

chamada

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Criar tipo comum ContentKey    

O trecho de código a seguir cria uma chave de conteúdo do tipo de criptografia comum. Ele então associa a tecla o ativo especificado.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
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
chamada

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
