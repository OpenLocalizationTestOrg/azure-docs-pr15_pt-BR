<properties
    pageTitle="Azure B2C diretório ativo: Chamar uma web API de um aplicativo iOS usando bibliotecas de terceiros | Microsoft Azure"
    description="Este artigo mostrará como criar um aplicativo de 'lista de tarefas pendentes' iOS que chama um Node web API usando tokens de portador OAuth 2.0 usando uma biblioteca de terceiros"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< marcas ms.service= "active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic= "banner do artigo"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>B2C do Azure AD: Chamar uma web API de um aplicativo iOS usando uma biblioteca de terceiros

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

A plataforma de identidade do Microsoft usa padrões abertos como OAuth2 e OpenID se conectar. Isso permite aos desenvolvedores aproveitar qualquer biblioteca que desejam integrar com nossos serviços. Para ajudar os desenvolvedores usando nossa plataforma com outras bibliotecas podemos escreveu algumas orientações como essa para demonstrar como configurar bibliotecas de terceiros para conectar-se para a plataforma de identidade da Microsoft. A maioria das bibliotecas que implementar [a especificação de RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) será capaz de se conectar à plataforma Microsoft Identity.


Se você está começando a OAuth2 ou OpenID conectar muito dessa configuração de amostra pode não fazer muito sentido para você. Recomendamos que você examine uma breve [Visão geral do protocolo que já tenha sido documentadas aqui](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Alguns recursos de nossa plataforma que têm uma expressão desses padrões, tais como gerenciamento de política de acesso condicional e Intune, exigem que você use nossa Abrir origem bibliotecas de identidade do Microsoft Azure. 
   
Nem todos os cenários do Active Directory do Azure e recursos são suportados pela plataforma B2C.  Para determinar se você deve usar a plataforma B2C, leia sobre as [limitações de B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você não tiver um já, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. O aplicativo e web API são representados por um único **ID de aplicativo** nesse caso, pois elas compõem um aplicativo lógico. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de:

- Inclua um **dispositivo móvel** no aplicativo.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você também precisará isso mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo contém uma experiência de identidade: um combinado entrar inscrição. Você precisa criar essa política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando você cria a política, certifique-se a:

- Escolha o **nome de exibição** e atributos de inscrição na sua política.
- Escolha as declarações de aplicativo **nome para exibição** e a **ID de objeto** em cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Ele deve ter o prefixo `b2c_1_`.  Posteriormente, você precisará o nome da política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após ter criado suas políticas, você está pronto para criar seu aplicativo.


## <a name="download-the-code"></a>Baixe o código

O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Para acompanhar, você pode [baixar o aplicativo como um. zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou cloná-la:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou basta baixar o código concluído e começar agora mesmo: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Baixar o nxoauth2 de biblioteca de terceiros e iniciar um espaço de trabalho

Para este passo a passo, usaremos a OAuth2Client do GitHub, uma biblioteca de OAuth2 para o Mac OS X & iOS (Cocoa & Cocoa toque). Esta biblioteca baseia-se em rascunho 10 da especificação OAuth2. Ele implementa o perfil do aplicativo nativo e aceita o ponto de extremidade de autorização de usuário final. Estas são todas as coisas que precisaremos em ordem para integrat com plataforma de identidade da Microsoft.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Adicionando a biblioteca ao seu projeto usando o CocoaPods

CocoaPods é um gerente de dependência para projetos Xcode. Ele gerencia automaticamente as etapas de instalação acima.

```
$ vi Podfile
```
Adicione o seguinte para este podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Carrega agora o podfile usando cocoapods. Isso criará um novo espaço de trabalho XCode você carregará.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>A estrutura do projeto

Nós temos a seguinte estrutura configurar para o nosso projeto no esqueleto:

* Um **Modo de exibição mestre** com um painel de tarefas
* Uma **Adicionar modo de exibição de tarefa** para os dados sobre a tarefa selecionada
* Um **Modo de exibição de Login** que permite que um usuário entrar no aplicativo.

Podemos saltará para vários arquivos do projeto para adicionar autenticação. Outras partes do código como o código visual não está germane a identidade e são fornecidos para você.

## <a name="create-the-settingsplist-file-for-your-application"></a>Criar a `settings.plist` arquivo para seu aplicativo

É mais fácil de configurar o aplicativo se temos um local centralizado para colocar nossos valores de configuração. Ele também ajuda você a entender o que cada configuração faz em seu aplicativo. Podemos utilizará a *Lista de propriedade* como uma maneira de fornecer esses valores para o aplicativo.

* Criar/abrir o `settings.plist` arquivo em `Supporting Files` em seu espaço de trabalho do aplicativo

* Insira os seguintes valores (entraremos riscadas detalhadamente em breve)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Vamos para esses detalhadamente.


Para `authURL`, `loginURL`, `bhh`, `tokenURL` você notará que você precisar preencher o nome do seu locatário. Este é o nome do locatário do seu locatário B2C que foi atribuído a você. Por exemplo, `kidventusb2c.onmicrosoft.com`. Se você usa o nossa Abrir origem bibliotecas de identidade do Microsoft Azure nós seria suspenso esses dados usando nosso ponto de extremidade de metadados. Fizemos o trabalho árduo de extrair esses valores para você.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

O `keychain` valor é o recipiente que a biblioteca de NXOAuth2Client será usado para criar um repositório de chaves para armazenar seus tokens. Se você gostaria de obter SSO entre vários aplicativos você pode especificar o mesmo conjunto de chaves em cada um dos seus aplicativos, bem como solicitar o uso de que chaves em seu entitements XCode. Isso é coberto na documentação do Apple.

O `<policy name>` no final de cada URL são os lugares onde você deseja colocados a política que você criou acima. O aplicativo chamará essas políticas dependendo do fluxo.

O `taskAPI` é o ponto de extremidade do resto será chamamos com seu token de B2C como adicionar tarefas ou consulta existente. Isso tiver sido configurado especificamente para este exemplo. Não é necessário alterá-lo para o exemplo funcione.

O restante desses valores são necessários para usar a biblioteca e simplesmente criar locais para você executar valores para o contexto.

Agora que temos a `settings.plist` arquivo criado, precisamos código lê-la.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configurar uma classe AppData para ler as configurações de nosso

Vamos criar um arquivo simples que apenas analisa nosso `settngs.plist` arquivo criado acima e fazer essas avaialble de configurações no futuro qualquer classe. Como não queremos criar uma nova cópia dos dados sempre que uma classe pergunta, abordaremos utilizar um padrão simples e retornar somente a mesma instância criada sempre que uma solicitação é feita para as configurações

* Criar um `AppData.h` arquivo:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Criar um `AppData.m` arquivo:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Agora podemos facilmente tocar em nossos dados simplesmente chamando `  AppData *data = [AppData getInstance];` em qualquer uma das nossas classes como você verá abaixo.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurar a biblioteca de NXOAuth2Client no seu AppDelegate

A biblioteca de NXOAuthClient requer alguns valores para configurar tudo. Uma vez ou seja concluída você pode usar o símbolo de obtidas para chamar a API REST. Como sabemos que o `AppDelegate` será chamado qualquer momento podemos carregar o aplicativo faz sentido que podemos colocar nossos valores de configuração no arquivo.
* Abrir `AppDelegate.m` arquivo

* Importe alguns arquivos de cabeçalho que usaremos posteriormente.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Adicionar o `setupOAuth2AccountStore` método na AppDelegate

Precisamos para criar um AccountStore e, em seguida, o feed de dados podemos apenas lidas a partir do `settings.plist` arquivo.

Existem algumas coisas que você deve estar ciente sobre o serviço B2C neste momento que fará mais compreendido este código:


1. Azure AD B2C usa a *política* conforme fornecido pelos parâmetros de consulta para sua solicitação de serviço. Isso permite que o Active Directory do Azure agir como um serviço independente apenas para seu aplicativo. Para fornecer esses extra precisamos fornecer de parâmetros de consulta a `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` método com nossos parâmetros de política personalizada. 

2. Azure AD B2C usa escopos em grande parte da mesma maneira como outros servidores de OAuth2. No entanto desde o uso de B2C é quanto sobre autenticação de um usuário como acessar recursos alguns escopos absolutamente são necessários para que o fluxo funcione corretamente. Este é o `openid` escopo. Nossa identidade do Microsoft SDKs automaticamente fornecem o `openid` escopo para você para que você não verá que na nossa configuração SDK. Como estamos usando uma biblioteca de terceiros, no entanto, precisamos especificar este escopo.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Em seguida, verifique se ligá-lo a AppDelegate em `didFinishLaunchingWithOptions:` método. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Criar um `LoginViewController` classe que usaremos para lidar com solicitações de autenticação

Usamos uma exibição da Web para a conta entrada. Isso nos permite solicitar ao usuário fatores adicionais como mensagem de texto SMS (se configurado) ou dar mensagens de erro de volta para o usuário. Aqui será definimos até a exibição da Web e, em seguida, gravar posteriormente o código para manipular os retornos de chamada que acontecerão na exibição da Web do serviço de identidade da Microsoft.

* Criar um `LoginViewController.h` classe

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vamos criar cada um desses métodos abaixo.

> [AZURE.NOTE] 
    Certifique-se de que você associa a `loginView` para a exibição da Web real que está dentro de seu storyboard. Caso contrário, você não tem uma exibição da Web que pode pop-up horário se autenticar.

* Criar um `LoginViewController.m` classe

* Adicione algumas variáveis para carregarem estado como podemos autenticar

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substituir os métodos de exibição da Web para lidar com a autenticação

Precisamos dizer a exibição da Web o comportamento que queremos quando um usuário precisa fazer o login conforme discutido acima. Você simplesmente pode recortar e colar o código a seguir.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escrever código para manipular o resultado da solicitação OAuth2

Precisaremos código que irá manipular o redirectURL proveniente a exibição da Web. Se não fosse bem-sucedida, podemos tentará novamente. Enquanto isso, a biblioteca fornecerá o erro que você pode ver no console ou lidar com asyncronously. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configure as fábricas de notificação.

Podemos criar o mesmo método fizemos na `AppDelegate` acima, mas desta vez adicionaremos alguns `NSNotification`s para diga-no que está acontecendo em nosso serviço. Podemos configurar observador que nos informará quando algo for alterado com o token. Depois do token nós retornar o usuário voltar para o `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Adicionar código que trata o usuário sempre que uma solicitação é iniciada para entrada nativo

Vamos criar um método que será chamado sempre que temos uma solicitação de autenticação. Este será o método que realmente cria uma exibição da Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por fim, vamos chamar todos esses métodos podemos escreveu acima sempre a `LoginViewController` carrega. Podemos fazer isso adicionando esses métodos para nosso `viewDidLoad` método Apple nos dá

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Agora, você terminou com a criação da maneira principal que podemos poderá interagir com nosso aplicativo para entrar. Nós se conectado, podemos precisará usar nossos tokens recebemos. Para que vamos criar alguns código auxiliar que irá chamar APIs REST para que possamos usar essa biblioteca.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Criar um `GraphAPICaller` classe lidar com nossas solicitações para uma API REST

Temos uma configuração carregada toda vez que devemos carregar nosso aplicativo. Agora, precisamos fazer algo com ele quando temos um token. 

* Criar um `GraphAPICaller.h` arquivo

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Você ver neste código que estamos criando dois métodos: um para obter as tarefas de uma API e outro para adicionar tarefas à API.

Agora que estamos configurou nossa interface, vamos adicionar a implementação real:

* Criar um`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Finalmente, criar e executar o aplicativo no Xcode. Inscrever ou entrar aplicativo e criar tarefas de um usuário conectado. Sair e entrar novamente como um usuário diferente e criar tarefas para esse usuário.

Observe que as tarefas são armazenados por usuário a API, porque a API extrai a identidade do usuário do token de acesso que ele recebe.


## <a name="next-steps"></a>Próximas etapas

Agora, você pode mover em tópicos B2C mais avançados. Você pode tentar:

[Chamar um web Node API a partir de um aplicativo de web Node]()

[Personalizar a UX para um aplicativo B2C]()
