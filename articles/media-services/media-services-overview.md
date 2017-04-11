<properties 
    pageTitle="Visão geral de serviços de mídia Azure e cenários comuns | Microsoft Azure" 
    description="Este tópico fornece uma visão geral dos serviços de mídia do Azure" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Visão geral de serviços de mídia Azure e cenários comuns

Serviços de mídia do Microsoft Azure é uma plataforma extensível baseado em nuvem que permite que os desenvolvedores criem mídia escaláveis aplicativos de gerenciamento e entrega. Serviços de mídia baseia-se nos APIs REST que permitem que você carregar, armazenar com segurança, codificar e empacotar conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Você pode criar fluxos de trabalho de ponta a ponta inteiramente usando os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifica usando um codificador de terceiros. Em seguida, carregue, proteger, empacotar, fornecer usando os serviços de mídia.

Você pode optar por fluxo seu conteúdo live ou fornecer conteúdo sob demanda. Este tópico mostra cenários comuns para fornecer o seu conteúdo [ao vivo](media-services-overview.md#live_scenarios) ou [sob demanda](media-services-overview.md#vod_scenarios). O tópico também links para outros tópicos relevantes.

## <a name="sdks-and-tools"></a>Ferramentas e SDKs

Para criar soluções de serviços de mídia, você pode usar:

- [Serviços de mídia API REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um dos SDKs do cliente do disponível:
- [Serviços de mídia do Microsoft azure SDK para .NET](https://github.com/Azure/azure-sdk-for-media-services),
- [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java),
- [Azure SDK PHP](https://github.com/Azure/azure-sdk-for-php),
- [Serviços de mídia do Microsoft Azure para Node](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Isso é uma versão não-Microsoft de um SDK Node. É mantido por uma comunidade e atualmente não tem uma cobertura de 100% das APIs AMS).
- Ferramentas existentes:
- [Portal do Azure](https://portal.azure.com/)
- [Azure-Media-Explorador de serviços](https://github.com/Azure/Azure-Media-Services-Explorer) (Explorador de serviços de mídia azure (AMSE) é um Winforms / aplicativo c# para Windows)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

Você pode exibir AMS aqui caminhos de aprendizagem:

- [AMS Live Streaming de fluxo de trabalho](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS sob demanda Streaming de fluxo de trabalho](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Pré-requisitos

Para começar a usar os serviços de mídia do Azure, você deve ter o seguinte:

3. Uma conta do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
2. Uma conta de serviços de mídia do Azure. Use o portal do Azure, .NET ou API REST para criar a conta de serviços de mídia do Azure. Para obter mais informações, consulte [Criar conta](media-services-portal-create-account.md).
3. (Opcional) Configure o ambiente de desenvolvimento. Escolha .NET ou API REST para seu ambiente de desenvolvimento. Para obter mais informações, consulte [Configurar o ambiente](media-services-dotnet-how-to-use.md).

Além disso, saiba como conectar programaticamente [Conectar](media-services-dotnet-connect-programmatically.md).
4. (Recomendado) Aloca uma ou mais unidades de escala. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção.   Para obter mais informações, consulte [Gerenciando streaming pontos de extremidade](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Visão geral e conceitos

Para conceitos de serviços de mídia do Azure, consulte [conceitos](media-services-concepts.md).

Para uma série que apresenta todos os componentes principais de serviços de mídia do Azure, consulte [tutoriais passo a passo de serviços de mídia do Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Nesta série tem uma excelente visão geral de conceitos e usa a ferramenta AMSE para demonstrar AMS tarefas. Observe que a ferramenta AMSE é uma ferramenta do Windows. Essa ferramenta oferece suporte a maioria das tarefas que você pode obter programaticamente com o [SDK do AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), o [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java)ou [SDK do Azure PHP](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Oferecer mídia sob demanda com os serviços de mídia do Azure: cenários e tarefas comuns

Esta seção descreve cenários comuns e fornece links para tópicos relevantes. O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas no fornecimento de conteúdo sob demanda. 

![Fluxo de trabalho VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger o conteúdo em armazenamento e fornecer fluxo de mídia no Limpar (não criptografado)

1. Carregar um arquivo de alta qualidade mezzanine em um ativo.
    
    É recomendável para aplicar a opção de criptografia de armazenamento em seu ativo para proteger o conteúdo durante o carregamento e ao restante em armazenamento.
 
1. Codifica em um conjunto de arquivos de taxa de bits adaptativa MP4. 

    É recomendável para aplicar a opção de criptografia de armazenamento ao ativo saída para proteger o conteúdo restante.
    
1. Configure política de entrega de ativos (usada por embalagem dinâmica). 
    
    Se seu ativo for armazenamento criptografado, você **deve** configurar política de entrega de ativos. 

1. Publica o ativo, criando um localizador de OnDemand.

    Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.

1. Fluxo de conteúdo publicado.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger o conteúdo em armazenamento, deliver dinamicamente criptografada fluxo de mídia  

Para poder usar a criptografia dinâmica, você deve primeiro obter pelo menos uma unidade reservada streaming no ponto de extremidade streaming do qual você deseja transmitir conteúdo criptografado.

1. Carregar um arquivo de alta qualidade mezzanine em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifica em um conjunto de arquivos de taxa de bits adaptativa MP4. Aplique a opção de criptografia de armazenamento para o ativo de saída.
1. Crie chave de criptografia de conteúdo para o ativo que você deseja ser criptografados dinamicamente durante a reprodução.
2. Configure diretiva de autorização de chave conteúdo.
1. Configure política de entrega de ativos (usada por embalagem dinâmica e criptografia dinâmico).
1. Publica o ativo, criando um localizador de OnDemand.
1. Fluxo de conteúdo publicado. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Usar a análise de mídia para gerar ideias acionáveis de vídeos 

Análise de mídia é uma coleção de componentes de fala e visão que tornam mais fácil para as organizações e empresas para originar acionáveis percepções do seus arquivos de vídeo. Para obter mais informações, consulte [Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md).

1. Carregar um arquivo de alta qualidade mezzanine em um ativo.
2. Use um dos seguintes serviços de análise de mídia para processar seus vídeos:
    
    - **Indexador** – [vídeos de processo com 2 de indexador de mídia do Azure](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [arquivos de mídia de Hyperlapse com Hyperlapse de mídia do Microsoft Azure](media-services-hyperlapse-content.md)
    - **Detecção de movimento** – [Detecção de movimento para análise de mídia do Azure](media-services-motion-detection.md).
    - **Detecção de face e emoções Face** – [nominal e detecção de emoções para análise de mídia do Azure](media-services-face-and-emotion-detection.md).
    - **Resumo do vídeo** – [Uso Azure Media Video miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)
3. Processadores de mídia de análise de mídia produzir arquivos MP4 ou JSON. Se um processador de mídia produzidos um arquivo MP4, cada vez que você pode baixar o arquivo. Se um processador de mídia produzidos um arquivo JSON, você pode baixar o arquivo do armazenamento de blob do Microsoft Azure. 


###<a name="deliver-progressive-download"></a>Entregar download progressivo 

1. Carregar um arquivo de alta qualidade mezzanine em um ativo.
1. Codifica em um único arquivo MP4.
1. Publica o ativo, criando um localizador de OnDemand ou SAS.

    Se usando OnDemand localizador, certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você planeja progressivamente baixar conteúdo de streaming.

    Se usando o localizador SAS, o conteúdo é baixado do armazenamento de blob do Microsoft Azure. Nesse caso, você não precisa ter a streaming unidades reservadas.
  
1. Cada vez Baixe conteúdo.

##<a id="live_scenarios"></a>Entregar eventos Streaming ao vivo com os serviços de mídia do Microsoft Azure

Ao trabalhar com Live Streaming os seguintes componentes comumente estão envolvidos:

- Uma câmera que é usada para transmitir um evento.
- Um codificador de vídeo ao vivo que converte sinais da câmera para fluxos que são enviados para um serviço de streaming ao vivo.

Opcionalmente, vários tempo ao vivo sincronizados codificadores. Crítico para determinados eventos ao vivo que disponibilidade muito alta demanda e qualidade da experiência, recomenda-se para adotar ativa codificadores redundantes com hora sincronizaçãoPara atingir failover perfeito sem perda de dados.
- Um serviço de streaming live que permite que você faça o seguinte:

- inclusão de conteúdo ao vivo usando vários protocolos de streaming live (por exemplo RTMP ou Streaming suave)
- (opcionalmente) codificar seu fluxo no fluxo de taxa de bits adaptativa
- visualizar seu fluxo ao vivo,
- gravar e armazenar o conteúdo ingerido para ser transmitido posterior (vídeo sob demanda)
- entregar o conteúdo por meio de protocolos comuns de streaming (por exemplo, MPEG traço, suave, HLS, HDS) diretamente aos seus clientes ou para uma rede de entrega de conteúdo (CDN) para distribuição adicional.


**Serviços de mídia do Microsoft Azure** (AMS) fornece a capacidade de inclusão, codificar, visualizar, armazenar e distribuir o conteúdo de streaming ao vivo.

Ao fornecer o seu conteúdo para clientes seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para cuidam de condições de qualidade e da rede, use codificadores ao vivo para codificar seu fluxo para fluxo de vídeo multi-taxa de bits (taxa de bits adaptativa).  Para cuidam de streaming em dispositivos diferentes, use serviços de mídia [embalagem dinâmica](media-services-dynamic-packaging-overview.md) dinamicamente empacotar novamente seu fluxo para protocolos diferentes. Serviços de mídia dá suporte a entrega da taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia do Azure, **canais**, **programas**e **StreamingEndpoints** tratar todas as funcionalidades de streaming live incluindo recebimento, formatação, DVR, segurança, escalabilidade e redundância.

Um **canal** representa um pipeline de processamento de conteúdo de streaming ao vivo. Um canal pode receber um live fluxos de entrada das seguintes maneiras:

- Um local ao vivo codificador envia multi-taxa de bits **RTMP** ou **Streaming suave** (fragmentados MP4) para o canal que está configurado para entrega de **passagem** . A entrega de **passagem** é quando os fluxos ingeridos passam por **canal**s sem qualquer processamento posterior. Você pode usar os seguintes codificadores ao vivo que multi-taxa de bits suave Streaming de saída: primários, Envivio, Cisco.  Os seguintes codificadores live saída RTMP: transcodificadores Adobe Flash Live Telestream Wirecast e Tricaster.  Um codificador ao vivo também pode enviar um fluxo de taxa de bits única em um canal que não está habilitado para codificação ao vivo, mas que não é recomendado. Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.

>[AZURE.NOTE] Usando um método passagem é a maneira mais econômica Live streaming quando você estiver fazendo vários eventos por um longo período de tempo, e você já tiver investido no codificadores local. Consulte detalhes de [preços](/pricing/details/media-services/) .

- Um codificador live local envia um fluxo de taxa de bits única no canal que estiver ativado para executar a codificação ao vivo com serviços de mídia em um dos seguintes formatos: RTP (MPEG-TS), RTMP ou suave Streaming (MP4 fragmentado). O canal, em seguida, executa a codificação ao vivo de entrada fluxo de taxa de bits única para um fluxo de vídeo multi-taxa de bits (adaptativa). Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhando com canais que recebem fluxo ao vivo multi-taxa de bits de codificadores local (passagem)

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho **passagem** .

![Fluxo de trabalho ao vivo][live-overview2]

Para obter mais informações, consulte [Trabalhando com canais que vários recebimento-fluxo ao vivo de taxa de bits de codificadores locais](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhando com canais que estão habilitados para executar a codificação ao vivo com serviços de mídia do Azure

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho Live Streaming onde um canal estiver ativado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo][live-overview1]

Para obter mais informações, consulte [Trabalhando com canais que estão habilitados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Consumo de conteúdo

Serviços de mídia do Azure fornece as ferramentas necessárias para criar aplicativos do player de cliente sofisticados e dinâmicos para maioria das plataformas inclusive: iOS dispositivos, dispositivos Android, Windows, Windows Phone, Xbox e decodificador caixas. O tópico a seguir fornece links para SDK e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos cliente que podem consumir o fluxo de mídia de serviços de mídia.

[Desenvolver aplicativos do Player de vídeo](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>Ativando o Azure CDN

Serviços de mídia oferece suporte a integração com o Azure CDN. Para obter informações sobre como habilitar o Azure CDN, consulte [como gerenciar Streaming pontos de extremidade de uma conta de serviços de mídia](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar **Serviços de mídia** , especificando o número de **Unidades de reservadas Streaming** e **Codificação reservadas unidades** que você gostaria de sua conta para ser provisionado com.

Você também pode dimensionar sua conta de serviços de mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento está limitada aos 500 TB. Para expandir seu armazenamento além das limitações do padrão, você pode optar por anexar várias contas de armazenamento para uma única conta de serviços de mídia.

[Este](media-services-portal-scale-streaming-endpoints.md) tópico fornece links para tópicos relevantes.

##<a name="support"></a>Suporte

[O suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para Azure, incluindo serviços de mídia.

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

- Codificação de serviços de mídia, garantimos 99,9% de disponibilidade de transações de API REST.
- Para Streaming, podemos com êxito atenderá solicitações com uma garantia de disponibilidade de 99,9% para o conteúdo de mídia existente quando pelo menos uma unidade de Streaming é adquirida.
- Para Live canais, garantimos que executar canais terá conectividade externa pelo menos 99,9% das vezes.
- Para proteção de conteúdo, garantimos que estamos com êxito atenderá solicitações de chave pelo menos 99,9% das vezes.
- Para indexador, podemos com êxito atenderá solicitações de tarefa indexador processadas com um reservado codificação unidade 99,9% das vezes.

Para obter mais informações, consulte [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
