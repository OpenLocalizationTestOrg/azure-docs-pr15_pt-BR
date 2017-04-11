<properties
    pageTitle="Como configurar a autenticação do Facebook para seu aplicativo de serviços de aplicativo"
    description="Saiba como configurar a autenticação do Facebook para seu aplicativo de serviços de aplicativo."
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

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar seu aplicativo de serviço de aplicativo para usar o logon do Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicativo do Azure para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook que possui um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, acesse [facebook.com].

## <a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Faça logon no [portal do Azure]e navegue até seu aplicativo. Copie a **URL**. Você usará isso para configurar seu aplicativo do Facebook.

2. Em outra janela do navegador, navegue até o site de [Desenvolvedores do Facebook] e entrar com suas credenciais de conta do Facebook.

3. (Opcional) Se você já não ter registrado, clique em **aplicativos** > **registrar como um desenvolvedor**, em seguida, aceite a política e siga as etapas de registro.

4. Clique em **Meus aplicativos** > **Adicionar um novo aplicativo** > **site** > **ignore e criar ID de aplicativo**. 

5. Em **Nome para exibição**, digite um nome exclusivo para o aplicativo, digite seu **Email de contato**, escolha uma **categoria** para seu aplicativo, e em seguida, clique em **Criar ID de aplicativo** e concluir a verificação de segurança. Isso o leva para o painel de controle do desenvolvedor para seu novo aplicativo do Facebook.

6. Em "Logon do Facebook," clique em **Começar**. Adicione seu aplicativo **Redirecionar URI** **válido OAuth**redirecionar URIs, depois clique em **Salvar alterações**. 

    > [AZURE.NOTE] O URI de redirecionamento é a URL do seu aplicativo acrescentado com o caminho, _/.auth/login/facebook/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que você está usando o esquema de HTTPS.

6. No painel de navegação esquerdo, clique em **configurações**. No campo **Segredo do aplicativo** , clique em **Mostrar**, forneça sua senha se solicitado, tome nota dos valores de **ID de aplicativo** e **Segredo do aplicativo**. Use esses posteriormente para configurar seu aplicativo no Azure.

    > [AZURE.IMPORTANT] O segredo de aplicativo é uma credencial de segurança importantes. Não compartilhar esse segredo com qualquer pessoa ou distribuí-lo em um aplicativo cliente.

7. Conta do Facebook que foi usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, somente os administradores podem entrar neste aplicativo. Para autenticar outras contas do Facebook, clique em **Revisão de aplicativo** e habilitar **< your--nome do aplicativo > tornar público** habilitar o acesso público geral usando autenticação do Facebook.

## <a name="secrets"> </a>Informações do Facebook adicionar ao seu aplicativo

1. Volta do [portal do Azure], navegue até seu aplicativo. Clique em **configurações** > **autenticação / autorização**e certifique-se de que a **Autenticação do serviço de aplicativo** está **ativado**.

2. Clique em **Facebook**, cole os valores de ID de aplicativo e segredo de aplicativo que você obteve anteriormente, opcionalmente, habilite quaisquer escopos necessitados para seu aplicativo, clique **Okey**.

    ![][0]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

3. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados pelo Facebook, defina **fazer quando solicitação não está autenticada** com o **Facebook**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas para o Facebook para autenticação.

4. Ao terminar a configuração de autenticação, clique em **Salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/
