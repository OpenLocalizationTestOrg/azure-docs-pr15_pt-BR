<properties
    pageTitle="Introdução ao Node web apps no serviço de aplicativo do Azure"
    description="Saiba como implantar um aplicativo Node para um aplicativo web no serviço de aplicativo do Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introdução ao Node web apps no serviço de aplicativo do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar um aplicativo [Node] simples e implantá-lo para [O serviço de aplicativo do Azure] de um ambiente de linha de comando, como cmd.exe ou bash. As instruções neste tutorial podem ser seguidas em qualquer sistema operacional que podem ser executados Node.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Pré-requisitos

- [Node]
- [Bower]
- [Yeoman]
- [Gito]
- [CLI Azure]
- Uma conta do Microsoft Azure. Se você não tiver uma conta, você pode [inscrever-se para uma avaliação gratuita] ou [ativar seus benefícios de assinante do Visual Studio].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Criar e implantar um aplicativo de web Node simple

1. Abra o terminal de linha de comando de sua escolha e instale o [Express gerador para Yeoman].

        npm install -g generator-express

2. `CD`para uma pasta de trabalho e gerar um aplicativo express usando a seguinte sintaxe:

        yo express
        
    Escolha as opções a seguir quando solicitado:  

    `? Would you like to create a new directory for your project?`**Sim**  
    `? Enter directory name`**{appname}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Nenhum**  
    `? Select a database to use:`**Nenhum**  
    `? Select a build tool to use:`**Pesado**

3. `CD`para o diretório raiz do seu novo aplicativo e iniciá-lo para certificar-se de que ele é executado em seu ambiente de desenvolvimento:

        npm start

    No seu navegador, navegue até <http://localhost:3000> para certificar-se de que você pode ver a home page do Express. Depois de confirmar a execução do seu aplicativo corretamente, use `Ctrl-C` para interrompê-lo.
    
1. Mude para o modo de ASM e efetuar login no Azure (necessário [Azure CLI](#prereq)):

        azure config mode asm
        azure login

    Siga os prompts para continuar o logon em um navegador com uma conta da Microsoft com sua assinatura do Azure.

2. Verifique se você ainda está na pasta raiz do seu aplicativo, em seguida, cria o recurso de aplicativo de serviço de aplicativo no Azure com um nome de aplicativo exclusiva com o próximo comando. Por exemplo: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Siga os prompts para selecionar uma região Azure para implantar. Se você nunca configurar credenciais de implantação gito/FTP para sua assinatura do Azure, você também será solicitado para criá-los.

3. Abra o arquivo de./config/config.js na raiz do seu aplicativo e alterar a porta de produção para `process.env.port`; seu `production` propriedade na `config` objeto deve parecer com o exemplo a seguir:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Isso permite que seu aplicativo Node responder para solicitações a porta padrão da web que iisnode ouve.
    
4. Abra./package.json e adicione o `engines` propriedade para [especificar a versão Node desejada](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Salvar suas alterações e, em seguida, use gito para implantar o seu aplicativo no Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Gerador de Express já oferece um arquivo de .gitignore, portanto sua `git push` não consumir largura de banda tentando carregar o node_modules / diretório.

5. Por fim, inicie seu aplicativo do Azure ao vivo no navegador:

        azure site browse

    Agora você deve ver seu aplicativo web do Node executando ao vivo no serviço de aplicativo do Azure.
    
    ![Exemplo de navegar para o aplicativo implantado.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Atualizar o aplicativo web do Node

Para fazer atualizações em seu aplicativo web do Node em execução em um serviço de aplicativo, basta executar `git add`, `git commit`, e `git push` como feito quando você primeiro implantado seu aplicativo web.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Como o serviço de aplicativo implanta o seu aplicativo Node

Serviço de aplicativo do Azure usa [iisnode] para executar aplicativos Node. A CLI do Azure e o mecanismo de Kudu (implantação de gito) trabalham juntos para dar uma experiência simplificada quando você desenvolver e implantar aplicativos Node da linha de comando. 

- `azure site create --git`reconhece padrão Node comuns Server. js ou app.js e cria um iisnode.yml no seu diretório raiz. Você pode usar este arquivo para personalizar iisnode.
- Em `git push azure master`, Kudu automatiza as seguintes tarefas de implantação:

    - Se package.json na raiz do repositório, execute `npm install --production`.
    - Gere um Web. config para iisnode que aponta para o script iniciar no package.json (por exemplo, Server. js ou app.js).
    - Personalize Web. config para preparar seu aplicativo para depuração com Inspetor de nó.
    
## <a name="use-a-nodejs-framework"></a>Usar uma estrutura de Node

Se você usar uma estrutura de Node popular, como o [Sails.js] [ SAILSJS] ou [MEAN.js] [ MEANJS] para desenvolver aplicativos, você pode implantar os aplicativos de serviço. Estruturas de Node populares têm seus quirks específicas e suas dependências de pacote mantém obtendo atualizadas. No entanto, o serviço de aplicativo disponibiliza os logs stdout e stderr para você, para que possa souber exatamente o que está acontecendo com seu aplicativo e fazer alterações de acordo. Para obter mais informações, consulte [obter os logs de stdout e stderr de iisnode](#iisnodelog).

Os seguintes tutoriais mostrará como trabalhar com uma estrutura específica no aplicativo de serviço:

- [Implantar um aplicativo de web Sails.js serviço de aplicativo do Azure]
- [Criar um aplicativo de bate-papo Node com Socket.IO no serviço de aplicativo do Azure]
- [Como usar io.js com aplicativos do Azure aplicativo de serviço Web]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Use um mecanismo de Node específico

Em seu fluxo de trabalho típico, você informa ao serviço de aplicativo para usar um mecanismo de Node específico como você faria normalmente em package.json.
Por exemplo:

    "engines": {
        "node": "6.6.0"
    }, 

O mecanismo de implantação de Kudu determina qual mecanismo Node usar na seguinte ordem:

- Primeiro, examine iisnode.yml para ver se `nodeProcessCommandLine` especificado. Se Sim, em seguida, use.
- Em seguida, examine a package.json para ver se `"node": "..."` especificado no `engines` objeto. Se Sim, em seguida, use.
- Escolha uma versão de Node padrão por padrão.

>[AZURE.NOTE] É recomendável que você definir explicitamente o mecanismo de Node desejado. A versão de Node padrão pode alterar, e você pode receber erros em seu aplicativo web Azure porque a versão de Node padrão não é adequada para seu aplicativo.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obter stdout e stderr logs de iisnode

Para ler logs de iisnode, siga estas etapas.

> [AZURE.NOTE] Após concluir essas etapas, os arquivos de log podem não existir até que ocorre um erro.

1. Abra o arquivo de iisnode.yml que fornece a CLI do Azure.

2. Defina os dois parâmetros a seguir: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Juntas, elas informam iisnode no serviço de aplicativo para colocar sua saída stdout e stderror na D:\home\site\wwwroot\**iisnode** diretório.

3. Salvar suas alterações e, em seguida, enviar as suas alterações no Azure com os seguintes comandos gito:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode agora está configurado. As próximas etapas mostram como acessar esses logs.
     
4. No seu navegador, acesse o console de depuração de Kudu para o aplicativo, que está no:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Essa URL difere a URL do aplicativo web pela adição de "*.scm.*" para o nome DNS. Se você omitir esse acréscimo à URL, você obterá um erro 404.

5. Navegue até D:\home\site\wwwroot\iisnode

    ![Navegando até o local dos arquivos de log iisnode.][iislog-kudu-console-find]

6. Clique no ícone **Editar** para o registro que você deseja ler. Você também pode clicar em **Baixar** ou **Excluir** se desejar.

    ![Abrindo um arquivo de log iisnode.][iislog-kudu-console-open]

    Agora você pode ver o log para ajudá-lo a depurar sua implantação do serviço de aplicativo.
    
    ![Examinando um arquivo de log iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Depurar seu aplicativo com o Inspetor de nó

Se você usar o Inspetor de nó depurar seus aplicativos Node, você pode usá-lo para o aplicativo de serviço de aplicativo ao vivo. Inspetor de nó é pré-instalado na instalação iisnode do aplicativo de serviço. E se você implantar através de gito, o Web. config gerado automaticamente do Kudu já contém todos os a configuração que você precisa habilitar o Inspetor de nó.

Para ativar o Inspetor de nó, siga estas etapas:

1. Abra iisnode.yml na raiz do seu repositório e especificar os parâmetros a seguir: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Salvar suas alterações e, em seguida, enviar as suas alterações no Azure com os seguintes comandos gito:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Agora, basta navegue até início arquivo do seu aplicativo conforme especificado pelo script iniciar em seu package.json, com /debug adicionado à URL. Por exemplo,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Mais recursos

- [Especificando uma versão Node em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md)
- [Guia de solução de problemas para aplicativos de Node no Azure e práticas recomendadas](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Como depurar um aplicativo da web Node no serviço de aplicativo do Azure](web-sites-nodejs-debug.md)
- [Usando o Node módulos com aplicativos do Azure](../nodejs-use-node-modules-azure-apps.md)
- [Aplicativos do Azure serviço de aplicativo Web: Node](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Central de desenvolvedores do Node](/develop/nodejs/)
- [Introdução ao web apps no serviço de aplicativo do Azure](app-service-web-get-started.md)
- [Explorando o Console de depuração Kudu Super secreto]

<!-- URL List -->

[CLI Azure]: ../xplat-cli-install.md
[Serviço de aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[ativar os benefícios de assinante do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Criar um aplicativo de bate-papo Node com Socket.IO no serviço de aplicativo do Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implantar um aplicativo de web Sails.js serviço de aplicativo do Azure]: ./app-service-web-nodejs-sails.md
[Explorando o Console de depuração Kudu Super secreto]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Gerador de Express para Yeoman]: https://github.com/petecoop/generator-express
[Gito]: http://www.git-scm.com/downloads
[Como usar io.js com aplicativos do Azure aplicativo de serviço Web]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[Inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
