
1. No arquivo de projeto MainPage.xaml.cs, adicione as seguintes instruções **usando** :

        using System.Linq;      
        using Windows.Security.Credentials;

2. Substitua o método de **AuthenticateAsync** com o seguinte código:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    Nesta versão do **AuthenticateAsync**, o aplicativo tentará usar credenciais armazenadas no **PasswordVault** para acessar o serviço. Um entrar regular também é executada quando não há nenhuma credencial armazenado.

    >[AZURE.NOTE]Um símbolo em cache pode ser expirado e expiração do token também pode ocorrer após a autenticação quando o aplicativo estiver em uso. Para saber como determinar se um token expirou, consulte [Verificar se há tokens de autenticação expirou](http://aka.ms/jww5vp). Há uma solução para tratamento de erros de autorização relacionados a tokens expirando, consulte a postagem [cache e manipulação de tokens expirados nos serviços do Azure Mobile gerenciado SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 

3. Reinicie o aplicativo duas vezes.

    Observe que a primeira inicialização, entrar com o provedor novamente é necessária. Entretanto, na segunda reinicialização as credenciais armazenadas em cache são usadas e entrar é ignorada. 
