
1. Abra o arquivo de projeto compartilhado MainPage.cs e adicione o trecho de código a seguir à classe MainPage:
    
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

3. Comentário-out ou exclua a chamada para o método **RefreshTodoItems** na substituição do método **OnNavigatedTo** existente.

    Isso impede que os dados sejam carregados antes do usuário é autenticado. Em seguida, você adicionará um botão **entrar no** aplicativo que aciona a autenticação.

4. Adicione o trecho de código a seguir à classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. No projeto de aplicativo da Windows Store, abra o arquivo de projeto MainPage e adicione o seguinte elemento de **botão** logo antes do elemento que define o botão **Salvar** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. No projeto de aplicativo de armazenamento do Windows Phone, adicione o seguinte elemento de **botão** na **ContentPanel**, após o elemento de **caixa de texto** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Abra o arquivo de projeto compartilhado App.xaml.cs e adicione o seguinte código:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Se o método **OnActivated** já existir, basta adicionar o `#if...#endif` bloco de código.

9. Pressione a tecla F5 para executar o aplicativo da Windows Store, clique no botão **entrar** e entrar no aplicativo com seu provedor de identidade escolhido. 

    Quando você estiver conectado em com êxito, o aplicativo deve ser executado sem erros e você deve ser capaz de consultar seu back-end e fazer as atualizações nos dados.

10. O projeto de aplicativo do Windows Phone repositório de atalho, clique em **Definir como projeto de inicialização**, repita a etapa anterior para verificar se o repositório do Windows Phone app também funciona corretamente.  

 