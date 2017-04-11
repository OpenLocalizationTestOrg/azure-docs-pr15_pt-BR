<properties
    pageTitle="Introdução ao autenticação para aplicativos Mobile no iOS Xamarin"
    description="Aprenda a usar os aplicativos móveis para autenticar os usuários do aplicativo iOS Xamarin por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Adicione autenticação ao seu aplicativo de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar os usuários de um aplicativo de celular de serviço de aplicativo do seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação para o projeto de início rápido do Xamarin.iOS usando um provedor de identidade que é suportado pelo serviço de aplicativo. Após com êxito sendo autenticado e autorizado pelo seu aplicativo móvel, o valor de ID de usuário é exibido e você poderá acessar os dados de tabela restrito.

Primeiro você deve concluir o tutorial [criar um aplicativo de Xamarin.iOS]. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para autenticação e configurar serviços de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. no Visual Studio ou Xamarin Studio, execute o projeto de cliente em um dispositivo ou emulador. Verifique se que uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. A falha é registrada no console do depurador. Portanto, no Visual Studio, você verá a falha na janela de saída.

&nbsp;&nbsp;Essa falha não autorizada acontece porque o aplicativo tenta acessar seu back-end do aplicativo Mobile como um usuário não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, você irá atualizar o aplicativo cliente para os recursos de solicitação de aplicativo Mobile back-end com um usuário autenticado.

##<a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo

Nesta seção, você irá modificar o aplicativo para exibir uma tela de login antes de exibir dados. Quando o aplicativo for iniciado, ele não não se conectará ao seu serviço de aplicativo e não serão exibidos todos os dados. Após a primeira vez que o usuário executa o gesto de atualização, a tela de login aparecerá; Após login bem-sucedido na lista de itens todo será exibida.

1. No projeto do cliente, abra o arquivo **QSTodoService.cs** e adicione o seguinte usando instrução e `MobileServiceUser` com acesso à classe QSTodoService:

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Adicione novo método chamado **autenticar** para **QSTodoService** com a seguinte definição:


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Se você estiver usando um provedor de identidade diferente de um Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modificar a definição de método de **ViewDidLoad** removendo a chamada para **RefreshAsync()** próximo ao final:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modifique o método **RefreshAsync** para autenticar se a propriedade de **usuário** é nula. Adicione o seguinte código na parte superior da definição do método:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. No Visual Studio ou Xamarin Studio conectado ao seu Xamarin construir Host no seu Mac, execute o projeto de cliente direcionado um dispositivo ou emulador. Verifique se o aplicativo não exibe nenhum dado.

    Execute o gesto de atualização, puxe para baixo na lista de itens, o que fará com que a tela de logon apareça. Depois que você inseriu credenciais válidas com êxito, o aplicativo exibirá a lista de itens todo e você pode fazer atualizações para os dados.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar um aplicativo de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
