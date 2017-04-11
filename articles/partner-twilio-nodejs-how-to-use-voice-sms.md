<properties 
    pageTitle="Usando Twilio para voz, VoIP e SMS mensagens no Azure" 
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos em Node." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Usando Twilio para voz, VoIP e SMS mensagens no Azure

Este guia demonstra como criar aplicativos que se comunicar com Twilio e Node no Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>O que é Twilio?

Twilio é uma plataforma de API que torna mais fácil para os desenvolvedores fazer e receber chamadas telefônicas, enviar e receber mensagens de texto e inserir chamada VoIP em aplicativos móveis baseadas em navegador e nativos.  Vamos brevemente sobre como isso funciona antes de começar.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e mensagens de texto

Twilio permite que os desenvolvedores [compra números de telefone programável] [ purchase_phone] que pode ser usado para enviar e receber chamadas e mensagens de texto.  Quando um número de Twilio recebe uma chamada de entrada ou texto, Twilio enviará seu aplicativo web um HTTP POST ou uma solicitação GET, solicitando que você para obter instruções sobre como lidar com o texto ou a chamada.  Seu servidor responderá à solicitação HTTP do Twilio com [TwiML][twiml], um conjunto de marcas XML que contêm instruções sobre como lidar com uma chamada ou texto simples.  Veremos exemplos de TwiML em alguns momentos.

### <a name="making-calls-and-sending-text-messages"></a>Fazer chamadas e enviar mensagens de texto

Fazendo solicitações HTTP a API do serviço web Twilio, os desenvolvedores podem enviar mensagens de texto ou iniciar saídas chamadas telefônicas.  Para chamadas de saída, o desenvolvedor também deve especificar uma URL que retorna TwiML instruções sobre como lidar com a chamada de saída depois que ele está conectado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporação de recursos de VoIP no código de interface do usuário (JavaScript, iOS ou Android)

Twilio fornece um SDK do lado do cliente que pode transformar qualquer navegador da web da área de trabalho, aplicativo iOS ou Android app em um telefone VoIP.  Neste artigo, nos concentraremos em como usar VoIP chamar no navegador.  Além do SDK do JavaScript Twilio executado no navegador, um aplicativo de servidor (nosso aplicativo node) deve ser usado para emitir um "token de capacidade" ao cliente JavaScript.  Você pode ler mais sobre como usar o VoIP com Node [no blog de desenvolvimento do Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscreva-se para Twilio (Microsoft desconto)

Antes de usar os serviços de Twilio, você deve primeiro [inscrever-se para uma conta][signup].  Os clientes do Microsoft Azure receber um desconto especial - [Certifique-se para se inscrever aqui][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implantar um site do Azure Node

Em seguida, você precisará criar um site de Node em execução no Azure.  [A documentação oficial para isso está localizada aqui][azure_new_site].  Em um alto nível, você fará o seguinte:

* Se inscrever para uma conta do Azure, caso você não tenha um
* Usando o console de administração Azure para criar um novo site
* Adicionar suporte de controle de origem (vamos supor que você usou gito)
* Criando um arquivo `server.js` com um aplicativo de web Node simples
* Implantando este aplicativo simples para o Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurar o módulo Twilio

Em seguida, podemos começará a escrever um aplicativo de Node simples que usa a API Twilio.  Antes de começar, precisamos configurar nossas credenciais de conta de Twilio.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurando credenciais de Twilio variáveis de ambiente do sistema

Para fazer solicitações autenticadas em relação a Twilio back-end, precisamos nossa conta SID e auth token, qual função como o nome de usuário e a senha definida para nossa conta Twilio. A maneira mais segura de configurá-los para uso com o módulo de nó no Azure é por meio de variáveis de ambiente do sistema, o qual você pode definir diretamente no console de administrador Azure.

Selecione seu site node e clique no link "Configurar".  Se você rolar para baixo um pouco, você verá uma área onde você pode definir propriedades de configuração do aplicativo.  Insira suas credenciais de conta Twilio ([encontrado no seu painel de Twilio][twilio_dashboard]) conforme mostrado - garantir nomeá-los "TWILIO_ACCOUNT_SID" e "TWILIO_AUTH_TOKEN", respectivamente:

![Console de administração do Azure][azure-admin-console]

Depois de ter configurado essas variáveis, reinicie o seu aplicativo no console do Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarando o módulo Twilio no package.json

Em seguida, precisamos criar um package.json para gerenciar nossas dependências de módulo nó via [npm].  No mesmo nível como o arquivo de "Server. js" que você criou no tutorial Azure/node.js, crie um arquivo denominado "package.json".  Dentro desse arquivo, coloque o seguinte:

  {"nome": "-nome do aplicativo", "versão": "0.0.1", "particular": verdadeiro, "scripts": {"começar": "servidor de nó"}, "dependências": {"expressa": "3.1.0", "ejs": "*", "twilio": "*"}}

Isso declara o módulo twilio como uma dependência, bem como o populares [framework web express] [ express] e o mecanismo de modelo EJS.  Okey, agora podemos está tudo pronto - vamos escrever um código!

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Fazer uma chamada de saída

Vamos criar um formulário simples que irá colocar uma chamada para um número que escolhemos.  Abra o Server. js e digite o seguinte código.  Observe onde está escrito "CHANGE_ME" - colocar o nome do seu site azure lá:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Em seguida, crie um diretório chamado "visualizações" - diretório, crie um arquivo denominado "index.ejs" com o seguinte conteúdo:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Agora, implantar seu site para o Azure e abra sua casa.  Você deve conseguir insira seu número de telefone no campo de texto e receber uma chamada de seu número de Twilio!

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Agora, vamos configurar uma interface de usuário e a lógica de manipulação de formulário para enviar uma mensagem de texto.  Abra o "Server. js" e adicione o seguinte código após a última chamada "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

Em "views/index.ejs", adicione outro formulário sob o primeiro que enviar um número e uma mensagem de texto:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Reimplantar seu aplicativo do Azure e você agora deve conseguir enviar que formam e enviar uma mensagem de texto si mesmo (ou qualquer um dos seus amigos mais próximos)!

<a id="nextsteps"/>
## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de uso node e Twilio para criar aplicativos que se comunicar.  Mas esses exemplos mal esboçar a superfície de que é possível com Twilio e Node.  Para obter mais informações com Twilio Node, confira os seguintes recursos:

* [Documentos do módulo oficial][docs]
* [Tutorial sobre VoIP com aplicativos de Node][voipnode]
* [Votr - um SMS em tempo real votação aplicativo com node e CouchDB (três partes)][votr]
* [Programação de par no navegador com Node][pair]

Esperamos que você conhece hackers node e Twilio no Azure!

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[NPM]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



