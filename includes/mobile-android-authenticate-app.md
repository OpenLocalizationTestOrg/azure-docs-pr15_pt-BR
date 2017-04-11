
1. No **Explorador de projeto** no Android Studio, abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Adicione o seguinte método à classe **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Isso cria um novo método para lidar com o processo de autenticação. O usuário é autenticado usando o logon do Google. Uma caixa de diálogo é exibida que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente Google, altere o valor passado para o método de **login** acima para um dos seguintes: _MicrosoftAccount_, _Facebook_, _Twitter_ou _windowsazureactivedirectory_.

3. No método **onCreate** , adicione a seguinte linha de código após o código que cria o `MobileServiceClient` objeto.

        authenticate();

    Esta chamada inicia o processo de autenticação.

4. Mover o código restante após `authenticate();` no método **onCreate** para um novo método de **createTable** , que tem esta aparência:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. No menu **Executar** , clique em **executar o aplicativo** para iniciar o aplicativo e entre com seu provedor de identidade escolhido. 

    Quando você estiver conectado em com êxito, o aplicativo deve ser executado sem erros e você deve ser capaz de consultar o serviço de back-end e fazer as atualizações nos dados.