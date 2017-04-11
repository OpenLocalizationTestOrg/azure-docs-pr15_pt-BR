<properties
    pageTitle="Adicione autenticação ao seu aplicativo de plataforma de Windows Universal (UWP) | Aplicativos móveis do Azure"
    description="Saiba como usar aplicativos do Azure aplicativo serviço Mobile para autenticar os usuários do aplicativo Universal Windows plataforma (UWP) usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Adicione autenticação ao seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como adicionar autenticação baseada em nuvem para o seu aplicativo móvel. Neste tutorial, você adicionar autenticação para o projeto de início rápido do Universal Windows plataforma (UWP) para os aplicativos móveis usando um provedor de identidade que é suportado pelo serviço de aplicativo do Azure. Após com êxito sendo autenticado e autorizado pelo seu back-end do aplicativo móvel, o valor de ID de usuário é exibido.

Este tutorial se baseia o início rápido aplicativos Mobile. Primeiro você deve concluir o tutorial [Introdução aos aplicativos Mobile](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, você pode confirmar que o acesso anônimo a seu back-end foi desativado. Com o projeto de aplicativo UWP definido como o projeto de inicialização, implantar e executar o aplicativo; verificar se uma exceção não tratada com um código de status de 401 (não autorizado) é aumentada depois que o aplicativo for iniciado. Isso acontece porque o aplicativo tenta acessar seu código de aplicativo móvel como um usuário não autenticado, mas a tabela de *TodoItem* agora requer autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do seu serviço de aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. No UWP aplicativo MainPage.cs do arquivo de projeto e adicione o trecho de código a seguir à classe MainPage:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Este código autentica o usuário com um logon do Facebook. Se você estiver usando um provedor de identidade diferente de Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor do seu provedor.

3. Comentário-out ou exclua a chamada para o método **ButtonRefresh_Click** (ou o método **InitLocalStoreAsync** ) na substituição método **OnNavigatedTo** existente. Isso impede que os dados sejam carregados antes do usuário é autenticado. Em seguida, você adicionará um botão **entrar no** aplicativo que aciona a autenticação.

4. Adicione o trecho de código a seguir à classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Abra o arquivo de projeto MainPage, localize o elemento que define o botão **Salvar** e substituí-lo com o seguinte código:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Pressione a tecla F5 para executar o aplicativo, clique no botão **entrar** e entrar no aplicativo com seu provedor de identidade escolhido. Após sua entrar for bem-sucedida, o aplicativo é executado sem erros e é possível consultar seu back-end e fazer as atualizações nos dados.


##<a name="tokens"></a>Armazenar o token de autenticação no cliente

O exemplo anterior mostrava um padrão entrar, que requer que o cliente entrar em contato com o provedor de identidade e o serviço de aplicativo sempre que o aplicativo é iniciado. Não é apenas este método ineficaz, você pode executar em uso-se relacionam problemas devem muitos clientes tentar iniciar aplicativo ao mesmo tempo. Uma melhor abordagem é o token de autorização retornado pelo seu serviço de aplicativo do cache e tentar usar este primeiro antes de usar um baseado em provedor entrar.

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelos serviços de aplicativo, independentemente de se você estiver usando autenticação gerenciado pelo cliente ou serviço. Este tutorial usa autenticação de serviços gerenciados.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar dentre os seguintes tutoriais:

+ [Adicionar notificações por push para o seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar push notificações de suporte para o seu aplicativo e configurar seu back-end do aplicativo móvel para usar Hubs de notificação do Azure para enviar notificações por push.

+ [Habilitar a sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline seu aplicativo usando um back-end do aplicativo Mobile. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

