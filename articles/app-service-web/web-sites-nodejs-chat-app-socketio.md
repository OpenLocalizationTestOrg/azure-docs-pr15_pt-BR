<properties
    pageTitle="Criar um aplicativo de bate-papo Node com Socket.IO no serviço de aplicativo do Azure"
    description="Um tutorial que demonstra usando socket.io em um aplicativo web do Node hospedado no Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Criar um aplicativo de bate-papo Node com Socket.IO no serviço de aplicativo do Azure

Socket.IO fornece comunicação em tempo real entre o servidor de node e clientes usando WebSocket. Ele também suporta fallback para outros transportes (como sondagem longo) que funcionam com navegadores mais antigos. Este tutorial irá orientá-lo a hospedagem de um aplicativo de chat Socket.IO com base em como um aplicativo web Azure e mostram como dimensionar o aplicativo usando [Azure relacionada Cache]. Para obter mais informações sobre Socket.IO, consulte <http://socket.io/>.

> [AZURE.NOTE] Os procedimentos nesta tarefa se aplicam ao [Aplicativo de serviço Web Apps]; para serviços de nuvem, consulte [criar um aplicativo de bate-papo Node com Socket.IO em um serviço de nuvem do Azure].

## <a name="download-the-chat-example"></a>Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [repositório de Socket.IO GitHub]. Execute as seguintes etapas para baixar o exemplo e adicioná-lo para o projeto que você criou anteriormente.

1.  Baixe um [ZIP ou GZ arquivados lançamento] do projeto Socket.IO (versão 1.3.5 foi usado para este documento)

1.  Extrair o arquivo morto e copiar o **exemplos\\bate-papo** diretório para um novo local. Por exemplo, ** \\nó\\bate-papo**.

## <a name="modify-appjs-and-install-modules"></a>Modificar app.js e instalar módulos

1.  Renomeie o arquivo **js** para **app.js**. Isso permite Azure detectar que se trata de um aplicativo de Node.

1.  Abra o arquivo **app.js** em um editor de texto. Alterar a linha que contém `var io = require('../..')(server);` conforme mostrado abaixo:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Abra o arquivo **package.json** e adicionar uma referência a socket.io em `dependencies`, conforme mostrado abaixo:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. Na linha de comando, altere para o ** \\nó\\bate-papo** npm diretório e uso para instalar os módulos necessários por este aplicativo:

        npm install

    Os módulos será instalado em uma subpasta chamada **node_modules**.

## <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

Siga estas etapas para criar um aplicativo web Azure, habilitar a publicação de gito e, em seguida, habilitar o suporte de WebSocket para o aplicativo web.

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação gratuita do Azure</a>.

1. Instale a Interface de linha do Azure (Azure comando) e se conectar à sua assinatura do Azure. Consulte [instalar e configurar o Azure CLI](../xplat-cli-install.md).

1. Se esta for sua primeira vez configurando um repositório no Azure, você precisa criar credenciais de login. Na CLI Azure, digite o seguinte comando:

        azure site deployment user set [username] [password]

1. Alterar para a ** \\node\chat** diretório e use o comando a seguir para criar um novo Azure web app e um repositório de gito local. Esse comando também cria um gito remoto nomeado 'azure'.

        azure site create mysitename --git

    Você deve substituir 'mysitename' com um nome exclusivo para o aplicativo web.

1. Confirme os arquivos existentes no repositório local usando os seguintes comandos:

        git add .
        git commit -m "Initial commit"

1. Enviar os arquivos para o repositório do Azure Web Apps com o seguinte comando:

        git push azure master

    Quando solicitado, insira suas credenciais da etapa 2. Você receberá mensagens de status como módulos são importados no servidor. Quando esse processo for concluído, o aplicativo será hospedado em seu aplicativo web Azure.

    > [AZURE.NOTE] Durante a instalação do módulo, você pode observar erros que ' o projeto importado … não foi encontrado '. Esses podem ser ignoradas.

1. Socket.IO usa WebSocket, que não está ativado por padrão no Azure. Para habilitar sockets web, use o seguinte comando:

        azure site set -w

    Se solicitado, insira o nome do aplicativo web.

    >[AZURE.NOTE]
    >O 'site azure set -w' comando será funcionam somente com a versão 0.7.4 ou superior da Interface de linha de comando do Azure. Você também pode habilitar o suporte de WebSocket usando o [Portal do Azure](https://portal.azure.com).
    >
    >Para habilitar WebSocket usando o Portal do Azure, clique no aplicativo web da lâmina aplicativos Web, clique em **todas as configurações** > **configurações do aplicativo**. Em **Web Sockets**, clique **em**. Clique em **Salvar**.

1. Para exibir o web app no Azure, use o seguinte comando para iniciar o seu navegador da web e navegue até o aplicativo web hospedado:

        azure site browse

Seu aplicativo agora está em execução no Azure e pode retransmissão mensagens de chat entre diferentes clientes usando Socket.IO.

## <a name="scale-out"></a>Dimensionar

Aplicativos de Socket.IO podem ser dimensionados usando um __adaptador__ para distribuir mensagens e eventos entre várias instâncias do aplicativo. Embora existam vários adaptadores disponível, o adaptador [relacionada socket.io] pode ser usado facilmente com o recurso de Cache relacionada do Azure.

> [AZURE.NOTE] Um requisito adicional para dimensionar uma solução Socket.IO é suporte para sessões de aderência. Sessões de aderência estão ativadas por padrão para Azure Web Apps por meio do Azure solicitar roteamento. Para obter mais informações, consulte [Instância afinidade em Sites do Azure].

### <a name="create-a-redis-cache"></a>Criar um cache relacionada

Execute as etapas em [criar um cache no Cache relacionada do Azure] para criar um novo cache.

> [AZURE.NOTE] Salve o __nome de Host__ e a __chave primária__ para seu cache, pois esses serão necessário nas próximas etapas.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Adicionar a relacionada e módulos relacionada socket.io

1. De uma linha de comando, altere para o __ \\nó\\bate-papo__ diretório e use o comando a seguir.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] As versões especificadas neste comando são as versões usadas ao testar neste artigo.

1. Modificar o arquivo __app.js__ para adicionar o seguinte linhas imediatamente após`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Substitua __redishostname__ e __rediskey__ com o nome de host e a chave para o cache relacionada.

    Isso criará uma publicação e assinar o cliente para o cache relacionada criado anteriormente. Os clientes são usados com o adaptador para configurar Socket.IO para usar o cache relacionada para passar mensagens e eventos entre as instâncias do aplicativo

    > [AZURE.NOTE] Enquanto o adaptador __relacionada socket.io__ possa se comunicar diretamente à relacionada, a versão atual não oferece suporte a autenticação necessária pelo cache Azure relacionada. Portanto a conexão inicial é criada usando o módulo __relacionada__ e, em seguida, o cliente é passado para o adaptador __relacionada socket.io__ .
    >
    > Enquanto o Azure relacionada Cache compatível com conexões seguras usando porta 6380, os módulos usados neste exemplo não dão suporte a conexões seguras a partir de 14/7/2014. O código acima usa o padrão, a porta insegura do 6379.

1. Salvar o modificadas __app.js__

### <a name="commit-changes-and-redeploy"></a>Confirmar as alterações e reimplantar

Na linha de comando na __ \\nó\\bate-papo__ diretório, use os seguintes comandos para confirmar as alterações e reimplantar o aplicativo.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Depois que as alterações foi colocadas no servidor, você pode dimensionar seu site em várias instâncias usando o comando a seguir.

    azure site scale instances --instances #

Onde __#__ é o número de instâncias para criar.

Você pode conectar ao seu aplicativo web de vários navegadores ou computadores para confirmar que as mensagens são enviadas corretamente para todos os clientes.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="connection-limits"></a>Limites de Conexão

Azure Web Apps está disponível em várias SKUs, que determinam os recursos disponíveis para seu site. Isso inclui o número de conexões de WebSocket permitidos. Para obter mais informações, consulte a [página de preços de aplicativos Web].

### <a name="messages-arent-being-sent-using-websockets"></a>Mensagens não estão sendo enviadas com o uso de WebSockets

Se navegadores cliente caindo volta longo sondagem em vez de usar WebSocket, pode ser devido a um dos seguintes.

* **Tente limitar o transporte apenas WebSocket**

    Em ordem para Socket.IO usar WebSocket como o transporte de mensagens, o servidor e o cliente devem suportar WebSocket. Se um ou outro não, Socket.IO será negocie transporte outro, como sondagem longa. A lista de transportes usado pelo Socket.IO padrão é ` websocket, htmlfile, xhr-polling, jsonp-polling`. Você pode forçar apenas usar WebSocket adicionando o código a seguir ao arquivo **app.js** , após a linha contendo `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Observe que navegadores mais antigos que não oferecem suporte para WebSocket não será capazes de se conectar ao site enquanto o código acima estiver ativo, como ele restringe a comunicação para o WebSocket.

* **Usar o protocolo SSL**

    WebSocket depende de alguns menor cabeçalhos HTTP usados, como o cabeçalho **de atualização** . Alguns dispositivos de rede intermediários, como proxies da web, podem remover esses cabeçalhos. Para evitar esse problema, você pode estabelecer a conexão WebSocket sobre SSL.

    Uma maneira fácil de fazer isso é configurar Socket.IO para `match origin protocol`. Isso instrui Socket.IO para proteger a comunicação de WebSockets a mesma que a solicitação HTTP/HTTPS original para a página da web. Se um navegador usa uma URL HTTPS visitar o seu site, comunicações WebSocket subsequentes por meio de Socket.IO serão protegidas por SSL.

    Para modificar este exemplo para habilitar esta configuração, adicione o seguinte código para o arquivo de **app.js** após a linha contendo `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Verificar configurações de Web. config**

    Aplicativos web Azure que hospedam aplicativos Node usam o arquivo **Web. config** para rotear solicitações de entrada para o aplicativo Node. WebSocket para funcionar corretamente com aplicativos de Node, o **Web. config** deve conter a seguinte entrada.

        <webSocket enabled="false"/>

    Isso desativa o módulo do IIS WebSocket, que inclui sua própria implementação do WebSocket e conflitos com módulos específicos de WebSocket Node como Socket.IO. Se esta linha não está presente ou está definida como `true`, isso pode ser o motivo pelo qual o transporte WebSocket não está funcionando para seu aplicativo.

    Normalmente, aplicativos Node não inclua um arquivo **Web. config** , portanto Azure sites gerará automaticamente um para os aplicativos de Node quando eles são implantados. Como esse arquivo é gerado automaticamente no servidor, você deve usar o FTP ou a URL de FTPS para seu site para exibir esse arquivo. Você pode encontrar o FTP e FTPS URLs para seu site no portal do clássico selecionando seu aplicativo web e, em seguida, o link **do Dashboard** . As URLs são exibidas na seção **rapidamente** .

    > [AZURE.NOTE] O arquivo **Web. config** só será gerado pelo Azure sites se seu aplicativo não fornecer um. Se você fornecer um arquivo **Web. config** na raiz do seu projeto de aplicativo, ele será usado por aplicativos de Web do Azure.

    Se a entrada não estiver presente ou estiver definida como um valor de `true`, então você deve criar um **Web. config** na raiz do seu aplicativo node e especifique um valor de `false`.  Para referência, a seguir é um padrão de **Web. config** para um aplicativo que usa **app.js** como o ponto de entrada.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Se o aplicativo usa um ponto de entrada diferente de **app.js**, você deve substituir todas as ocorrências de **app.js** com o ponto de entrada correta. Por exemplo, substituindo **app.js** com **Server. js**.

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo], onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de bate-papo hospedado em um aplicativo web Azure. Você também pode hospedar este aplicativo como um serviço de nuvem do Azure. Para obter etapas sobre como fazer isso, consulte [criar um aplicativo de bate-papo Node com Socket.IO em um serviço de nuvem do Azure].

Para obter mais informações, consulte também o [Node Developer Center].

## <a name="whats-changed"></a>O que mudou

* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure].

<!-- URL List -->

[Cache relacionada Azure]: /documentation/services/redis-cache/
[Serviço de aplicativo Web Apps]: http://go.microsoft.com/fwlink/?LinkId=529714
[Página de preços de aplicativos da Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Criar um aplicativo de bate-papo Node com Socket.IO em um serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Serviço de aplicativo do Azure e seu impacto sobre os serviços do Azure existentes]: http://go.microsoft.com/fwlink/?LinkId=529714
[Central de desenvolvedores do Node]: /develop/nodejs/
[Experimente o aplicativo de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[Afinidade de instância em Sites Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Criar um cache no Cache relacionada do Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[relacionada Socket.IO]: https://github.com/socketio/socket.io-redis
[Repositório de Socket.IO GitHub]: https://github.com/socketio/socket.io
[ZIP ou GZ arquivados lançamento]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
