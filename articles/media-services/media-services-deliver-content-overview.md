<properties
    pageTitle="Fornecer conteúdo aos clientes | Microsoft Azure"
    description="Este tópico fornece uma visão geral do que está envolvido no fornecimento de seu conteúdo com os serviços de mídia do Azure."
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="deliver-content-to-customers"></a>Fornecer conteúdo aos clientes
Quando você estiver entregando seu conteúdo de streaming ou vídeo sob demanda aos clientes, seu objetivo é fornecer o vídeo de alta qualidade para vários dispositivos em condições de rede diferente.

Para atingir esse objetivo, você pode:

- Codifica seu fluxo para um fluxo de vídeo multi-taxa de bits (taxa de bits adaptativa). Isso executarão condições de qualidade e da rede.
- Use serviços de mídia do Microsoft Azure [embalagem dinâmica](media-services-dynamic-packaging-overview.md) para dinamicamente empacotar novamente seu fluxo em protocolos diferentes. Isso executarão do streaming em dispositivos diferentes. Serviços de mídia dá suporte a entrega da taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG-traço e HDS (para somente licenciados Adobe faltava/acesso).

Este artigo fornece uma visão geral dos conceitos importantes de fornecimento de conteúdo.

Para verificar problemas conhecidos, consulte [problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Embalagem dinâmica

Com o pacote dinâmico que fornece serviços de mídia, você pode fornecer o conteúdo de taxa de bits adaptativa MP4 ou Streaming suave codificado no streaming formatos compatíveis com os serviços de mídia (MPEG-traço, HLS, Streaming suave, HDS) sem precisar empacotar novamente em esses formatos de fluxo contínuo. É recomendável oferecer seu conteúdo com embalagem dinâmico.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de adaptativa taxa de bits MP4 arquivos ou taxa de bits adaptativa Streaming suave.
- Obtenha pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja distribuir seu conteúdo. Para obter mais informações, consulte [como dimensionar streaming unidades reservadas sob demanda](media-services-portal-manage-streaming-endpoints.md).

Com a embalagem dinâmica, armazenar e pagar os arquivos no formato de armazenamento único. Serviços de mídia será construir e servir a resposta apropriada com base em suas solicitações.

Além de fornecer acesso aos recursos de embalagem dinâmico, o streaming unidades reservadas sob demanda fornece capacidade de egresso dedicado que pode ser comprada em incrementos de 200 Mbps. Por padrão, sob demanda streaming está configurado em um modelo de instância compartilhada para qual servidor recursos (por exemplo, capacidade de computação ou egresso) são compartilhados com todos os outros usuários. Você pode melhorar a uma taxa de transferência de streaming sob demanda adquirindo sob demanda streaming reservadas unidades.

Para obter mais informações, consulte [embalagem dinâmica](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

Você pode definir filtros para seus ativos com os serviços de mídia. Esses filtros são regras de servidor que ajudam seus clientes a fazer algo como reproduzir uma seção específica de um vídeo ou especificar um subconjunto de representações de áudio e vídeo que dispositivo do cliente pode manipular (em vez de todas as representações que estão associadas a ativo). A filtragem é obtida por meio de *manifestos dinâmicos* que são criados quando seu cliente solicita streaming de vídeo com base em um ou mais filtros especificados.

Para obter mais informações, consulte [filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Locators

Para fornecer uma URL que pode ser usada para transmitir ou baixar o conteúdo de usuário, primeiro é necessário publicar seu ativo, criando um localizador. Um localizador fornece um ponto de entrada para acessar os arquivos contidos em um ativo. Serviços de mídia compatível com dois tipos de localizadores:

- OnDemandOrigin locators. Estes são usados para transmitir mídia (por exemplo, MPEG-traço, HLS ou Streaming suave) ou baixar os arquivos de cada vez.
-  Localizadores de URL de assinatura (SAS) de acesso compartilhado. Eles são usados para baixar arquivos de mídia no seu computador local.

Uma *política de acesso* é usada para definir as permissões (como ler, gravar e lista) e a duração para o qual um cliente tem acesso para um determinado ativo. Observe que a permissão de lista (AccessPermissions.List) não deve ser usada na criação de um localizador de OrDemandOrigin.

Locators têm datas de vencimento. O portal do Azure define uma data de validade 100 anos no futuro para localizadores.

>[AZURE.NOTE] Se você usou o portal do Azure para criar locators antes de março de 2015, esses locators foram definidas para expirar após dois anos.

Para atualizar a data de expiração de um localizador, use o [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou APIs do [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Observe que quando você atualizar a data de expiração de um localizador SAS, a URL é alterado.

Locators não foram projetados para gerenciar o controle de acesso por usuário. Você pode conceder direitos de acesso diferente para usuários individuais, usando as soluções de gerenciamento de direitos digitais (DRM). Para obter mais informações, consulte [Protegendo mídia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando você cria um localizador, pode haver um atraso de 30 segundos devido a processos de propagação no armazenamento do Azure e de armazenamento necessário.


## <a name="adaptive-streaming"></a>Adaptável streaming

Tecnologias de taxa de bits adaptativa permitem que aplicativos do player de vídeo determinar as condições de rede e selecionar várias bitrates. Quando a comunicação de rede cai, o cliente pode selecionar uma taxa de bits inferior para que reprodução possa continuar com menor qualidade de vídeo. Como melhorem a condições da rede, o cliente pode alternar para uma taxa de bits mais alta qualidade do vídeo aprimorada. Serviços de mídia do Azure suporta as seguintes tecnologias de taxa de bits adaptativa: Streaming Live HTTP (HLS), Streaming suave, MPEG-traço e HDS.

Para fornecer aos usuários streaming URLs, você deve primeiro criar um localizador de OnDemandOrigin. Criando o localizador fornece o caminho base para o ativo que contém o conteúdo que deseja transmitir. No entanto, para poder transmitir esse conteúdo, você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto streaming, você deve concatenar o valor do caminho do localizador e o manifesto (filename.ism) nome do arquivo. Em seguida, **acrescente/manifesto** e um formato apropriado (se necessário) para o caminho de localizador.

>[AZURE.NOTE]Você também pode transmitir seu conteúdo em uma conexão SSL. Para fazer isso, certifique-se de que URLs streaming iniciar com HTTPS.

Você só pode transmitir sobre SSL se o ponto de extremidade streaming do qual você distribuir o conteúdo foi criado após 10 de setembro de 2014. Se suas URLs streaming se baseiam os pontos de extremidade streaming criados após 10 de setembro de 2014, a URL contém "streaming.mediaservices.windows.net". Streaming URLs que contêm "origin.mediaservices.windows.net" (o antigo formato) não suportam SSL. Se a URL for no formato antigo e você quer ser capaz de transmitir em SSL, crie um novo ponto de extremidade de streaming. URLs de uso de acordo com o ponto de extremidade de streaming de novo para transmitir seu conteúdo em SSL.


## <a name="streaming-url-formats"></a>Formatos de URL Streaming

### <a name="mpeg-dash-format"></a>Formato de MPEG-traço

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=mpd-time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato de V4 Apple HTTP Live Streaming (HLS)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato de V3 Apple HTTP Live Streaming (HLS)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato de Apple HTTP Live Streaming (HLS) com filtro somente de áudio

Por padrão, faixas somente de áudio estão incluídas no HLS manifesto. Isso é necessário para certificação Apple Store para redes celulares. Nesse caso, se um cliente não tem largura de banda suficiente ou conectado por meio de uma conexão de 2G, reprodução alterna para o somente de áudio. Isso ajuda a manter o streaming de conteúdo sem buffer, mas há nenhum vídeo. Em alguns cenários, player buffer pode ser preferível somente de áudio. Se você quiser remover o controle somente de áudio, adicione **somente de áudio = false** para a URL.

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3,Audio-only=False)

Para obter mais informações, consulte [suporte dinâmico composição manifesto e HLS recursos adicionais de saída](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Formato de Streaming suave

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Manifesto de Streaming 2.0 suave (manifesto herdado)

Por padrão, formato de manifesto Streaming suave contém a marca de repetição (r-marca). No entanto, alguns players não dão suporte a marca r. Clientes com esses players podem usar um formato que desabilita a marca de r:

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS (para licenciados do Adobe faltava/acesso somente)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Download progressivo

Com download progressivo, você pode iniciar a reprodução de mídia antes de todo o arquivo foi baixado. Cada vez que você não pode baixar .ism * (ismv, isma, ismt ou ismc) arquivos.

Para cada vez baixar conteúdo, use o tipo de OnDemandOrigin de localizador. O exemplo a seguir mostra a URL com base no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Você deve descriptografar qualquer ativos criptografados armazenamento que você deseja transmitir do serviço de origem para download progressivo.

## <a name="download"></a>Baixar

Para baixar o conteúdo para um dispositivo de cliente, você deve criar um localizador de SAS. O localizador SAS lhe dá acesso ao contêiner de armazenamento do Azure onde o arquivo está localizado. Para criar o URL de download, é necessário inserir o nome do arquivo entre o host e assinatura SAS.

O exemplo a seguir mostra a URL que baseia-se no localizador de SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Aplica considerando o seguinte:

- Você deve descriptografar qualquer ativos criptografados armazenamento que você deseja transmitir do serviço de origem para download progressivo.
- Um download que não terminou dentro de 12 horas falhará.

## <a name="streaming-endpoints"></a>Pontos de extremidade de streaming

Um ponto de extremidade streaming representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou a uma rede de distribuição de conteúdo (CDN) para distribuição adicional. O fluxo de saída de um serviço de streaming de ponto de extremidade pode ser um fluxo ao vivo ou um ativo de vídeo sob demanda em sua conta de serviços de mídia. Você também pode controlar a capacidade do serviço de ponto de extremidade do streaming lidar com crescentes necessidades de largura de banda ajustando streaming unidades reservadas. Você deve alocar pelo menos uma unidade reservada para aplicativos em um ambiente de produção. Para obter mais informações, consulte [como dimensionar um serviço de mídia](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações para Streaming suave manifestam versão

Antes do lançamento do serviço de julho de 2016 – quando ativos produzidos pelo padrão de codificador de mídia, fluxo de trabalho do Media codificador Premium ou o codificador de mídia do Azure anteriores foram transmitidos usando embalagem dinâmica – o Streaming suave manifesto retornado deve estar em conformidade com versão 2.0. Na versão 2.0, as durações de fragmento não use as marcas de repetir chamados ('r'). Por exemplo:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na versão de serviço de julho de 2016, o manifesto gerado Streaming suave atende às versão 2.2, com durações de fragmento usando marcas de repetição. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes herdados Streaming suave podem não suportar as marcas de repetição e falharão carregar o manifesto. Para atenuar esse problema, você pode usar o parâmetro de formato de manifesto herdados **(formato = fmp4 v20)** ou atualizar seu cliente para a versão mais recente, que oferece suporte a marcas de repetição. Para obter mais informações, consulte [suave 2.0 Streaming](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados

[Atualizar localizadores de serviços de mídia após sem interrupção chaves de armazenamento](media-services-roll-storage-access-keys.md)
