<properties 
    pageTitle="Protegendo visão geral do conteúdo | Microsoft Azure" 
    description="Este artigo dar uma visão geral da proteção de conteúdo com os serviços de mídia." 
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

#<a name="protecting-content-overview"></a>Protegendo visão geral do conteúdo


Serviços de mídia do Microsoft Azure permite que você proteja sua mídia do horário deixa seu computador por meio de armazenamento, processamento e entrega. Serviços de mídia permite que você envie seu conteúdo ao vivo e sob demanda dinamicamente criptografado com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits) ou qualquer as DRMs principais: Microsoft PlayReady, Google Widevine e FairPlay da Apple. Serviços de mídia também oferece um serviço para oferecer chaves AES e DRM (PlayReady, Widevine e FairPlay) licenças aos clientes autorizados. 

A imagem a seguir demonstra os fluxos de trabalho de proteção de conteúdo que suporta AMS. 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Para poder usar a criptografia dinâmica, você deve primeiro obter pelo menos uma unidade reservada streaming no ponto de extremidade streaming do qual você deseja transmitir conteúdo criptografado.

Este tópico explica [conceitos e a terminologia](media-services-content-protection-overview.md) relevantes para Noções básicas sobre a proteção de conteúdo com AMS. O tópico também contém [links](media-services-content-protection-overview.md#common-scenarios) para tópicos que mostram como atingir tarefas de proteção de conteúdo. 

##<a name="dynamic-encryption"></a>Criptografia dinâmico

Serviços de mídia do Microsoft Azure permitem que você forneça seu conteúdo dinamicamente criptografado com criptografia de DRM ou AES limpar chave: Microsoft PlayReady, Google Widevine e FairPlay da Apple.

Atualmente, você pode criptografar os seguintes formatos de streaming: HLS, MPEG traço e Streaming suave. Você não pode criptografar HDS streaming format ou progressivo downloads.

Se você quiser para serviços de mídia criptografar um ativo, você precisa associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) a seus ativos e também configurar diretivas de autorização para a chave.

Você também precisa configurar política de entrega do ativo. Se você deseja transmitir um ativo criptografada do armazenamento, verifique se especificar como você deseja entregá-lo configurando política de entrega de ativos.

Quando um fluxo é solicitado por um player, serviços de mídia usa a chave especificada para criptografar dinamicamente seu conteúdo usando AES limpar chave ou criptografia de DRM. Para descriptografar o fluxo, o player solicitará a chave do serviço de entrega de chave. Para decidir se ou não o usuário está autorizado obter a chave, o serviço avalia as diretivas de autorização que você especificou para a chave.

>[AZURE.NOTE]Para tirar proveito de criptografia dinâmico, você deve primeiro obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo criptografado. Para obter mais informações, consulte [como serviços de mídia de escala](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Criptografia de armazenamento

Use a criptografia de armazenamento para criptografar o conteúdo limpar localmente usando criptografia AES 256 e carregue-o para o armazenamento do Azure onde ele está armazenado criptografado no restante. Ativos protegidos com criptografia de armazenamento são automaticamente não criptografados e colocados em um sistema de arquivos criptografados antes de codificação e, opcionalmente, criptografados novamente antes de carregar volta como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte inativos no disco.

Para entregar um ativo criptografada do armazenamento, você deve configurar política de entrega do ativo para que serviços de mídia Saiba como deseja distribuir o conteúdo. Antes de seu ativo pode ser transmitido, o servidor de streaming remove a criptografia de armazenamento e fluxos seu conteúdo usando a política de entrega especificado (por exemplo, AES, criptografia comum ou sem criptografia).

## <a name="common-encryption-cenc"></a>Criptografia comuns (CENC)

Criptografia comuns é usada para criptografar o conteúdo com PlayReady ou / e Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Usando criptografia de cbcs-aapl

Cbcs aapl é usado ao criptografar seu conteúdo com FairPlay.

## <a name="envelope-encryption"></a>Criptografia de envelope 

Use esta opção se você deseja proteger seu conteúdo com chave Limpar AES-128. Se você quiser uma opção mais segura, escolha uma das DRMs listadas neste tópico. 

##<a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves

Serviços de mídia fornece um serviço para entregar licenças DRM (PlayReady, Widevine, FairPlay) e AES desmarque teclas para clientes autorizados. Você pode usar [o portal do Azure](media-services-portal-protect-content.md), API REST ou SDK de serviços de mídia para .NET para configurar diretivas de autenticação e a autorização para suas licenças e chaves.

##<a name="token-restriction"></a>Restrição token

A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens em simples Web Tokens SWT () e o formato de JSON Web Token (JWT). Serviços de mídia não fornece serviços de Token de segurança. Você pode criar um STS personalizado ou aproveitar Microsoft Azure ACS para tokens de problema. O STS deve ser configurado para criar um token assinado com as declarações especificadas de chave e o problema que você especificou na configuração do token de restrição. O serviço de entrega de chave de serviços de mídia retornará a chave solicitada (ou licença) para o cliente se o token for válido e as declarações na correspondência de token aqueles configurados para a chave (ou a licença).

Ao configurar o token restrito política, especifique a chave primária de verificação, emissor e parâmetros de audiência. A chave primária de verificação contém a chave que o token foi assinado com, emissor é o serviço de símbolo seguro que emite o token. A audiência (às vezes chamada de escopo) descreve o propósito do token ou o recurso o token autoriza acesso a. O serviço de entrega de chave de serviços de mídia valida que esses valores no token coincidir com os valores no modelo.

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

##<a name="common-scenarios"></a>Cenários comuns

Os tópicos a seguir demonstram como proteger o conteúdo em armazenamento, entregar dinamicamente criptografada fluxo de mídia, use o serviço de entrega de chave/licença AMS

- [Proteger com AES](media-services-protect-with-aes128.md) 
- [Proteger com PlayReady e/ou Widevine](media-services-protect-with-drm.md)
- [Transmitir sua protegido de conteúdo de HLS com o Apple FairPlay e/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Cenários adicionais

- [Como integrar o serviço de licença do Azure PlayReady com seu próprio servidor Criptografador/streaming](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Usando castLabs para fornecer licenças DRM aos serviços de mídia do Azure](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Links relacionados

[Anunciando PlayReady como um serviço e criptografia AES de dinâmico com os serviços de mídia do Azure](http://mingfeiy.com/playready)

[Preços de entrega de licença de PlayReady de serviços de mídia Azure explicadas](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para AES criptografados fluxo em serviços de mídia do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT authenitcation de token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o Azure Media Services OWIN MVC baseado app com o Active Directory do Azure e restringir a entrega de chave conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso do Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
