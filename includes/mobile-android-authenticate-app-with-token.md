
O exemplo anterior mostrava um padrão entrar, que requer que o cliente entrar em contato com o provedor de identidade e o serviço do Azure de back-end toda vez que o aplicativo é iniciado. Não é apenas este método ineficaz, que você pode enfrentar problemas relacionados ao uso devem muitos clientes tentar iniciar aplicativo ao mesmo tempo. Uma melhor abordagem é o token de autorização retornado pelo serviço Azure em cache e tente usar este primeiro antes de usar um baseado em provedor entrar. 

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelo serviço Azure, independentemente de se você estiver usando autenticação gerenciado pelo cliente ou serviço back-end. Este tutorial usa autenticação de serviços gerenciados.


1. Abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Adicionar o os seguintes membros a `ToDoActivity` classe.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. No arquivo ToDoActivity.java, adicione a seguinte definição para o `cacheUserToken` método.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Este método armazena a id de usuário e o símbolo em um arquivo de preferência que está marcado como particular. Isso deve proteger o acesso ao cache para que outros aplicativos do dispositivo não tem acesso ao token porque a preferência está na área restrita para o aplicativo. No entanto, se alguém obtiver acesso ao dispositivo, é possível que eles podem acessar o cache de token por outros meios. 

    >[AZURE.NOTE]Você pode proteger ainda mais o token com criptografia se token acesso aos seus dados considerado altamente confidencial e alguém pode obter acesso ao dispositivo. No entanto, uma solução completamente segura está além do escopo deste tutorial e dependente seus requisitos de segurança.


4. No arquivo ToDoActivity.java, adicione a seguinte definição para o `loadUserTokenCache` método.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. No arquivo de *ToDoActivity.java* , substitua o `authenticate` método com o seguinte método que usa um cache de token. Altere o provedor de login se você quiser usar uma conta diferente Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Construa a autenticação de aplicativo e teste usando uma conta válida. Executá-lo pelo menos duas vezes. Durante a primeira execução, você deve receber um aviso para efetuar login e criar o cache de token. Depois disso, cada execução tentará carregar o cache de token para autenticação e você não deve ser necessárias para fazer logon.



