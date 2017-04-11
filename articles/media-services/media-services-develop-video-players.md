<properties 
    pageTitle="Desenvolver aplicativos do player de vídeo" 
    description="O tópico fornece links para estruturas de Player e plug-ins que você pode usar para desenvolver seus próprios aplicativos cliente que podem consumir o fluxo de mídia de serviços de mídia." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Desenvolver aplicativos do player de vídeo

##<a name="overview"></a>Visão geral

Serviços de mídia do Azure fornece as ferramentas necessárias para criar aplicativos do player de cliente sofisticados e dinâmicos para maioria das plataformas inclusive: iOS dispositivos, dispositivos Android, Windows, Windows Phone, Xbox e decodificador caixas. Este tópico também fornece links para SDK e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos cliente que podem consumir o fluxo de mídia de serviços de mídia do Azure.

##<a name="azure-media-player"></a>Player de mídia do Microsoft Azure

[Azure Media Player](http://aka.ms/ampinfo) é um player de vídeo da web criado para reproduzir conteúdo de mídia de serviços de mídia do Microsoft Azure em uma ampla variedade de dispositivos e navegadores. Azure Media Player utiliza padrões do setor, como HTML5, extensões de origem de mídia (MSE) e extensões de mídia criptografado (EME) para fornecer uma experiência de streaming adaptativa rico. Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de mídia do Azure usa Flash e do Silverlight como tecnologia de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface JavaScript unificada acessem APIs. Isso permite conteúdo atendido pelos serviços de mídia do Azure para ser reproduzido em um diversos dispositivos e navegadores sem qualquer esforço extra.

Serviços de mídia do Microsoft Azure permite conteúdo a ser atendidas com traço, Streaming suave e HLS streaming formatos para reproduzir conteúdo. Azure Media Player leva em conta esses vários formatos e reproduzirá automaticamente o link melhor com base nos recursos de plataforma/navegador. Serviços de mídia do Microsoft Azure também permite criptografia dinâmico de ativos com criptografia PlayReady ou AES de 128 bits criptografia de envelope. Azure Media Player permite descriptografia do PlayReady e AES de 128 bits conteúdo criptografado quando adequadamente configuradas. 

Para obter mais informações:

- [Player de mídia do Microsoft Azure](http://aka.ms/ampinfo)
- [Documentação do Player de mídia do Microsoft Azure](http://aka.ms/ampdocs) 
- [Blog de Introdução do Player de mídia do Microsoft Azure obtendo](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Inscrever-se para se manter atualizado com as últimas do Player de mídia do Azure](http://aka.ms/ampsignup)
- [Adicionar novas solicitações de recurso, ideias, comentários](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para a criação de aplicativos do Player

Você também pode usar qualquer um dos seguintes SDKs:

- [Suaves Streaming SDK do cliente](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Aplicativo da Windows Store Streaming suave](media-services-build-smooth-streaming-apps.md)
- [Plataforma do Microsoft Media: Estrutura de Player](http://playerframework.codeplex.com/) 
- [HTML5 Documentação Player Framework](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft suave Streaming plug-in para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licenciamento Microsoft® suave Streaming cliente portabilidade Kit](http://aka.ms/sspk) 
- [Desenvolvimento de aplicativos de vídeo do XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Anunciando

Serviços de mídia do Azure oferece suporte para inserção de anúncios na plataforma de mídia do Windows: estruturas de Player. Estruturas de Player com suporte do ad estão disponíveis para dispositivos iOS, Silverlight, Windows Phone 8 e Windows 8. Cada estrutura de player contém código de exemplo que mostra como implementar um aplicativo de player. Há três tipos diferentes de anúncios, que você pode inserir em sua mídia:

Linear – anúncios de quadro completo que pausar o vídeo principal

Não linear – anúncios de sobreposição que são exibidos como o vídeo principal estiver em execução, geralmente um logotipo ou outra imagem estática posicionado dentro do player

Companion – anúncios que são exibidas fora do player

Anúncios podem ser inseridos em qualquer ponto na linha do tempo do vídeo principal. Você deve saber o player quando tocar o anúncio e quais anúncios para reproduzir. Isso é feito usando um conjunto de arquivos padrão baseado em XML: vídeo Ad serviço modelo (VAST), Digital vídeo vários Ad reprodução (VMAP), modelo de ordenar abstratos mídia (BELA) e Digital vídeo Player Ad Interface definição (VPAID). Arquivos grandes especificam quais anúncios para exibir. Arquivos VMAP especificam quando reproduzir diversos anúncios e conter XML grande. Arquivos de BELA são outra maneira de anúncios de sequência que também podem conter XML grande. Arquivos VPAID definem uma interface entre o player de vídeo e o anúncio ou o servidor do ad. Para obter mais informações, consulte [Inserir anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre o suporte de legendas e anúncios fechada no Live streaming vídeos, consulte [suporte legenda codificada e padrões de inserção do Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Incorporar um vídeo de Streaming adaptativa MPEG-traço em um aplicativo de HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js repositório](https://github.com/Dash-Industry-Forum/dash.js)
 
