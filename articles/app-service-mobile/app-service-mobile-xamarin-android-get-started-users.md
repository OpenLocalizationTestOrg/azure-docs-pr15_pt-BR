<properties
    pageTitle="Introdução ao autenticação para aplicativos de Mobile no Xamarin Android"
    description="Aprenda a usar os aplicativos móveis para autenticar os usuários do aplicativo Xamarin Android por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicione autenticação ao seu aplicativo de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar os usuários de um aplicativo móvel de seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação para o projeto de início rápido usando um provedor de identidade que é suportado pelo aplicativos do Azure Mobile. Após com êxito sendo autenticada e autorizada no aplicativo móvel, o valor de ID de usuário é exibido.

Este tutorial se baseia o início rápido aplicativo Mobile. Você também conclua o tutorial [criar um aplicativo de Xamarin.Android]. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar serviços de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou Xamarin Studio, execute o projeto de cliente em um dispositivo ou emulador. Verifique se que uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. Isso acontece porque o aplicativo tenta acessar seu back-end do aplicativo Mobile como um usuário não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, você irá atualizar o aplicativo cliente para os recursos de solicitação de aplicativo Mobile back-end com um usuário autenticado.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

O aplicativo é atualizado para exigir que os usuários toque no botão **entrar** e autenticar antes de dados são exibidos.

1. Adicione o seguinte código à classe **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Isso cria um novo método para autenticar um usuário e um manipulador de método para um novo botão **entrar** . O usuário no código de exemplo acima é autenticado usando o logon do Facebook. Uma caixa de diálogo é usada para exibir a ID de usuário depois da autenticação.

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente de Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. O método **OnCreate** , excluir ou de comentário a seguinte linha de código:

        OnRefreshItemsSelected ();

4. No arquivo Activity_To_Do.axml, adicione a seguinte definição de botão de *LoginUser* antes do botão *AddItem* existente:

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Adicione o seguinte elemento no arquivo de recursos de Strings.xml:

        <string name="login_button_text">Sign in</string>

6. No Visual Studio ou Xamarin Studio, executar o projeto de cliente em um dispositivo ou emulador e entre com seu provedor de identidade escolhido.

    Quando você estiver conectado em com êxito, o aplicativo exibirá sua ID de logon e a lista de itens todo, e você pode fazer atualizações para os dados.


<!-- URLs. -->
[Criar um aplicativo de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
