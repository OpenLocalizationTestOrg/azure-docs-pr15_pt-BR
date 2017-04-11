<properties 
    pageTitle="Filtros e manifestos dinâmicos | Microsoft Azure" 
    description="Este tópico descreve como criar filtros para que seu cliente possa usá-los para seções específicas do fluxo de um fluxo. Serviços de mídia cria manifestos dinâmicos para arquivar esta streaming seletiva." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

Começando com versão 2.11, serviços de mídia permite definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirá que seus clientes optar por fazer coisas como: reprodução apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro), ou especificar apenas um subconjunto de representações de áudio e vídeo que dispositivo do cliente pode manipular (em vez de todas as representações que estão associadas a ativo). A filtragem de seus ativos é arquivada por meio de s **Manifesto dinâmicos**que são criados por solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Tópicos aborda cenários comuns na qual usando filtros será muito útil aos seus clientes e links para tópicos que demonstram como criar filtros programaticamente (no momento, você pode criar filtros com APIs REST somente).

##<a name="overview"></a>Visão geral

Ao fornecer o seu conteúdo para clientes (streaming eventos ao vivo ou vídeo sob demanda) seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para obter essa faça meta o seguinte:

- codificar seu fluxo para o fluxo de vídeo multi-taxa de bits ([taxa de bits adaptativa](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (Isso será cuidam de condições de rede e qualidade) e 
- Use serviços de mídia [Embalagem dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente empacotar novamente seu fluxo em protocolos diferentes (Isso será cuidam de streaming em dispositivos diferentes). Serviços de mídia dá suporte a entrega da taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso). 

###<a name="manifest-files"></a>Arquivos de manifesto 

Quando você codificar um ativo para taxa de bits adaptativa streaming, um arquivo de **manifesto** (reprodução) seja criado (o arquivo é baseado em texto ou baseado em XML). O arquivo de **manifesto** inclui streaming metadados como: controlar o tipo (áudio, vídeo ou texto), controlar o nome, a hora de início e término, taxa de bits (características) e idiomas de controle, a janela de apresentação (janela deslizante de duração fixa), codec de vídeo (FourCC). Também instrui o player para recuperar o próximo fragmento fornecendo informações sobre os próximos executável vídeo fragmentos disponíveis e sua localização. Fragmentos (ou segmentos) são os "blocos" reais de um conteúdo de vídeo.


Aqui está um exemplo de um arquivo de manifesto: 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifestos dinâmicos

Há [situações](media-services-dynamic-manifest-overview.md#scenarios) quando seu cliente precisa de mais flexibilidade que o que está descrito no arquivo de manifesto do ativo padrão. Por exemplo:

- Dispositivo específico: entregar apenas a e/ou representações especificado especificada faixas de idioma com suporte pelo dispositivo que é usado para reprodução de conteúdo ("representação filtragem"). 
- Reduza o manifesto para mostrar um subclipe de um evento ao vivo ("subclipe filtragem").
- Corte o início de um vídeo ("filtrando um vídeo").
- Ajuste a janela de apresentação (DVR) para fornecer um comprimento limitado da janela DVR no player ("ajuste apresentação janela").
 
Para atingir esta flexibilidade, serviços de mídia oferece **Manifestos dinâmico** com base em [filtros](media-services-dynamic-manifest-overview.md#filters)de predefinidos.  Depois de definir os filtros, seus clientes poderiam usá-los para transmitir uma representação específica ou sub clipes do seu vídeo. Eles seriam especificar filtros na URL streaming. Filtros podem ser aplicados a adaptativa taxa de bits streaming protocolos suportados pelo [Embalagem dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG-traço, Streaming suave e HDS. Por exemplo:

URL de traço MPEG com filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de Streaming suave com filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como distribuir o conteúdo e construir streaming URLs, consulte [Visão geral do conteúdo entregando](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Observe que manifestos dinâmico não altere o ativo e o manifesto padrão para esse ativo. O cliente pode optar por solicitar um fluxo com ou sem filtros. 


###<a id="filters"></a>Filtros 

Há dois tipos de filtros de ativo: 

- Filtros globais (pode ser aplicado a qualquer ativo na conta de serviços de mídia do Azure, têm uma vida útil da conta) e 
- Filtros locais (só podem ser aplicadas a um ativo com o qual o filtro foi associado durante a criação, têm uma vida útil do ativo). 

Tipos de filtro globais e locais têm exatamente as mesmas propriedades. A principal diferença entre os dois é para quais cenários que tipo de um servidor de dados é mais adequado. Filtros globais são geralmente adequados para os perfis de dispositivo (representação filtragem) onde os filtros locais podem ser usados para cortar um ativo específico.


##<a id="scenarios"></a>Cenários comuns 

Como foi mencionado anteriormente, quando entregar seu conteúdo aos clientes (streaming eventos ao vivo ou vídeo sob demanda) seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Além disso, sua pode ter outros requisitos que envolvem filtragem seus ativos e uso de **Manifesto dinâmico**s. As seções a seguir oferecem uma breve visão geral de cenários diferentes de filtragem.

- Especifique apenas um subconjunto de representações de áudio e vídeo que determinados dispositivos podem manipular (em vez de todas as representações que estão associadas a ativo). 
- Reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro).
- Ajuste a janela de apresentação de DVR.

##<a name="rendition-filtering"></a>Filtragem de representação 

Você pode optar por codificar seu ativo para vários perfis de codificação (linha de base h. 264, h. 264 alta, AACL, AACH, Dolby Digital Plus) e várias bitrates de qualidade. No entanto, nem todos os dispositivos de cliente oferecerá suporte perfis e bitrates de todos os seus ativos. Por exemplo, dispositivos Android antigos só oferece suporte a linha de base + AACL h. 264. Enviando bitrates superior para um dispositivo que não é possível obter os benefícios, desperdiça computação de largura de banda e do dispositivo. Tal dispositivo deve decodificar todas as informações de determinado, somente para dimensioná-la para baixo para exibição.

Com o manifesto dinâmico, você pode criar perfis de dispositivo como celular, console, HD/SD, etc. e incluir as faixas e características que você deseja fazer parte de cada perfil.

 
![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, um codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo de ISO MP4s (de 180p para 1080p). O ativo codificado pode ser dinamicamente empacotado em qualquer uma das seguintes protocolos de streaming: HLS, suave, MPEG traço e HDS.  Na parte superior do diagrama, o manifesto HLS do ativo sem filtros é mostrado (ele contém todas as sete representações).  Na parte inferior esquerda, o manifesto HLS ao qual foi aplicado um filtro denominado "ott" é exibido. Especifica o filtro de "ott" para remover todas as bitrates abaixo 1Mbps, que resultou nos níveis de qualidade de dois inferior sendo removidos na resposta.  No canto inferior direito, o manifesto HLS ao qual foi aplicado um filtro denominado "móveis" é mostrado. Especifica o filtro de "móvel" para remover representações onde a resolução for maior do que 720p, resultando em duas representações 1080p sendo removidas.

![Filtragem de representação][renditions1]

##<a name="removing-language-tracks"></a>Remover faixas de idioma

Os ativos podem conter vários idiomas áudio como inglês, espanhol, francês etc. Geralmente, os gerentes de Player SDK padrão seleção de faixa de áudio e áudio disponível rastreia por seleção do usuário. É desafiador desenvolver tais SDKs Player, requer implementações diferentes nas player-estruturas específicas do dispositivo. Além disso, em algumas plataformas, Player APIs são limitados e não inclui o recurso de seleção de áudio, onde os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, você pode controlar o comportamento Criando filtros que incluem apenas os idiomas desejados de áudio.

![Filtragem de faixas de idioma][language_filter]


##<a name="trimming-start-of-an-asset"></a>Filtragem de início de um ativo 

Na maioria dos eventos de streaming ao vivo, operadores executar alguns testes antes do evento real. Por exemplo, eles podem incluir um zero assim antes do início do evento: "Programa começará instantes". Se o programa está arquivando, teste e dados CleanSlate também são arquivados e serão incluídos na apresentação. No entanto, essa informação não deve ser mostrada para os clientes. Com o manifesto dinâmico, você pode criar um filtro de tempo de início e remover os dados indesejados do manifesto.

![Início de filtragem][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Criando clipes sub (exibições) de um arquivo ao vivo

Muitos eventos ao vivo forem longos executando e arquivamento dinâmico pode conter vários eventos. Após o evento ao vivo extremidades transmissores talvez queira dividir o arquivamento dinâmico para o início de programas lógicos e parar sequências. Em seguida, publica esses programas virtuais separadamente sem post processamento o arquivamento ao vivo e não criando ativos separados (que não obterá benefícios dos fragmentos em cache existentes nas CDNs). Exemplos de tais programas virtuais (sub clipes) são os trimestres de um campo de futebol ou jogo de basquete, o innings no beisebol ou eventos individuais de uma tarde do programa de Jogos Olímpicos.

Com o manifesto dinâmico, você pode criar filtros usando horas de início/término e criar modos de exibição virtuais sobre o topo da sua arquivamento dinâmico. 

![Filtro de subclip][subclip_filter]

Ativos filtrado:

![Esqui][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Ajustar a janela de apresentação (DVR)

Atualmente, os serviços de mídia do Azure oferece arquivamento circular onde a duração pode ser configurada entre 5 minutos - 25 horas. Manifesto filtragem pode ser usada para criar uma janela DVR sucessivas sobre o topo do arquivo, sem excluir mídia. Existem muitos cenários onde as difusoras deseja fornecer uma janela DVR limitada que move com a borda ao vivo e ao mesmo tempo manter uma janela de arquivamento maior. Um apresentador talvez queira usar os dados que estão fora da janela DVR para realçar clipes ou he\she talvez queira fornecer janelas DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não manipular janelas DVR grandes (você pode ter uma janela DVR de 2 minutos para dispositivos móveis e 1 hora para clientes de desktop).

![Janela DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Ajustando LiveBackoff (posição ao vivo)

Manifesto filtragem pode ser usada para remover vários segundos da borda de um programa de visualização ao vivo. Isso permite que as difusoras assistir à apresentação no ponto de publicação de visualização e criar anúncio pontos de inserção antes dos visualizadores recebem o fluxo (normalmente feito-desativar por 30 segundos). Difusores podem enviar esses anúncios para suas estruturas de cliente em tempo para que elas sejam recebidos e processar as informações antes da oportunidade de anúncio.

Além do suporte de anúncio, LiveBackoff pode ser usado para ajustar a posição de download ao vivo do cliente para que quando clientes incorretos e a borda ao vivo de visitas que eles ainda possam acessar fragmentos do servidor em vez de encontrando erros HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras em um único filtro

Você pode combinar várias regras de filtragem em um único filtro. Como exemplo, você pode definir uma regra de intervalo para remover zero de um arquivo morto ao vivo e também filtrar bitrates disponíveis. Para várias regras de filtragem o resultado final é a composição (somente interseção) dessas regras.

![regras de múltiplo][multiple-rules]

##<a name="create-filters-programmatically"></a>Criar filtros programaticamente

O seguinte tópico discute entidades de serviços de mídia que estão relacionadas aos filtros. O tópico também mostra como criar filtros programaticamente.  

[Criar filtros com APIs REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinar vários filtros (filtro composição)

Você também pode combinar vários filtros em uma única URL. 

O cenário a seguir demonstra por que talvez você queira combinar filtros:

1. Você precisa filtrar suas características de vídeo para dispositivos móveis como Android ou iPAD (para limitar as características de vídeo). Para remover as características indesejadas, você criaria um filtro global que é adequado para os perfis de dispositivo. Conforme mencionado acima, filtros globais podem ser usados para todos os seus ativos com a mesma conta de serviços de mídia sem qualquer outra associação. 
2. Você também deseja cortar a hora de início e término de um ativo. Para fazer isso, você poderia criar um filtro local e definir a hora de início/término. 
3. Deseja combinar ambos desses filtros (sem combinação que você precisa adicionar a filtragem de qualidade para o filtro de filtragem que tornará o uso de filtro difícil).

Para combinar filtros, você precisa definir os nomes de filtro à lista de manifesto/reprodução URL com ponto e vírgula separada. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que características de filtros e você tem outra nomeado *MyStartTime* para definir uma hora de início específica. Você pode combiná-los como esta:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Você pode combinar até 3 filtros. 

Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


##<a name="know-issues-and-limitations"></a>Conheça os problemas e limitações

- Manifesto dinâmico opera em GOP limites (quadros chave), portanto, filtrando tem precisão de GOP. 
- Você pode usar o mesmo nome de filtro para filtros globais e locais. Observe o filtro local têm precedência e substituirá filtros globais.
- Se você atualizar um filtro, pode levar até 2 minutos para o ponto de extremidade de streaming atualizar as regras. Se o conteúdo foi servido usando alguns filtros (e armazenados em cache proxies e CDN caches), atualizar esses filtros pode resultar em falhas de player. É recomendável para limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Consulte também

[Fornecimento de conteúdo a visão geral de clientes](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 