<properties 
    pageTitle="Configurar diretiva de autorização de chave conteúdo usando o portal de Azure | Microsoft Azure" 
    description="Saiba como configurar uma diretiva de autorização para uma chave de conteúdo." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurar diretiva de autorização de chave de conteúdo
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure permitem que você forneça fluxos MPEG-traço, Streaming suave e Streaming Live HTTP (HLS) protegidos com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permite que você forneça fluxos de traço criptografados com Widevine DRM. PlayReady e Widevine são criptografados pela especificação de criptografia comum (ISO/IEC 23001-7 CENC).

Serviços de mídia também fornece um **Serviço de entrega de chave/licença** do qual os clientes podem obter chaves AES ou licenças de PlayReady/Widevine para reproduzir o conteúdo criptografado.

Este tópico mostra como usar o portal do Azure para configurar a política de autorização de chave conteúdo. A chave pode ser usada mais tarde para criptografar dinamicamente seu conteúdo. Observação atualmente pode criptografar o streaming seguintes formatos: HLS, MPEG traço e Streaming suave. Você não pode criptografar HDS streaming format ou progressivo downloads.

Quando um player solicita um fluxo que está definido para ser criptografados dinamicamente, serviços de mídia usa a chave configurada para criptografar dinamicamente seu conteúdo usando criptografia AES ou DRM. Para descriptografar o fluxo, o player solicitará a chave do serviço de entrega de chave. Para decidir se ou não o usuário está autorizado obter a chave, o serviço avalia as diretivas de autorização que você especificou para a chave.


Se você planeja ter várias teclas conteúdas ou deseja especificar uma URL de **Serviço de entrega de chave/licença** diferente do serviço de entrega de chave de serviços de mídia, use o SDK do .NET de serviços de mídia ou APIs REST.

[Configurar diretiva de autorização de chave conteúdo usando o SDK do .NET de serviços de mídia](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar diretiva de autorização de chave conteúdo usando a API de REST de serviços de mídia](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Algumas considerações se aplicar:

- Para poder usar embalagem dinâmica e criptografia dinâmico, você deve certificar ter pelo menos uma unidade reservada de streaming. Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).
- Seu ativo deve conter um conjunto de taxa de bits adaptativa MP4s ou taxa de bits adaptativa Streaming suave arquivos. Para obter mais informações, consulte [codificar um ativo](media-services-encode-asset.md).
- O serviço de entrega de chave armazena ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) por 15 minutos.  Se você cria um ContentKeyAuthorizationPolicy e especifica para usar uma restrição "Token", então testá-lo e, em seguida, atualize a política para restrição "Abrir", levará aproximadamente 15 minutos antes da política alterna para a versão "Aberta" da política.


##<a name="how-to-configure-the-key-authorization-policy"></a>Como: configurar a política de autorização de chave

Para configurar a política de autorização de chave, selecione a página de **Proteção de conteúdo** .

Serviços de mídia oferece suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter **Abrir**, **símbolo**ou restrições de autorização de **IP** (**IP** pode ser configurado com REST ou .NET SDK).

###<a name="open-restriction"></a>Restrição aberta

A restrição **Abrir** significa que o sistema fornecerá a chave para qualquer pessoa que faz uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restrição token

Para escolher a política restrita token, pressione o botão **TOKEN** .

A política de **token** restringido deve ser acompanhada por um token emitido por um **Serviço de Token de segurança** (STS). Serviços de mídia suporta tokens nos **Tokens de Web simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formato **JSON Web Token** (JWT). Para obter informações, consulte [autenticação de token de JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Serviços de mídia não fornece **Serviços de Token de segurança**. Você pode criar um STS personalizado ou aproveitar Microsoft Azure ACS para tokens de problema. O STS deve ser configurado para criar um token assinado com as declarações especificadas de chave e o problema que você especificou na configuração do token de restrição. O serviço de entrega de chave de serviços de mídia retornará a chave de criptografia do cliente se o token for válido e as declarações no token correspondam aqueles configurados para a chave de conteúdo. Para obter mais informações, consulte [Usar Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

Ao configurar o **TOKEN** restrito política, você deve definir valores para a **chave de verificação**, o **emissor** e o **público**. A chave primária de verificação contém a chave que o token foi assinado com, emissor é o serviço de símbolo seguro que emite o token. A audiência (às vezes chamada de escopo) descreve o propósito do token ou o recurso o token autoriza acesso a. O serviço de entrega de chave de serviços de mídia valida que esses valores no token coincidir com os valores no modelo.

###<a name="playready"></a>PlayReady

Ao proteger o conteúdo com **PlayReady**, uma das coisas que você precisa especificar em sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença PlayReady. Por padrão, a seguinte política é definida:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Você pode clicar no botão **importar xml da política** e fornecer um XML diferente que está em conformidade com o esquema XML definido [aqui](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

