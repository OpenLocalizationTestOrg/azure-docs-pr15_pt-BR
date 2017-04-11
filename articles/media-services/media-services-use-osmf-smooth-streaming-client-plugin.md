<properties 
    pageTitle="Plug-in de Streaming tranquila para a estrutura de mídia Abrir origem" 
    description="Saiba como usar o plug-in Streaming suave do Azure Media Services para a estrutura de mídia de origem aberta do Adobe." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como usar o suave do Microsoft Streaming plug-in para a estrutura de mídia do Adobe Abrir origem

##<a name="overview"></a>Visão geral


O plug-in Microsoft suave Streaming para abrir origem mídia Framework 2.0 (SS para OSMF) estende os recursos padrão do OSMF e adiciona Microsoft suave Streaming de reprodução de conteúdo para os players OSMF novos e existentes. O plug-in também adiciona recursos de reprodução de Streaming suave para reprodução de mídia estroboscópicas (SMP).

SS para OSMF inclui duas versões do plug-in:

- Estático Streaming suave plug-in para OSMF (. SWC)

- Dinâmico Streaming suave plug-in para OSMF (. SWF)

Este documento supõe que o leitor tenha um conhecimento de trabalho geral da OSMF e OSMF plug-ins. Para obter mais informações sobre OSMF, consulte a documentação do [site oficial do OSMF](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Plug-in Streaming suave para OSMF 2.0

O plug-in oferece suporte para carregamento e a reprodução de conteúdo de Streaming suave sob demanda com os seguintes recursos:

- Reprodução de Streaming suave sob demanda (reproduzir, pausar, Seek, parada)
- Reprodução de Streaming suave ao vivo (reproduzir)
- Funções DVR Live (pausar, Seek, DVR reprodução, ir-to-Live)
- Suporte para codecs de vídeo - h. 264
- Suporte para codecs de áudio - AAC
- Vários idiomas áudio migrando com APIs interno OSMF
- Seleção de qualidade de reprodução de max com APIs interno OSMF
- Secundários fechado legendas com OSMF legendas plug-in
- Adobe&reg; Flash&reg; Player 11,4 ou superior.
- Esta versão suporta apenas OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Recursos com suporte e problemas conhecidos

Para obter uma lista completa de problemas conhecidos, recursos sem suporte e recursos com suporte, consulte [Este documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## <a name="loading-the-plugin"></a>Carregar o plug-in
Plug-ins OSMF podem ser carregados estática (em tempo de compilação) ou dinamicamente (em tempo de execução). O plug-in Streaming suave para download OSMF inclui versões dinâmicas e estáticas.

- Carregamento estático: para carregar estática, é necessário um arquivo de biblioteca estática (SWC). Plug-ins estáticos são adicionados como uma referência para os projetos e a mesclagem dentro do arquivo de saída final em tempo de compilação.

- Carregamento dinâmico: para carregar dinamicamente, é necessário um arquivo de (SWF) pré-compilados. Plug-ins dinâmicos são carregados no runtime e não incluídos na saída do projeto. (Saída compilada) Plug-ins dinâmicos podem ser carregados usando protocolos HTTP e arquivo.

Para obter mais informações sobre carregamento estático e dinâmico, consulte a [página de plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)de oficial.

###<a name="ss-for-osmf-static-loading"></a>SS para carregamento estático OSMF
O trecho de código a seguir mostra como carregar o plug-in SS para OSMF estática e reproduzir um vídeo básico usando OSMF MediaFactory classe. Antes de incluir o SS para código OSMF, certifique-se de que a referência de projeto inclui o plug-in estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico OSMF

O trecho de código a seguir mostra como carregar o plug-in SS para OSMF dinamicamente e reproduzir uma basic vídeo usando a classe de OSMF MediaFactory. Antes de incluir o SS para código OSMF, copie o plug-in dinâmico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" à pasta do projeto se desejar carregar usando o protocolo de arquivo, ou copie em um servidor web para carga HTTP. Não é necessário incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências de projeto.

 
{de pacote
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reprodução de mídia estroboscópicas com o plug-in dinâmico SS ODMF

O Streaming suave para plug-in dinâmico OSMF é compatível com a [Reprodução de mídia estroboscópicas (SMP)](http://osmf.org/strobe_mediaplayback.html). Você pode usar a opção SS OSMF plug-in para adicionar Streaming suave reprodução de conteúdo ao SMP. Para fazer isso, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" em um servidor web para carregamento HTTP usando as seguintes etapas:

1.  Navegar na [página de configuração de reprodução de mídia estroboscópicas](http://osmf.org/dev/2.0gm/setup.html). 
2.  Defina o src como uma fonte de Streaming suave, (por exemplo, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Faça as alterações de configuração desejada e clique em Visualizar e atualizar.
 
    **Observação** Seu servidor de conteúdo da web precisa um crossdomain válida. 
4.  Copie e cole o código para uma página HTML simples utilizando o editor de texto preferido, como no exemplo a seguir:



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Adicionar o plug-in OSMF Streaming suave para o código de inserção e salvar.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Salve sua página HTML e publicar em um servidor web. Navegue até a página da web publicado usando seu favorito Flash&reg; Player habilitado para navegador de Internet (Internet Explorer, Chrome, Firefox, assim por diante).
7.  Reproduzir conteúdo de Streaming suave dentro do Adobe&reg; Flash&reg; Player.

Para obter mais informações sobre o desenvolvimento de OSMF geral, consulte a [página de desenvolvimento OSMF](http://osmf.org/resources.html)de oficial.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Consulte também

[Microsoft adaptativa Streaming plug-in para atualização OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
