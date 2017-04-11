<properties
    pageTitle="  Publicar conteúdo com o portal do Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de seu conteúdo com o Azure portal de publicação."
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

# <a name="publish-content-with-the-azure-portal"></a>Publicar conteúdo com o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTANTE](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Para fornecer uma URL que pode ser usada para transmitir ou baixar o conteúdo de usuário, primeiro é necessário "publicação" seu ativo, criando um localizador. Locators fornecem acesso a arquivos contidos em ativo. Serviços de mídia compatível com dois tipos de localizadores: 

- Streaming locators (OnDemandOrigin), usados para fluxo adaptativa (por exemplo, para fluxo MPEG traço, HLS ou Streaming suave). Para criar um localizador de streaming seu ativo deve conter um arquivo de .ism. 
- Localizadores progressivos de (SAS), usados para a entrega de vídeo por meio de download progressivo.


Uma URL de streaming tem o seguinte formato e você pode usá-lo para ser reproduzido Streaming suave ativos.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar um HLS streaming URL, acrescentar (formato = m3u8-aapl) para a URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar um traço MPEG streaming URL, acrescentar (formato = mpd-tempo-csf) para a URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL de SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Para obter mais informações, consulte [Visão geral do conteúdo entregando](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Se você usou o portal para criar locators antes de março de 2015, locators com uma data de validade de dois anos foram criados.  

Para atualizar a data de expiração de um localizador, use o [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou APIs do [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Observe que quando você atualizar a data de expiração de um localizador SAS, a URL é alterado.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usar o portal para publicar um ativo

Para usar o portal para publicar um ativo, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
1. Selecione **configurações** > **ativos**.
1. Selecione o ativo que você deseja publicar.
1. Clique no botão **Publicar** .
1. Selecione o tipo de localizador.
2. Pressione a **Adicionar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL será adicionada à lista de **URLs publicado**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo do portal

O portal Azure fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Executar** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicar:

- Verifique se que o vídeo foi publicado.
- Este **Media player** reproduz do padrão streaming ponto de extremidade. Se você deseja reproduzir a partir de um ponto de extremidade de streaming não padrão, clique em para copiar a URL e usar outro jogador. Por exemplo, [O reprodutor de serviços de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- O ponto de extremidade streaming do qual você está streaming deve estar executando.  
- Para transmitir de um ponto de extremidade streaming, você deve adicionar pelo menos uma unidade de streaming. Para obter mais informações, consulte [Este](media-services-portal-scale-streaming-endpoints.md) tópico.   

##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


