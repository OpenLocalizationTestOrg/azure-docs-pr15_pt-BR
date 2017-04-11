<properties 
    pageTitle="Configurando diretivas de proteção de conteúdo usando o portal de Azure | Microsoft Azure" 
    description="Este artigo mostra como usar o portal do Azure para configurar as diretivas de proteção de conteúdo. O artigo também mostra como habilitar a criptografia dinâmica para seus ativos." 
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

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configurando diretivas de proteção de conteúdo usando o portal do Azure

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Visão geral

Microsoft Azure Media Services (AMS) permite que você proteja sua mídia do horário deixa seu computador por meio de armazenamento, processamento e entrega. Serviços de mídia permite que você envie seu conteúdo criptografado dinamicamente com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits), criptografia comuns (CENC) usando PlayReady e/ou Widevine DRM e FairPlay da Apple. 

AMS fornece um serviço para entregar licenças DRM e AES desmarque teclas para clientes autorizados. O portal do Azure permite que você crie uma **diretiva de autorização de chave/licença** para todos os tipos de criptografia.

Este artigo mostra como configurar políticas de proteção de conteúdo com o portal do Azure. O artigo também mostra como aplicar criptografia dinâmico a seus ativos.

> [AZURE.NOTE]  Se você usou o Azure portal clássico para criar políticas de proteção, as políticas podem não aparecer no [portal do Azure](https://portal.azure.com/). Entretanto, todas as políticas do antigo ainda existem. Você pode examiná-los usando o SDK do Azure Media serviços .NET ou a ferramenta de [Azure-Media-serviços-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (para ver as políticas, o botão direito do mouse no ativo -> Exibir informações (F4) -> clique na guia conteúdo chaves -> clique na chave). 
> 
> Se desejar criptografar seu ativo usando novas políticas, configurá-las com o portal do Azure, clique em Salvar e reaplicar criptografia dinâmico. 

## <a name="start-configuring-content-protection"></a>Iniciar a configuração de proteção de conteúdo

Para usar o portal para iniciar a configuração de proteção de conteúdo, global à sua conta de AMS, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2. Selecione **configurações** > **proteção de conteúdo**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Diretiva de autorização de chave/licença

AMS suporta várias maneiras de autenticar os usuários que fazem solicitações de chave ou licença. A política de autorização de chave conteúdo deve ser configurada por você e atendida por seu cliente na ordem da chave/licença para ser delived ao cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: **Abrir** ou **token** de restrição.

O portal do Azure permite que você crie uma **diretiva de autorização de chave/licença** para todos os tipos de criptografia.

###<a name="open"></a>Abrir 

Restrições aberta significa que o sistema utilizará para exibir a chave para qualquer pessoa que faz uma solicitação de chave. Essa restrição pode ser útil para fins de teste. 

### <a name="token"></a>Token

A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens em simples Web Tokens SWT () e o formato de JSON Web Token (JWT). Serviços de mídia não fornece serviços de Token de segurança. Você pode criar um STS personalizado ou aproveitar Microsoft Azure ACS para tokens de problema. O STS deve ser configurado para criar um token assinado com as declarações especificadas de chave e o problema que você especificou na configuração do token de restrição. O serviço de entrega de chave de serviços de mídia retornará a chave solicitada (ou licença) para o cliente se o token for válido e as declarações na correspondência de token aqueles configurados para a chave (ou a licença).

Ao configurar o token restrito política, especifique a chave primária de verificação, emissor e parâmetros de audiência. A chave primária de verificação contém a chave que o token foi assinado com, emissor é o serviço de símbolo seguro que emite o token. A audiência (às vezes chamada de escopo) descreve o propósito do token ou o recurso o token autoriza acesso a. O serviço de entrega de chave de serviços de mídia valida que esses valores no token coincidir com os valores no modelo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modelo de direitos de PlayReady

Para obter informações detalhadas sobre o modelo de direitos PlayReady, consulte [Visão geral do modelo de licença de PlayReady de serviços de mídia](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Não persistente

Se você configurar licença como não-persistente, ele só é mantido na memória enquanto o player está usando a licença.  

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente

Se você configurar a licença como persistente, ele é salvo no armazenamento persistente no cliente.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modelo de direitos de Widevine

Para obter informações detalhadas sobre o modelo de direitos de Widevine, consulte [Visão geral do modelo de licença Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Básico

Quando você seleciona **básica**, o modelo será criado com todos os valores de padrões.

### <a name="advanced"></a>Avançadas

Para obter explicações detalhadas sobre opção advance das configurações de Widevine, consulte [Este](media-services-widevine-license-template-overview.md) tópico.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração de FairPlay

Para ativar a criptografia de FairPlay, você precisa fornecer a chave de segredo de aplicativo (SOLICITAR) e o certificado de aplicativo por meio da opção de configuração de FairPlay. Para obter informações detalhadas sobre a configuração de FairPlay e requisitos, consulte [Este](media-services-protect-hls-with-fairplay.md) artigo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplicar criptografia dinâmico ao seu ativo

Para tirar proveito de criptografia dinâmica, você precisa fazer o seguinte:

- Codifica seu arquivo de origem em um conjunto de arquivos de MP4 adaptativa taxa de bits.
- Obtenha pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja distribuir seu conteúdo. Para obter mais informações, consulte [como dimensionar streaming unidades reservadas sob demanda](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecionar um ativo que você deseja criptografar

Para ver todos os seus ativos, selecione **configurações** > **ativos**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografar com AES ou DRM

Depois que você pressiona **criptografar** um ativo, você verá duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES

AES limpar criptografia de chave será habilitada em todos os protocolos streaming: Streaming suave, HLS e MPEG-traço.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Quando você seleciona a guia DRM, são apresentadas diferentes opções de diretivas de proteção de conteúdo (o que você deve ter configurado agora) + um conjunto de protocolos de streaming.

- **PlayReady e Widevine com traço MPEG** - dinamicamente irá criptografar seu fluxo de MPEG-traço com PlayReady e Widevine DRMs.
- **PlayReady e Widevine com MPEG-traço + FairPlay com HLS** - irá dinamicamente criptografar você fluxo de MPEG-traço com PlayReady e Widevine DRMs. Também será criptografar seus fluxos HLS com FairPlay.
- **PlayReady somente com Streaming suave, HLS e MPEG-traço** - irá dinamicamente criptografar Streaming suave, HLS, fluxos MPEG-traço com PlayReady DRM.
- **Widevine somente com traço MPEG** - dinamicamente irá criptografar você MPEG-traço com Widevine DRM.
- **FairPlay somente com HLS** - dinamicamente irá criptografar seu fluxo HLS com FairPlay.

Para ativar a criptografia de FairPlay, você precisa fornecer a chave de segredo de aplicativo (SOLICITAR) e o certificado de aplicativo através da opção FairPlay configuração a lâmina de configurações de proteção de conteúdo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Após fazer a seleção de criptografia, pressione **Aplicar**.

##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





