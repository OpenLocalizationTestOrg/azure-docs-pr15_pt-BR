Nossa cache de token deve trabalhar em um caso simples, mas, o que acontece quando o token expira ou foi revogado? O token pode expirar quando o aplicativo não está sendo executado. Isso significa que o cache de token é inválido. O token também poderia expirar enquanto o aplicativo está sendo executado. O resultado é um status HTTP código 401 "não autorizado". 

Precisamos sejam capazes de detectar um token expirado e atualizá-lo. Para fazer isso, usamos uma [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) da [biblioteca cliente Android](http://dl.windowsazure.com/androiddocs/).

Nesta seção, você irá definir um ServiceFilter que detectar uma resposta 401 do código de status HTTP e acionar uma atualização do token e o cache de token. Além disso, este ServiceFilter bloqueará outras solicitações de saída durante a autenticação para que essas solicitações podem usar o token atualizado.

1. Abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:
 
        import java.util.concurrent.atomic.AtomicBoolean;
        import java.util.concurrent.ExecutionException;

        import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. Adicionar os seguintes membros a `ToDoActivity` classe. 

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Esses serão usados para ajudar a sincronizar a autenticação do usuário. Queremos autenticar uma vez. Quaisquer chamadas durante uma autenticação deverão aguardar e use o novo token de autenticação em andamento.

3. No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity que será usada para bloquear chamadas de saída em outros threads enquanto autenticação está em andamento.

        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;
            
            return detected;
        }
        

4. No arquivo ToDoActivity.java, adicione o seguinte método à classe ToDoActivity. Este método aciona a espera e em seguida, atualize o token no solicitações de saída quando a autenticação é concluída. 

        
        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }


5. No arquivo ToDoActivity.java, atualize o `authenticate` método do ToDoActivity para que ele aceita um parâmetro booliano para permitir forçando a atualização do cache de token e token de classe. Precisamos também notificar qualquer threads bloqueados quando autenticação seja concluída para que ela podem atende o novo token.

        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {
            
            bAuthenticating = true;
            
            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {
    
                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   



6. No arquivo ToDoActivity.java, adicione este código para um novo `RefreshTokenCacheFilter` classe dentro da classe ToDoActivity:

        /**
        * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {
         
            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
            
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(
                    final ServiceFilterRequest request, 
                    final NextServiceFilterCallback nextServiceFilterCallback
                    )
            {
                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);
     
                // Send the request down the filter chain
                // retrying up to 5 times on 401 response codes.
                ListenableFuture<ServiceFilterResponse> future = null;
                ServiceFilterResponse response = null;
                int responseCode = 401;
                for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
                {
                    future = nextServiceFilterCallback.onNext(request);
                    try {
                        response = future.get();
                        responseCode = response.getStatus().getStatusCode();
                    } catch (InterruptedException e) {
                       e.printStackTrace();
                    } catch (ExecutionException e) {
                        if (e.getCause().getClass() == MobileServiceException.class)
                        {
                            MobileServiceException mEx = (MobileServiceException) e.getCause();
                            responseCode = mEx.getResponse().getStatus().getStatusCode();
                            if (responseCode == 401)
                            {
                                // Two simultaneous requests from independent threads could get HTTP status 401. 
                                // Protecting against that right here so multiple authentication requests are
                                // not setup to run on the UI thread.
                                // We only want to authenticate once. Requests should just wait and retry 
                                // with the new token.
                                if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
                                {
                                    // Authenticate on UI thread
                                    runOnUiThread(new Runnable() {
                                        @Override
                                        public void run() {
                                            // Force a token refresh during authentication.
                                            authenticate(true);
                                        }
                                    });
                                }
    
                                // Wait for authentication to complete then update the token in the request. 
                                waitAndUpdateRequestToken(request);
                                mAtomicAuthenticatingFlag.set(false);                                                  
                            }
                        }
                    }
                }
                return future;
            }
        }


    Esse filtro de serviço verificará cada resposta para código de status HTTP 401 "não autorizado". Se um 401 for encontrada, uma nova solicitação de logon para obter um novo símbolo será configuração no thread da interface do usuário. Outras chamadas serão bloqueadas até que o logon seja concluído ou 5 tentativas falha. Se o novo token é obtido, a solicitação que disparou o 401 será repetida com o novo token e quaisquer chamadas bloqueadas serão repetidas com o token de novo. 

7. No arquivo ToDoActivity.java, adicione este código para um novo `ProgressFilter` classe dentro da classe ToDoActivity:
        
        /**
        * The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
        * the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
        */
        private class ProgressFilter implements ServiceFilter {
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {

                final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();

                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
                    }
                });

                ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);

                Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
                    @Override
                    public void onFailure(Throwable e) {
                        resultFuture.setException(e);
                    }

                    @Override
                    public void onSuccess(ServiceFilterResponse response) {
                        runOnUiThread(new Runnable() {

                            @Override
                            public void run() {
                                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
                            }
                        });

                        resultFuture.set(response);
                    }
                });

                return resultFuture;
            }
        }
        
    Esse filtro mostrará a barra de progresso no início da solicitação e ocultará a ele quando a resposta chegou.

8. No arquivo ToDoActivity.java, atualize o `onCreate` método da seguinte forma:

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            
            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);
        
            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
                // Authenticate passing false to load the current token cache if available.
                authenticate(false);
                    
            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }


       In this code, `RefreshTokenCacheFilter` is used in addition to `ProgressFilter`. Also during `onCreate` we want to load the token cache. So `false` is passed in to the `authenticate` method.


