<properties 
    pageTitle="Construir e implantar um aplicativo de web Node para Azure usando o WebMatrix" 
    description="Um tutorial que ensina como usar WebMatrix para desenvolver um aplicativo node e implantá-lo para aplicativos do Azure aplicativo de serviço Web." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Construir e implantar um aplicativo de web Node para Azure usando o WebMatrix

Este tutorial mostra como usar WebMatrix para desenvolver um aplicativo node e implantá-lo para [O serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. O WebMatrix é uma ferramenta de desenvolvimento web gratuito da Microsoft que inclui tudo o que você precisa para desenvolvimento de aplicativo web ou site. WebMatrix inclui vários recursos que tornam mais fácil de usar Node, incluindo a conclusão de código, modelos pré-criados e suporte de editor para Jade, menos e CoffeeScript. Saiba mais sobre [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Após concluir este guia, você terá um aplicativo web do Node em execução no serviço de aplicativo do Azure.
 
Uma captura de tela da aplicação concluída está abaixo:

![Site da Web de nó Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="sign-into-azure"></a>Entrar no Azure

Siga estas etapas para criar um aplicativo web no serviço de aplicativo do Azure.

1. Iniciar o WebMatrix
2. Se esta for a primeira vez que você usou WebMatrix, você será solicitado a entrar no Azure.  Caso contrário, você pode clicar no botão **Entrar** e escolha **Adicionar conta**.  Selecione **entrar** usando sua Account da Microsoft.

    ![Adicionar conta][addaccount]

3. Se você se inscreveu para uma conta do Azure, você pode efetuar login usando sua Account da Microsoft:

    ![Entrar no Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Criar um site usando um modelo interno do Azure

1. Na tela inicial, clique no botão **novo** e escolha a **Galeria de modelos** para criar um novo site da Galeria de modelos:

    ![Novo site da Galeria de modelos][sitefromtemplate]

2. Na caixa de diálogo do **Site do modelo** , selecione o **nó** e selecione **Site Express**. Por fim, clique em **Avançar**. Se estão faltando todos os pré-requisitos para o modelo de **Site Express** , você será solicitado a instalá-los.

    ![Selecione modelo express][webmatrix-templates]

3. Se você estiver conectado ao Azure, agora você tem a opção para criar um aplicativo de serviço web app para seu site local.  Escolha um nome exclusivo e selecione Data Center onde quer que o aplicativo web do serviço de aplicativo seja criado: 

    ![Criar o site no Azure][nodesitefromtemplateazure]
    
4. Após o WebMatrix concluir a criação de site local e criar o aplicativo de serviço web app, o IDE WebMatrix é exibido.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publicar seu aplicativo no Azure

1. No WebMatrix, clique em **Publicar** na faixa de opções **Home** para exibir a caixa de diálogo **Publicar visualização** do site.

    ![Publicar visualização][webmatrix-node-publishpreview]

2. Clique em **continuar**. Quando a publicação estiver concluída, a URL para o serviço de aplicativo web app é exibida na parte inferior do IDE WebMatrix

    ![Publicar concluída][webmatrix-publish-complete]

3. Clique no link para abrir o aplicativo de serviço web app no seu navegador.

    ![Express web app][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modificar e republicar seu aplicativo

Você pode facilmente modificar e republique o aplicativo. Aqui, você criará um simples alterar para o título no arquivo **Jade** e republicar o aplicativo.

1. No WebMatrix, selecione **arquivos**e, em seguida, expanda a pasta de **modos de exibição** . Abra o arquivo **Jade** clicando duas vezes nele.

    ![WebMatrix exibindo Jade][webmatrix-modify-index]

2. Altere a linha do parágrafo ao seguinte:

        p Welcome to #{title} with WebMatrix on Azure!

3. Salve suas alterações e clique no ícone de publicar. Finalmente, clique em **continuar** na caixa de diálogo **Publicar visualização** e aguarde a atualização a ser publicado.

    ![Publicar visualização][webmatrix-republish]

4. Quando a publicação estiver concluída, use o link retornado quando o processo de publicação é concluído para ver o aplicativo de serviço web app atualizado.

    ![Nó Azure web app][webmatrix-node-completed]

##<a name="next-steps"></a>Próximas etapas

Para saber mais sobre as versões do Node que são fornecidas com o Azure e como especificar a versão a ser usado com o seu aplicativo, consulte [especificando uma versão Node em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md).

Se você encontrar problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [como depurar um aplicativo da web Node no serviço de aplicativo do Azure](web-sites-nodejs-debug.md) para obter informações sobre diagnosticar o problema.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 