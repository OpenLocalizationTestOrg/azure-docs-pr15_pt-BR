<properties
    pageTitle="Como configurar a autenticação do Twitter para seu aplicativo de serviços de aplicativo"
    description="Saiba como configurar a autenticação do Twitter para seu aplicativo de serviços de aplicativo."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar seu aplicativo de serviço de aplicativo para usar o logon do Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicativo do Azure para usar Twitter como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Twitter que possui um número de telefone e endereço de email verificado. Para criar uma nova conta do Twitter, acesse <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrar seu aplicativo com Twitter


1. Faça logon no [portal do Azure]e navegue até seu aplicativo. Copie a **URL**. Você usará isso para configurar seu aplicativo do Twitter.

2. Navegue até o site de [Desenvolvedores do Twitter] , entre com suas credenciais de conta do Twitter e clique em **Criar novo aplicativo**.

3. Digite o **nome** e uma **Descrição** para seu novo aplicativo. Cole **URL** para o valor de **site do seu aplicativo** . Em seguida, para a **URL de retorno de chamada**, cole a **URL de retorno de chamada** que você copiou anteriormente. Este é o gateway de aplicativo Mobile acrescentado com o caminho, _/.auth/login/twitter/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que você está usando o esquema de HTTPS.

3.  Na parte inferior, a página, leia e aceite os termos. Clique em **criar seu aplicativo do Twitter**. Isso registra o aplicativo exibe os detalhes do aplicativo.

4. Clique na guia **configurações** , marque **Permitir que este aplicativo a ser usado para entrar com Twitter**e clique em **Configurações de atualização**.

5. Selecione a guia de **chaves e Tokens de acesso** . Tome nota dos valores da **Chave do consumidor (chave de API)** e **segredo do consumidor (API secreta)**.

    > [AZURE.NOTE] O segredo do consumidor é uma credencial de segurança importantes. Não compartilhar esse segredo com qualquer pessoa ou distribuí-lo com seu aplicativo.


## <a name="secrets"> </a>Adicionar Twitter informações de seu aplicativo

13. Volta do [portal do Azure], navegue até seu aplicativo. Clique em **configurações**e então **autenticação / autorização**.

14. Se a autenticação / autorização recurso não está habilitado, ative a opção para **em**.

15. Clique em **Twitter**. Cole o ID de aplicativo e o aplicativo segredo valores que você obteve anteriormente. Clique em **Okey**.

    ![][1]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

17. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados pelo Twitter, defina **fazer quando solicitação não é autenticada** como **Twitter**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas para Twitter para autenticação.

17. Clique em **Salvar**.

Agora você está pronto para usar Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
