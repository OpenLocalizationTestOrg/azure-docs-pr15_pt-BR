<properties 
    pageTitle="Reprodução seu conteúdo | Microsoft Azure" 
    description="Este tópico lista players existentes que você pode usar para reprodução seu conteúdo." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>Reproduzir seu conteúdo com jogadores existentes

Serviços de mídia do Azure oferece suporte a vários formatos de streaming populares, como Streaming suave, Live Streaming HTTP e MPEG-traço. Este tópico o direciona para players existentes que você pode usar para testar seus fluxos.

>[AZURE.NOTE]Para reproduzir dinamicamente empacotados ou conteúdo dinamicamente criptografado, certifique-se de obter pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja distribuir seu conteúdo. Para obter informações sobre dimensionamento unidades streaming, consulte: [como escalar streaming unidades](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>O player de conteúdo de serviços de mídia portal Azure

O portal do **Azure** fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado (Verifique se ele foi [publicado](media-services-portal-publish.md)) e clique no botão **Executar** na parte inferior do portal.

Algumas considerações se aplicar:

- O **MEDIA PLAYER de conteúdo de serviços** é reproduzido do padrão streaming ponto de extremidade. Se você deseja reproduzir a partir de um ponto de extremidade de streaming não padrão, use outro jogador. Por exemplo, [Player de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Player de mídia do Microsoft Azure

Usar o [Player de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reprodução seu conteúdo (limpar ou protegido) em qualquer um dos seguintes formatos:

- Streaming suave
- MPEG TRAÇO
- HLS
- Progressivo MP4


###<a name="flash-player"></a>O Flash Player

####<a name="aes-encrypted-with-token"></a>Criptografia AES com Token

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Players Silverlight

####<a name="monitoring"></a>Monitoramento

[http://SMF.cloudapp.NET/HealthMonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady com Token

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Players de traço

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Outros

Para testar URLs de HLS você também pode usar:

- **Safari** em um dispositivo iOS ou
- **3ivx HLS Player** no Windows.

##<a name="developing-video-players"></a>Desenvolvendo players de vídeo

Para obter informações sobre como desenvolver seus próprios jogadores, consulte [players de vídeo de desenvolvimento](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
