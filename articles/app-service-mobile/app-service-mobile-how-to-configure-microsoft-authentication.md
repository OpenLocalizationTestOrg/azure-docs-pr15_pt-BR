<properties
    pageTitle="Como configurar a autenticação de Account da Microsoft para seu aplicativo de serviços de aplicativo"
    description="Saiba como configurar a autenticação de Account da Microsoft para seu aplicativo de serviços de aplicativo."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar seu aplicativo de serviço de aplicativo para usar o logon de Account da Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicativo do Azure para usar o Microsoft Account como um provedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registrar seu aplicativo com a conta da Microsoft

1. Faça logon no [portal do Azure]e navegue até seu aplicativo. Copie a **URL**, que posteriormente você usa para configurar seu aplicativo com Account da Microsoft.

2. Navegue para a página [Meus aplicativos] no Microsoft Account Developer Center e faça logon com sua conta da Microsoft, se necessário.

3. Clique em **Adicionar um aplicativo**, e em seguida, digite um nome de aplicativo e clique em **criar um aplicativo**.

4. Anote a **ID do aplicativo**, como você precisará dele mais tarde. 

5. Em "Plataformas", clique em **Adicionar plataforma** e selecione "Web".

6. Em "Redirecionar URIs" fornecer o ponto de extremidade do aplicativo, clique em **Salvar**. 
 
    >[AZURE.NOTE]O URI de redirecionamento é a URL do seu aplicativo acrescentado com o caminho, _/.auth/login/microsoftaccount/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Certifique-se de que você está usando o esquema de HTTPS.

7. Em "Segredos de aplicativo", clique em **Gerar nova senha**. Anote o valor que aparece. Depois que você sair da página, ele não será exibido novamente.


    > [AZURE.IMPORTANT] A senha é uma credencial de segurança importantes. Não compartilhar a senha com qualquer pessoa ou distribuí-lo em um aplicativo cliente.

## <a name="secrets"> </a>Informações de conta da Microsoft adicionar ao seu aplicativo de serviço de aplicativo

1. No [portal do Azure], navegue até seu aplicativo, clique em **configurações**de volta > **autenticação / autorização**.

2. Se a autenticação / autorização recurso não está habilitado, **ligue-a**.

3. Clique na **Conta da Microsoft**. Cole os valores de ID de aplicativo e a senha que você obteve anteriormente e opcionalmente ativar quaisquer escopos que seu aplicativo requer. Clique em **Okey**.

    ![][1]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

4. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados por conta da Microsoft, defina **fazer quando solicitação não é autenticada** a **Conta da Microsoft**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas a conta da Microsoft para autenticação.

5. Clique em **Salvar**.

Agora você está pronto para usar Account da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
