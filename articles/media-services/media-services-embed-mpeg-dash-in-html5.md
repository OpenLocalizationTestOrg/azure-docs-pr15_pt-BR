<properties 
    pageTitle="Incorporar um vídeo de Streaming adaptativa MPEG-traço em um aplicativo de HTML5 com DASH.js | Microsoft Azure" 
    description="Este tópico demonstra como incorporar um vídeo de Streaming adaptativa MPEG-traço em um aplicativo de HTML5 com DASH.js." 
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


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um vídeo de Streaming adaptativa MPEG-traço em um aplicativo de HTML5 com DASH.js

##<a name="overview"></a>Visão geral

MPEG-traço é um padrão ISO para a tecnologia adaptativa streaming de conteúdo de vídeo, que oferece benefícios significativos para aqueles que desejam fornecer saída de streaming de vídeo de alta qualidade, adaptativo. Com o MPEG-traço, o fluxo de vídeo será automaticamente soltar uma definição inferior quando a rede fica congestionada. Isso reduz a probabilidade do visualizador vendo um vídeo "pausado", enquanto o player baixa os próxima alguns segundos para reproduzir (também conhecidos como buffer). Como reduz o congestionamento da rede, o player de vídeo alternadamente retornará um fluxo de qualidade superior. A capacidade de se adaptar a largura de banda necessária também resulta em uma hora de início rápida para o vídeo. Isso significa que os primeiros segundos podem ser reproduzidos em um segmento do fast para baixar mais baixo qualidade e, em seguida, etapa até um conteúdo de uma vez suficientes de qualidade superior tenha sido buffer.

Dash.js é um player de vídeo MPEG-traço em Abrir origem escrito em JavaScript. Seu objetivo é fornecer um player robusto, de plataforma híbrida que pode ser reutilizado livremente em aplicativos que requerem reprodução de vídeo. Ele fornece reprodução de MPEG-traço em qualquer navegador que ofereça suporte a W3C mídia fonte extensões MSE (), hoje Chrome, Microsoft Edge e IE11 (outros navegadores indicou sua intenção para dar suporte a MSE). Para saber mais sobre DASH.js, js consulte GitHub dash.js repositório.


##<a name="creating-a-browser-based-streaming-video-player"></a>Criando um player de vídeo streaming baseadas em navegador

Para criar uma página da web simples que exibe um player de vídeo com o esperado controla tal um reproduzir, pausar, retroceder etc., você precisará:

1. Criar uma página HTML
1. Adicionar a marca de vídeo
1. Adicionar o player dash.js
1. Inicializar o player
1. Adicionar um estilo CSS
1. Exibir os resultados em um navegador que implementa MSE

Inicializar o player pode ser concluído em poucos das linhas de código JavaScript. Usando dash.js, na realidade é simple inserir um vídeo MPEG-traço em seus aplicativos baseado em navegador.

##<a name="creating-the-html-page"></a>Criando a página de HTML

A primeira etapa é criar uma página HTML padrão que contém o elemento de **vídeo** , salvar este arquivo como basicPlayer.html, como ilustra o exemplo a seguir:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Adicionando o Player DASH.js

Para adicionar a implementação de referência de dash.js para o aplicativo, você precisará pegue o arquivo dash.all.js da 1.0 versão do projeto dash.js. Isso deve ser salvo na pasta JavaScript do seu aplicativo. Este é um arquivo de conveniência que reúne todos os códigos de dash.js necessárias em um único arquivo. Se você tiver uma aparência em torno do repositório de dash.js, você será encontrar os arquivos individuais, testar código e muito mais, mas se tudo o que você deseja fazer é usar dash.js e o arquivo dash.all.js é o que você precisa.

Para adicionar o player dash.js a seus aplicativos, adicione uma marca de script a seção de cabeçalho de basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Em seguida, crie uma função para inicializar o player quando a página for carregada. Adicione o seguinte script após a linha na qual você carregar dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Essa função primeiro cria um DashContext. Isso é usado para configurar o aplicativo para um ambiente de tempo de execução específico. Do ponto de vista técnico, ele define as classes que a estrutura de inserção de dependência deve usar ao construir o aplicativo. Na maioria dos casos, você usará Dash.di.DashContext.

Em seguida, crie uma instância da classe principal do framework dash.js, MediaPlayer. Essa classe contém o núcleo métodos necessários como reproduzir e pausar, gerencia a relação com o elemento de vídeo e também gerencia a interpretação do arquivo de descrição de apresentação de mídia (MPD) que descreve o vídeo para ser reproduzido.

A função startup() da classe MediaPlayer denomina-se para garantir que o player está pronto para reproduzir o vídeo. Entre outras coisas que esta função garante que todas as classes necessárias (conforme definido pelo contexto) tiverem sido carregadas. Quando o player estiver pronto, você pode anexar o elemento de vídeo usando a função attachView(). Isso permite que o MediaPlayer inserir o fluxo de vídeo no elemento e também controlar a reprodução conforme necessário.

Passe o URL do arquivo MPD para o MediaPlayer para que ele saiba sobre o vídeo esperamos para reproduzir. A função setupVideo() recém-criado deverão ser executadas assim que a página for carregada totalmente. Faça isso usando o evento onload do elemento de corpo. Alterar sua <body> elemento para:

    <body onload="setupVideo()">

Finalmente, defina o tamanho do vídeo elemento usando CSS. Em um ambiente de streaming adaptativo, isso é especialmente importante porque o tamanho do vídeo que está sendo executado pode ser alterado conforme reprodução se adapta às condições de rede. Nesta demonstração simples simplesmente força o elemento de vídeo para ser 80% da janela do navegador disponível adicionando o CSS seguintes à seção de cabeçalho da página:
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Reproduzir um vídeo

Reproduzir um vídeo, aponte o navegador o arquivo basicPlayback.html e clique em Reproduzir no player de vídeo exibido.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Desenvolver aplicativos do player de vídeo](media-services-develop-video-players.md)

[GitHub dash.js repositório](https://github.com/Dash-Industry-Forum/dash.js) 
