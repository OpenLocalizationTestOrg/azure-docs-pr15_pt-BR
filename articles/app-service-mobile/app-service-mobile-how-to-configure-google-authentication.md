<properties
    pageTitle="Como configurar a autenticação do Google para o seu aplicativo de serviços de aplicativo"
    description="Saiba como configurar a autenticação do Google para o seu aplicativo de serviços de aplicativo."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Como configurar seu aplicativo de serviço de aplicativo para usar o logon do Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicativo do Azure para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google que tem um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrar seu aplicativo com Google

1. Faça logon no [portal do Azure]e navegue até seu aplicativo. Copie a **URL**, que você usar posteriormente para configurar seu aplicativo do Google.

2. Navegue até o site de [apis do Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , entrar com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **nome de projeto**, clique em **criar**.

3. Em **APIs Social** clique **Google + API** e, em seguida, **Habilitar**.

4. No painel esquerdo, **credenciais** > **OAuth consentimento tela**, selecione seu **endereço de Email**, insira um **Nome de produto**e clique em **Salvar**.

5. Na guia **credenciais** , clique em **criar credenciais** > **OAuth ID do cliente**, em seguida, selecione **aplicativo Web**.

6. Colar a **URL** de serviço de aplicativo que você copiou anteriormente **Autorizados origens de JavaScript**e, em seguida, cole o URI de redirecionamento **Autorizados URI redirecionar**. O redirecionamento URI é a URL do seu aplicativo acrescentado com o caminho, _/.auth/login/google/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Certifique-se de que você está usando o esquema de HTTPS. Clique em **criar**.

7. Na tela seguinte, tome nota dos valores da ID do cliente e segredo cliente.


    > [AZURE.IMPORTANT]
    O segredo de cliente é uma credencial de segurança importantes. Não compartilhar esse segredo com qualquer pessoa ou distribuí-lo em um aplicativo cliente.


## <a name="secrets"> </a>Google adicionar informações ao seu aplicativo

8. Volta do [portal do Azure], navegue até seu aplicativo. Clique em **configurações**e então **autenticação / autorização**.

9. Se a autenticação / autorização recurso não está habilitado, ative a opção para **em**.

10. Clique em **Google**. Cole os valores de ID de aplicativo e segredo de aplicativo que você obteve anteriormente e opcionalmente ativar quaisquer escopos que seu aplicativo requer. Clique em **Okey**.

    ![][1]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

17. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados pelo Google, defina **fazer quando solicitação não é autenticada** como **Google**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas para o Google para autenticação.

12. Clique em **Salvar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal do Azure]: https://portal.azure.com/

