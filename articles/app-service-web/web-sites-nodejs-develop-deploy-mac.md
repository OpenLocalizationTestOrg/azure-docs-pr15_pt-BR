<properties
    pageTitle="Criar um aplicativo da web de Node no serviço de aplicativo do Azure | Microsoft Azure"
    description="Saiba como implantar um aplicativo Node para um aplicativo web no serviço de aplicativo do Azure."
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
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Criar um aplicativo da web de Node no serviço de aplicativo do Azure

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [Node](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - gito](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Este tutorial mostra como criar um aplicativo [Node](http://nodejs.org) simples e implantá-lo em um [aplicativo da web](app-service-web-overview.md) em um [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) usando [gito](http://git-scm.com). As instruções neste tutorial podem ser seguidas em qualquer sistema operacional que é capaz de executar Node.

Você aprenderá:

* Como criar um aplicativo web no serviço de aplicativo do Azure usando o Portal do Azure.
* Como implantar um aplicativo Node para o aplicativo web enviando para o repositório de gito do aplicativo da web.

O aplicativo concluído grava uma cadeia de caracteres curta "Olá mundo" para o navegador.

![Um navegador exibindo a mensagem "Olá, mundo".][helloworld-completed]

Para tutoriais e código de exemplo com aplicativos de Node mais complexos, ou para outros tópicos sobre como usar Node no Azure, consulte o [Node Developer Center](/develop/nodejs/).

> [AZURE.NOTE]
> Para concluir este tutorial, você precisará de uma conta do Microsoft Azure. Se você não tiver uma conta, você pode [ativar seus benefícios de assinante do Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se para uma avaliação gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você pode criar imediatamente um aplicativo da web de curta duração starter no serviço de aplicativo — sem cartão de crédito necessários e nenhum compromissos.

## <a name="create-a-web-app-and-enable-git-publishing"></a>Criar um aplicativo web e habilitar a publicação de gito

Siga estas etapas para criar um aplicativo web no serviço de aplicativo do Azure e habilitar a publicação de gito. 

[Gito](http://git-scm.com/) é um sistema de controle de versão distribuída que você pode usar para implantar o seu site do Azure. Você vai armazenar o código que você escreve para o aplicativo da web em um repositório gito local e você vai implantar seu código para Azure enviando para um repositório remoto. Esse método de implantação é um recurso de aplicativos do serviço de aplicativo web.  

1. Entre [Portal do Azure](https://portal.azure.com).

2. Clique no ícone **+ novo** na parte superior esquerdo do Portal do Azure.

3. Clique em **Web + Mobile**e, em seguida, clique em **aplicativo Web**.

    ![][portal-quick-create]

4. Insira um nome para o aplicativo da web na caixa **Web app** .

    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo web será {name}. azurewebsites.net. Se o nome inserido não exclusivo, um ponto de exclamação vermelho aparece na caixa de texto.

5. Selecione uma **assinatura**.

6. Selecione um **Grupo de recursos** ou crie um novo.

    Para obter mais informações sobre os grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

7. Selecione um **Local da plano de serviço de aplicativo** ou crie um novo.

    Para obter mais informações sobre planos de serviço de aplicativo, consulte [Visão geral de planos de serviço de aplicativo do Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

8. Clique em **criar**.
   
    ![][portal-quick-create2]

    Em um pouco tempo, geralmente menores do que um minuto, Azure termina de criar o novo aplicativo web.

9. Clique em **aplicativos Web > {seu novo aplicativo web}**.

    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

10. Na lâmina **Web app** , clique na parte de **implantação** .

    ![][deployment-part]

11. Na lâmina **Implantação contínuo** , clique em **Escolher a fonte**

12. Clique em **Repositório gito Local**e clique em **Okey**.

    ![][setup-git-publishing]

13. Configure credenciais de implantação se você ainda não fez isso.

    a. Na lâmina Web app, clique em **Configurações > credenciais de implantação**.

    ![][deployment-credentials]
 
    b. Crie um nome de usuário e senha. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

14. Na lâmina Web app, clique em **configurações**e, em seguida, clique em **Propriedades**.
 
    Para publicar, você vai enviar para um repositório gito remoto. A URL para o repositório está listada em **GITO URL**. Você usará essa URL posteriormente no tutorial.

    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Criar e testar seu aplicativo localmente

Nesta seção, você vai criar um arquivo de **Server. js** que contém uma versão ligeiramente modificada do exemplo 'Hello World' de [nodejs.org]. O código adiciona process.env.PORT como a porta para escutar em durante a execução em um aplicativo web Azure.

1. Crie um diretório chamado *helloworld*.

2. Use um editor de texto para criar um novo arquivo chamado **Server. js** no diretório *helloworld* .

2. Copie o seguinte código para o arquivo **Server. js** e salve o arquivo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abrir a linha de comando e use o seguinte comando para iniciar o aplicativo web localmente.

        node server.js

4. Abra seu navegador da web e navegue até http://localhost:1337. 

    Uma página da Web que exibe "Olá, mundo" é exibida, conforme mostrado na seguinte captura de tela.

    ![Um navegador exibindo a mensagem "Olá, mundo".][helloworld-localhost]

## <a name="publish-your-application"></a>Publicar seu aplicativo

1. Instale gito se você ainda não fez isso.

    Para obter instruções de instalação para sua plataforma, consulte a [página de download de gito](http://git-scm.com/download).

1. Na linha de comando, altere diretórios para o diretório de **helloworld** e insira o seguinte comando para inicializar um repositório gito local.

        git init


2. Use os comandos a seguir para adicionar arquivos ao repositório:

        git add .
        git commit -m "initial commit"

3. Adicione um gito remoto para enviar as atualizações para o aplicativo da web que você criou anteriormente, usando o seguinte comando:

        git remote add azure [URL for remote repository]

4. Enviar as suas alterações no Azure usando o seguinte comando:

        git push azure master

    Você será solicitado a senha que você criou anteriormente. A saída é semelhante ao seguinte exemplo.

        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master

5. Para exibir seu aplicativo, clique no botão **Procurar** na parte **Web App** no portal do Azure.

    ![Botão Procurar](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

    ![Olá no Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Publicar alterações em seu aplicativo

1. Abra o arquivo **Server. js** em um editor de texto e alterar 'Hello World\n' para 'Hello Azure\n'. 

2. Salve o arquivo.

2. Na linha de comando, altere diretórios para o diretório de **helloworld** e execute os seguintes comandos:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Você será solicitado a senha novamente.

3. Atualize a janela do navegador que você navega-se a URL do aplicativo da web.

    ![Uma página da web exibindo 'Hello Azure'][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Reverter uma implantação

A lâmina **Web app** você pode clicar em **Configurações > implantação contínuo** para ver o histórico de implantação na lâmina **implantações** . Se você precisar reverter para uma implantação anterior, você poderá selecioná-la e clique em **reimplantar** na lâmina **Detalhes de implantação** .

## <a name="next-steps"></a>Próximas etapas

Implantar um aplicativo Node para um aplicativo web no serviço de aplicativo do Azure. Para saber mais sobre como o serviço de aplicativo web apps executados Node aplicativos, consulte [Azure aplicativo de serviço Web Apps: Node](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) e [especificando uma versão Node em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md).

Node fornece um rico ecossistema de módulos que podem ser usados por seus aplicativos. Para saber como Web Apps funciona com módulos, consulte [usando Node módulos com aplicativos do Azure](../nodejs-use-node-modules-azure-apps.md).

Se você encontrar problemas com seu aplicativo após ele ter sido implantado no Azure, veja [como depurar um aplicativo Node no serviço de aplicativo do Azure](web-sites-nodejs-debug.md) para obter informações sobre diagnosticar o problema.

Este artigo usa o Portal do Azure para criar um aplicativo web. Você também pode usar a [Interface de linha de comando do Azure](../xplat-cli-install.md) ou [Azure PowerShell](../powershell-install-configure.md) para realizar as mesmas operações.

Para obter mais informações sobre como desenvolver aplicativos Node no Azure, consulte o [Node Developer Center](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
