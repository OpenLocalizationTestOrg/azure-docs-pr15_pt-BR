<properties
    pageTitle="IOS v 2.0 de Azure AD App | Microsoft Azure"
    description="Como criar um aplicativo iOS que sinais em contas de trabalho ou da escola e usuários com os dois conta pessoal da Microsoft usando bibliotecas de terceiros."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar entrada a um aplicativo iOS usando uma biblioteca de terceiros com a API de gráfico usando o ponto de extremidade de v 2.0

A plataforma de identidade do Microsoft usa padrões abertos como OAuth2 e OpenID se conectar. Os desenvolvedores podem usar qualquer biblioteca que desejam integrar com nossos serviços. Para ajudar os desenvolvedores usar nossa plataforma com outras bibliotecas, podemos escreveu algumas orientações como essa para demonstrar como configurar bibliotecas de terceiros para conectar-se para a plataforma de identidade da Microsoft. A maioria das bibliotecas que implementar [a especificação de RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode se conectar para a plataforma de identidade da Microsoft.

Com o aplicativo que cria este passo a passo, os usuários podem entrar em sua organização e pesquise por outras pessoas em sua organização usando a API do gráfico.

Se você for novo OAuth2 ou OpenID conectar, muito dessa configuração de amostra não faz sentido para você. Recomendamos que você leia [protocolos v 2.0 - OAuth 2.0 autorização código fluxo](active-directory-v2-protocols-oauth-code.md) como plano de fundo.


> [AZURE.NOTE]
    Alguns recursos de nossa plataforma que têm uma expressão nos padrões OAuth2 ou OpenID conectar, como acesso condicional e gerenciamento de políticas de Intune, exigem que você use nossa Abrir origem bibliotecas de identidade do Microsoft Azure.

O ponto de extremidade de v 2.0 não oferece suporte a todos os cenários do Active Directory do Azure e recursos.

> [AZURE.NOTE]
    Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Baixe o código do GitHub
O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Você também pode fazer o download de amostra e começar agora mesmo:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrar um aplicativo
Criar um novo aplicativo no [portal de registro do aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga as etapas detalhadas da [como registrar um aplicativo com o ponto de extremidade de v 2.0](active-directory-v2-app-registration.md).  Certifique-se de:

- Copie a **Id do aplicativo** que é atribuído a seu aplicativo, pois você precisará delas em breve.
- Adicione **a plataforma para o aplicativo** .
- Copie o **URI redirecionar** a partir do portal. Você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Baixar a biblioteca de NXOAuth2 de terceiros e criar um espaço de trabalho

Para este passo a passo, você usará o OAuth2Client do GitHub, que é uma biblioteca de OAuth2 para o Mac OS X e iOS (Cocoa e Cocoa toque). Esta biblioteca baseia-se em rascunho 10 da especificação OAuth2. Ele implementa o perfil do aplicativo nativo e aceita o ponto de extremidade de autorização do usuário. Estas são todas as coisas que você precisará integrar com a plataforma de identidade da Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Adicionar a biblioteca ao seu projeto usando CocoaPods

CocoaPods é um gerente de dependência para projetos Xcode. Ele gerencia automaticamente as etapas de instalação anterior.

```
$ vi Podfile
```
1. Adicione o seguinte para este podfile:

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Carrega o podfile usando CocoaPods. Isso criará um novo espaço de trabalho Xcode que você irá carregar.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorar a estrutura do projeto

A seguinte estrutura está configurada para nosso projeto no esqueleto:

- Um modo de exibição mestre com uma pesquisa UPN
- Um modo de exibição de detalhes para os dados sobre o usuário selecionado
- Um modo de exibição de Login onde um usuário pode entrar aplicativo para consultar o gráfico

Podemos moverá para vários arquivos no esqueleto para adicionar autenticação. Outras partes do código, como o código de visual não relacionados à identidade, mas são fornecidas para você.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurar o arquivo de settings.plst na biblioteca

-   No projeto de início rápido, abra o `settings.plist` arquivo. Substitua os valores dos elementos na seção para refletir os valores que você usou no portal do Azure. Seu código fará referência esses valores sempre que ele usa a biblioteca de autenticação do Active Directory.
    -   O `clientId` é a ID do cliente do aplicativo que você copiou do portal.
    -   O `redirectUri` é a URL de redirecionamento que o portal fornecido.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurar a biblioteca de NXOAuth2Client no seu LoginViewController

A biblioteca de NXOAuth2Client requer alguns valores para configurar tudo. Depois de concluir essa tarefa, você pode usar o token adquirido para chamar a API do gráfico. Porque `LoginView` será chamado a qualquer momento que precisamos autenticar, faz sentido colocar valores de configuração no arquivo.

- Vamos adicionar alguns valores para o `LoginViewController.m` arquivo para definir o contexto para autenticação e autorização. Detalhes sobre os valores a seguir o código.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Vamos ver detalhes sobre o código.

Primeira cadeia de caracteres é para `scopes`.  O `User.Read` valor permite que você leia a básicas de perfil do usuário conectado.

Você pode saber mais sobre todos os escopos disponíveis em [escopos de permissão do Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh`, e `tokenURL`, você deve usar os valores fornecidos anteriormente. Se você usa a fonte de abrir bibliotecas de identidade do Microsoft Azure, podemos suspenso esses dados para você, usando nosso ponto de extremidade de metadados. Fizemos o trabalho árduo de extrair esses valores para você.

O `keychain` valor é o recipiente que a biblioteca de NXOAuth2Client será usado para criar um repositório de chaves para armazenar seus tokens. Se você gostaria de obter logon único (SSO) entre vários aplicativos, você pode especificar o mesmo conjunto de chaves em cada um dos seus aplicativos e solicitar o uso de que chaves em seus direitos Xcode. Isso é explicado na documentação do Apple.

O restante desses valores são necessários para usar a biblioteca e criar locais para você executar valores para o contexto.

### <a name="create-a-url-cache"></a>Criar um cache de URL

Dentro de `(void)viewDidLoad()`, que é sempre chamado depois que o modo de exibição é carregado, o seguinte código primes um cache para nosso uso.

Adicione o seguinte código:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Criar uma exibição da Web para entrar

Uma exibição da Web pode solicitar ao usuário fatores adicionais como mensagem de texto SMS (se configurado) ou retornar mensagens de erro para o usuário. Aqui você definirá até a exibição da Web e, em seguida, mais tarde, escreva o código para manipular os retornos de chamada que acontecerão na exibição da Web a partir dos serviços de identidade.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substituir os métodos de exibição da Web para lidar com a autenticação

Para informar a exibição da Web o que acontece quando um usuário precisa entrar como discutido anteriormente, você pode colar o código a seguir.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escrever código para manipular o resultado da solicitação OAuth2

O seguinte código tratará o redirectURL que retorna da exibição da Web. Se a autenticação não foi bem-sucedida, o código tentará novamente. Enquanto isso, a biblioteca fornecerá o erro que você pode ver no console ou tratar assíncrona.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurar o contexto de OAuth (chamado de repositório de conta)

Aqui você pode chamar `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` no repositório de conta compartilhada para cada serviço que você deseja que o aplicativo possam acessar. O tipo de conta é uma cadeia de caracteres que é usada como um identificador para um determinado serviço. Porque você está acessando a API do gráfico, o código se refere a ele como `"myGraphService"`. Você então configurar observador que lhe dirá quando algo for alterado com o token. Depois de obter o token, você voltar o usuário de volta para o `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurar o modo de exibição mestre para pesquisar e exibir os usuários da API do gráfico

Um aplicativo do controlador de exibição mestre (MVC) que exibe os dados retornados na grade está além do escopo desta explicação e tutoriais online muitos explicam como criar um. Todo esse código está no arquivo reduzido. No entanto, você precisa lidar com algumas coisas neste aplicativo MVC:

* INTERCEPÇÃO quando um usuário digita algo no campo de pesquisa
* Fornecer um objeto de dados para o MasterView para que ele possa exibir os resultados na grade

Faremos aqueles abaixo.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Adicionar uma verificação para ver se você está conectado

O aplicativo faz pouco, se o usuário não estiver conectado, portanto, é inteligente para verificar se já existe um token no cache. Caso contrário, você redirecionar para o LoginView para o usuário para entrar. Se você cancelar, a melhor maneira de realizar ações quando carrega uma exibição é usar o `viewDidLoad()` método que Apple fornece conosco.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Atualizar o modo de exibição de tabela quando dados for recebidos

Quando a API de gráfico retorna dados, você precisa exibir os dados. Para simplificar, aqui está todo o código para atualizar a tabela. Apenas você pode colar os valores corretos em seu código de texto clichê MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Oferece uma maneira de chamar a API de gráfico quando alguém digitar no campo de pesquisa

Quando um usuário digita uma pesquisa na caixa, você precisa as empurre que a API do gráfico. O `GraphAPICaller` classe, que você criará no código a seguir, separa a funcionalidade de pesquisa da apresentação. Por agora, vamos escrever o código que feeds quaisquer caracteres de pesquisa para a API do gráfico. Podemos fazer isso fornecendo um método chamado `lookupInGraph`, que usa a cadeia de caracteres que desejamos pesquisar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escrever uma classe auxiliar para acessar a API do gráfico

Este é o núcleo do nosso aplicativo. Enquanto o resto foi inserir código no padrão MVC padrão da Apple, aqui você escreve código para consultar o gráfico como os tipos de usuário e, em seguida, retornar esses dados. Aqui está o código e uma explicação detalhada acompanhará.

### <a name="create-a-new-objective-c-header-file"></a>Criar um novo arquivo de cabeçalho de objetivo C

Nomeie o arquivo `GraphAPICaller.h`e adicione o seguinte código.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Veja que um método especificado leva uma cadeia de caracteres e retorna um completionBlock. Este completionBlock, como você pode esperar, atualizará a tabela, fornecendo um objeto com dados preenchidos em tempo real, como as pesquisas de usuário.


### <a name="create-a-new-objective-c-file"></a>Criar um novo arquivo de objetivo C

Nomeie o arquivo `GraphAPICaller.m`e adicione o seguinte método.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Vamos dar uma olhada esse método em detalhes.

O núcleo desse código é na `NXOAuth2Request`, método que leva os parâmetros que você já definiu no arquivo settings.plist.

A primeira etapa é construir a chamada de API de gráfico à direita. Como você está chamando `/users`, você pode especificar que anexando-o para o recurso de gráfico API junto com a versão. Faz sentido para colocar em um arquivo de configurações externas porque esses podem alterar como a API evolui.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Em seguida, você precisa especificar parâmetros que você também fornecerá à chamada API do gráfico. É *muito importante* que você não coloque os parâmetros no ponto de extremidade do recurso porque que é apagado para todos os caracteres de conformidade não-URI no tempo de execução. Todo o código de consulta deve ser fornecido nos parâmetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Você pode observar isso chama um `convertParamsToDictionary` método que você ainda não tenha escrito. Vamos fazer isso agora no final do arquivo:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Em seguida, vamos usar o `NXOAuth2Request` método para obter dados de API no formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Por fim, vejamos como você retorna os dados para o MasterViewController. Os dados retorna forma e precisam ser desserializado e carregados em um objeto que o MainViewController pode consumir. Para essa finalidade, a estrutura tem um `User.m/h` arquivo que cria um objeto de usuário. Preencher esse objeto de usuário com as informações do gráfico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Executar a amostra

Se você tiver usado o esqueleto ou seguido juntamente com o passo a passo seu aplicativo agora deve executar. Inicie o simulator e clique em **entrar** para usar o aplicativo.

## <a name="get-security-updates-for-our-product"></a>Obter atualizações de segurança do nosso produto

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando o [TechCenter de segurança](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
