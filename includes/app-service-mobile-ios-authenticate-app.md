**Objetivo-C**: 

1. No seu Mac, abra _QSTodoListViewController.m_ em Xcode e adicione o seguinte método. Altere o _google_ para _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ se você não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, [você precisará branca Facebook domínios em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Substituir `[self refresh]` na `viewDidLoad` em _QSTodoListViewController.m_ com o seguinte:

            [self loginAndGetData];

3. Pressione _Executar_ para iniciar o aplicativo e faça logon. Quando você estiver conectado, você poderá ver a lista de tarefas pendentes e fazer atualizações.

**Rápido**:

1. No seu Mac, abra _ToDoTableViewController.swift_ em Xcode e adicione o seguinte método. Altere o _google_ para _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ se você não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, [você precisará branca Facebook domínios em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Remover as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no final da `viewDidLoad()` em _ToDoTableViewController.swift_. Adicionar uma chamada para `loginAndGetData()` no lugar dela:

            loginAndGetData()

3. Pressione _Executar_ para iniciar o aplicativo e faça logon. Quando você estiver conectado, você poderá ver a lista de tarefas pendentes e fazer atualizações.
