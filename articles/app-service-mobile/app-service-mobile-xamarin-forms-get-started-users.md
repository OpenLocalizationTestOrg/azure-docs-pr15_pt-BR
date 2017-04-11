<properties
    pageTitle="Introdução ao autenticação para aplicativos móvel no aplicativo de Xamarin.Forms | Microsoft Azure"
    description="Aprenda a usar os aplicativos móveis para autenticar os usuários de seu aplicativo de formulários de Xamarin por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Adicione autenticação ao seu aplicativo de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Visão geral

Este tópico mostra como autenticar os usuários de um aplicativo de celular de serviço de aplicativo do seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação para o projeto de início rápido do Xamarin.Forms usando um provedor de identidade que é suportado pelo serviço de aplicativo. Após com êxito sendo autenticado e autorizado pelo seu aplicativo móvel, o valor de ID de usuário é exibido e você poderá acessar os dados de tabela restrito.

##<a name="prerequisites"></a>Pré-requisitos

Para o melhor resultado com este tutorial, recomendamos que você primeiro conclua o tutorial de [criar um aplicativo de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) . Depois de concluir este tutorial, você terá um projeto de Xamarin.Forms que é um aplicativo de lista de tarefas pendentes de várias plataformas.

Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para autenticação e configurar serviços de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação para a biblioteca de classes portátil

Aplicativos Mobile usa o método de extensão [LoginAsync] na [MobileServiceClient] para um usuário com a autenticação do serviço de aplicativo entrar. Este exemplo utiliza um fluxo de autenticação de servidor gerenciado que exibe a interface do provedor de entrar no aplicativo. Para obter mais informações, consulte [autenticação de servidor gerenciado](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para fornecer uma melhor experiência de usuário em seu aplicativo de produção, você pode considerar em vez disso, usando a [autenticação do cliente gerenciado](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Para autenticar com um projeto de Xamarin.Forms, você pode definir uma interface **IAuthenticate** na biblioteca de classes portátil para o aplicativo. Você também atualizar a interface de usuário definida na biblioteca de classes portátil para adicionar um botão **entrar** , que o usuário clica para iniciar a autenticação. Após a autenticação bem-sucedida, os dados são carregados do aplicativo móvel back-end.

Você deve implementar a interface **IAuthenticate** para cada plataforma suportada pelo seu aplicativo.


1. No Visual Studio ou Xamarin Studio, abrir App.cs do projeto com **portátil** no nome, que é o projeto de biblioteca de classes portátil, em seguida, adicione o seguinte `using` instrução:

        using System.Threading.Tasks;

2. Em App.cs, adicione o seguinte `IAuthenticate` definição de interface imediatamente antes do `App` definição da classe.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Adicione os seguintes membros estáticos à classe **App** inicializar a interface com uma implementação específica de plataforma.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml do projeto biblioteca de classes portátil, adicione o seguinte elemento de **botão** no elemento layout *buttonsPanel* , após o botão existente: 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Esse botão aciona autenticação de servidor gerenciado com seu back-end do aplicativo móvel.

5. Abra TodoList.xaml.cs do projeto de biblioteca de classes portátil e adicione o campo a seguir para o `TodoList` classe:

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Substitua o método **OnAppearing** com o seguinte código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Isso garante que os dados somente sejam atualizados do serviço após o usuário foi autenticado.

7. Adicione o seguinte manipulador para o evento **Clicked** para a classe de **lista de tarefas** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Salve suas alterações e reconstruir o projeto de biblioteca de classes portátil verificando sem erros.


##<a name="add-authentication-to-the-android-app"></a>Adicionar autenticação ao aplicativo do Android

Esta seção mostra como implementar a interface **IAuthenticate** do projeto de aplicativo do Android. Ignore esta seção se você não oferece suporte a dispositivos Android.

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto **droid** , **Definir como projeto de inicialização**.

2. Pressione F5 para iniciar o projeto no depurador, em seguida, verifique se que uma exceção não tratada com um código de status de 401 (não autorizado) é gerada após o aplicativo é iniciado. Isso acontece porque o acesso no back-end é restrito apenas a usuários autorizados.

3. Abrir MainActivity.cs no projeto Android e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Atualize a classe de **MainActivity** para implementar a interface **IAuthenticate** , da seguinte maneira:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Atualize a classe de **MainActivity** adicionando um campo de **MobileServiceUser** e um método de **autenticação** , que é requerido pela interface **IAuthenticate** , da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Se você estiver usando um provedor de identidade diferente de Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione o seguinte código para o método **OnCreate** da classe **MainActivity** antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Isso garante que o autenticador seja inicializado antes das cargas de aplicativo.

7. Recriar o aplicativo, executá-lo e entrar com o provedor de autenticação você escolheu e verifique se que é possível acessar os dados como um usuário autenticado.

##<a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação ao aplicativo do iOS

Esta seção mostra como implementar a interface **IAuthenticate** do projeto de aplicativo do iOS. Se você não oferece suporte a dispositivos iOS, ignore esta seção.

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto do **iOS** , **Definir como projeto de inicialização**.

2. Pressione F5 para iniciar o projeto no depurador, em seguida, verifique se que uma exceção não tratada com um código de status de 401 (não autorizado) é gerada após o aplicativo é iniciado. Isso acontece porque o acesso no back-end é restrito apenas a usuários autorizados.

4. Abrir AppDelegate.cs no projeto iOS e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Atualize a classe de **AppDelegate** para implementar a interface **IAuthenticate** , da seguinte maneira:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Atualize a classe de **AppDelegate** adicionando um campo de **MobileServiceUser** e um método de **autenticação** , que é requerido pela interface **IAuthenticate** , da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Se você estiver usando um provedor de identidade diferente de Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione a seguinte linha de código para o método de **FinishedLaunching** antes da chamada para `LoadApplication()`: 

        App.Init(this);

    Isso garante que o autenticador é inicializado antes que o aplicativo seja carregado.

7. Recriar o aplicativo, executá-lo e entrar com o provedor de autenticação você escolheu e verifique se que é possível acessar os dados como um usuário autenticado.


##<a name="add-authentication-to-windows-app-projects"></a>Adicionar autenticação a projetos de aplicativo do Windows

Esta seção mostra como implementar a interface **IAuthenticate** no Windows 8.1 e projetos de aplicativo do Windows Phone 8.1. Aplicam as mesmas etapas para projetos de plataforma de Windows Universal (UWP). Ignore esta seção se você não oferece suporte a dispositivos Windows.

1. No Visual Studio, clique com botão direito a **WinApp** ou o projeto **WinPhone81** , em seguida, **Definir como projeto de inicialização**.

2. Pressione F5 para iniciar o projeto no depurador, em seguida, verifique se que uma exceção não tratada com um código de status de 401 (não autorizado) é gerada após o aplicativo é iniciado. Isso acontece porque o acesso no back-end é restrito apenas a usuários autorizados.

3. Abra o MainPage.xaml.cs para o projeto de aplicativo do Windows e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substituir `<your_Portable_Class_Library_namespace>` com o namespace para sua biblioteca de classes portátil.

4. Atualize a classe **MainPage** para implementar a interface **IAuthenticate** , da seguinte maneira:

        public sealed partial class MainPage : IAuthenticate


5. Atualize a classe **MainPage** adicionando um campo de **MobileServiceUser** e um método de **autenticação** , que é requerido pela interface **IAuthenticate** , da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Se você estiver usando um provedor de identidade diferente de Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione a seguinte linha de código no construtor da classe **MainPage** antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Substituir `<your_Portable_Class_Library_namespace>` com o namespace para sua biblioteca de classes portátil.  
    Se esse for o projeto de WinApp, você pode pular para baixo até a etapa 8. A próxima etapa se aplica apenas ao projeto WinPhone81, onde você precisa concluir o retorno de chamada de login.

7. (Opcional) No projeto **WinPhone81** aplicativo, abra App.xaml.cs e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Substituir `<your_Portable_Class_Library_namespace>` com o namespace para sua biblioteca de classes portátil.

8.  Adicione a seguinte substituição do método **OnActivated** para a classe de **aplicativo** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Quando a substituição do método já existe, basta adicione o código condicional do trecho acima.

7. Recriar o aplicativo, executá-lo e entrar com o provedor de autenticação você escolheu e verifique se que é possível acessar os dados como um usuário autenticado.

##<a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar dentre os seguintes tutoriais:

+ [Adicionar notificações por push para o seu aplicativo](app-service-mobile-xamarin-forms-get-started-push.md)  
  Saiba como adicionar push notificações de suporte para o seu aplicativo e configurar seu back-end do aplicativo móvel para usar Hubs de notificação do Azure para enviar notificações por push.

+ [Habilitar a sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline seu aplicativo usando um back-end do aplicativo Mobile. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

