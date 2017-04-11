<properties
    pageTitle="Como usar iOS SDK para aplicativos do Azure móvel"
    description="Como usar iOS SDK para aplicativos do Azure móvel"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como usar iOS biblioteca de cliente para aplicativos do Azure móvel

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando a mais recente [iOS de aplicativos do Azure Mobile SDK][1]. Se você estiver começando a usar os aplicativos do Azure Mobile, primeiro completa [Início rápido do Azure Mobile aplicativos] para criar um back-end, crie uma tabela e fazer o download de um projeto de Xcode iOS pré-criados. Neste guia, podemos focalizar o iOS SDK do lado do cliente. Para saber mais sobre o SDK do lado do servidor para o back-end, consulte o HOWTOs de SDK do servidor.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o cliente do iOS SDK está localizada aqui: [aplicativos do Azure Mobile iOS referência cliente][2].

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK do iOS oferece suporte a projetos de objetivo-C, rápido 2.2 e 2.3 rápido projetos para iOS versões 8.0 ou posteriores.

A autenticação de "server-fluxo" usa uma exibição da Web para a interface do usuário apresentada.  Se o dispositivo não for capaz de apresentar uma WebView UI, então outro método de autenticação é necessário que está fora do escopo do produto.  
Este SDK, portanto, não é adequado para o tipo de inspeção ou da mesma forma dispositivos restritos.

##<a name="Setup"></a>Pré-requisitos e configuração

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tenha o mesmo esquema as tabelas esses tutoriais. Este guia também pressupõe que no seu código, você referência `MicrosoftAzureMobile.framework` e importar `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Como: criar cliente

Para acessar um back-end de aplicativos do Azure Mobile em seu projeto, crie um `MSClient`. Substituir `AppUrl` com a URL do aplicativo. Você pode deixar `gatewayURLString` e `applicationKey` vazia. Se você configurar um gateway para autenticação, preencher `gatewayURLString` com a URL do gateway.

**Objetivo-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Rápido**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Como: criar referência de tabela

A dados do access ou atualização, criar uma referência para a tabela de back-end. Substituir `TodoItem` com o nome da tabela

**Objetivo-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Rápido**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Como: consultar dados

Para criar uma consulta de banco de dados, consultar a `MSTable` objeto. A seguinte consulta obtém todos os itens em `TodoItem` e registra o texto de cada item.

**Objetivo-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Rápido**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Como: filtrar dados retornados

Para filtrar resultados, há muitas opções disponíveis.

Para filtrar usando um predicado, use um `NSPredicate` e `readWithPredicate`. Os seguintes filtros retornou dados para localizar somente os itens Todo incompletos.

**Objetivo-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Rápido**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Como: usar MSQuery

Para executar uma consulta complexa (incluindo a classificação e paginação), crie um `MSQuery` objeto, diretamente ou usando um predicado:

**Objetivo-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Rápido**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`permite que você controle vários comportamentos de consulta.

* Especificar a ordem de resultados
* Limitar quais campos para retornar
* Limitar a quantos registros para retornar
* Especificar a contagem total em resposta
* Especificar parâmetros de cadeia de caracteres de consulta personalizada na solicitação
* Aplicar funções adicionais

Executar um `MSQuery` consulta chamando `readWithCompletion` no objeto.

## <a name="sorting"></a>Como: classificar dados com MSQuery

Para classificar resultados, vamos examinar um exemplo. Para classificar por texto' campo' em ordem crescente, depois por decrescente 'completa', invocar `MSQuery` desta forma:

**Objetivo-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Rápido**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Como: limitar campos e expanda parâmetros de cadeia de caracteres de consulta com MSQuery

Para limitar os campos a ser retornado em uma consulta, especifique os nomes dos campos na propriedade **selectFields** . Este exemplo retorna somente o texto e os campos concluídos:

**Objetivo-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Rápido**:

```
query.selectFields = ["text", "complete"]
```

Para incluir parâmetros de cadeia de caracteres de consulta adicionais na solicitação de servidor (por exemplo, porque um script de servidor personalizado usa-las), preencher `query.parameters` desta forma:

**Objetivo-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Rápido**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Como: configurar o tamanho da página

Com aplicativos do Azure Mobile, o tamanho da página controla o número de registros que são inseridos por vez das tabelas back-end. Uma chamada para `pull` dados, em seguida, criaria lote backup de dados, com base neste tamanho de página, até que não há nenhuma mais registros retirar.

É possível configurar um tamanho de página usando **MSPullSettings** , conforme mostrado abaixo. O tamanho de página padrão é 50, e o exemplo a seguir altera para 3.

Você pode configurar um tamanho de página diferentes por razões de desempenho. Se você tiver um grande número de registros de dados pequeno, um tamanho de página alta reduz o número de ida e volta do servidor. 

Esta configuração controla apenas o tamanho de página no lado do cliente. Se o cliente solicitar um tamanho de página maior que o back-end aplicativos Mobile oferece suporte, o tamanho da página está limitado ao máximo que back-end está configurado para oferecer suporte. 

Esta configuração também é o _número_ de registros de dados, não o _tamanho em bytes_.

Se você aumentar o tamanho de página de cliente, [você também deve aumentar o tamanho da página no servidor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Objetivo-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Rápido**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Como: inserir dados

Para inserir uma nova linha de tabela, criar um `NSDictionary` e invocar `table insert`. Se [Esquema dinâmico] estiver habilitada, o serviço de aplicativo do Azure móvel back-end gera automaticamente novas colunas com base na `NSDictionary`.

Se `id` não for fornecido, o back-end gera automaticamente uma nova identificação exclusiva. Fornecer o seu próprio `id` usar o email endereços, nomes de usuário, ou seu próprio personalizado valores como ID. Fornecer sua própria identificação pode facilitar a lógica de banco de dados comerciais e junções.

O `result` contém o novo item que foi inserido. Dependendo de sua lógica de servidor, ele pode ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

**Objetivo-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Rápido**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Como: modificar dados

Para atualizar uma linha existente, modificar um item e chamada `update`:

**Objetivo-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Rápido**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Como alternativa, fornece a ID da linha e o campo atualizado:

**Objetivo-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Rápido**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o `id` atributo deve ser definido ao fazer atualizações.

##<a name="deleting"></a>Como: excluir dados

Para excluir um item, invocar `delete` com o item:

**Objetivo-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Rápido**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Como alternativa, exclua, fornecendo um ID de linha:

**Objetivo-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Rápido**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o `id` atributo deve ser definido quando tomada exclui.

##<a name="customapi"></a>Como: chamar API personalizado

Com uma API personalizada, você pode expor qualquer funcionalidade de back-end. Não é necessário mapear para uma operação de tabela. Não apenas você obtenha mais controle sobre mensagens, você pode até mesmo leitura/conjunto cabeçalhos e altere o formato de corpo de resposta. Para saber como criar uma API personalizada no back-end, leia [APIs personalizado](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para chamar uma API personalizada, chame `MSClient.invokeAPI`. A solicitação e resposta conteúda são tratadas como JSON. Usar outros tipos de mídia, [usar a outra sobrecarga do `invokeAPI` ] [ 5].  Para fazer um `GET` solicitação, em vez de um `POST` solicitar, parâmetro set `HTTPMethod` para `"GET"` e parâmetro `body` para `nil` (desde solicitações GET não têm corpos de mensagens.) Se sua API personalizado oferece suporte a outros verbos HTTP, altere `HTTPMethod` adequadamente.

**Objetivo-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Rápido**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Como: registrar modelos de envio enviar notificações de várias plataformas

Para registrar modelos, passe modelos com seu método de **client.push registerDeviceToken** em seu aplicativo de cliente.

**Objetivo-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Rápido**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Os modelos são do tipo NSDictionary e podem conter vários modelos no seguinte formato:

**Objetivo-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Rápido**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as marcas são removidas da solicitação de segurança.  Para adicionar marcas a instalações ou modelos dentro de instalações, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel][4].  Para enviar notificações usando esses modelos registrados, trabalhar com [APIs de Hubs de notificação][3].

##<a name="errors"></a>Como: tratar erros

Quando você chamar um serviço de aplicativo do Azure de móvel back-end, o bloco de conclusão contém um `NSError` parâmetro. Quando ocorre um erro, este parâmetro é não nulo. No seu código, você deve verificar esse parâmetro e manipular o erro conforme necessário, como demonstrou nos trechos de código anterior.

O arquivo [`<WindowsAzureMobileServices/MSError.h>`] [6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`, e `MSErrorServerItemKey`. Para obter mais dados relacionados para o erro:

**Objetivo-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Rápido**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o arquivo define constantes para cada código de erro:

**Objetivo-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Rápido**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como: autenticar usuários com a biblioteca de autenticação do Active Directory

Você pode usar a biblioteca de autenticação do Active Directory (ADAL) entre os usuários em seu aplicativo usando o Active Directory do Azure. Autenticação de fluxo de cliente usando um provedor de identidade SDK é preferível usando o `loginWithProvider:completion:` método.  Autenticação de fluxo de cliente fornece uma aparência mais nativa do UX e permite a personalização adicional.

1. Configurar seu back-end do aplicativo móvel para entrar no AAD seguindo [como configurar o serviço de aplicativo para o logon do Active Directory] [ 7] tutorial. Certifique-se concluir a etapa opcional de registro de um aplicativo cliente nativo. Para iOS, recomendamos que o redirecionamento URI é do formulário `<app-scheme>://<bundle-id>`. Para obter mais informações, consulte o [início rápido do iOS ADAL][8].

2. Instale o ADAL usando Cocoapods. Edite seu Podfile para incluir a seguinte definição, substituindo o **Seu projeto** com o nome do seu projeto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e o Pod:

        pod 'ADALiOS'

3. Usando o Terminal, execute `pod install` do diretório que contém o projeto e abra o espaço de trabalho Xcode gerado (não o projeto).

4. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que você está usando. Em cada um, verifique estes substituições:

    * Substitua o nome do locatário na qual você provisionado seu aplicativo **Inserir-autoridade-HERE** . O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir da guia de domínio no seu Azure Active Directory no [Azure clássico portal].
    * Substitua **Inserir-recurso-ID-HERE** com a ID do cliente para seu back-end do aplicativo móvel. Você pode obter a ID do cliente da guia **Avançado** em **Configurações do Azure Active Directory** no portal.
    * Substitua **Inserir-cliente-ID-HERE** com a ID do cliente que você copiou do aplicativo cliente nativo.
    * Substitua **Inserir-REDIRECT-URI-HERE** com ponto de extremidade de _/.auth/login/done_ do seu site, usando o esquema HTTPS. Este valor deve ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

**Objetivo-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Rápido**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Como: autenticar usuários com o SDK do Facebook para iOS

Você pode usar o SDK do Facebook para iOS entrar usuários em seu aplicativo usando o Facebook.  Usando a autenticação de um fluxo de cliente é preferível usando o `loginWithProvider:completion:` método.  A autenticação do fluxo de cliente fornece uma aparência mais nativa do UX e permite a personalização adicional.

1. Configurar seu back-end do aplicativo móvel para entrar no Facebook seguindo [como configurar o serviço de aplicativo para Facebook login] [ 9] tutorial.

2. Instalar o SDK do Facebook para iOS seguindo o [SDK do Facebook para iOS - Introdução] [ 10] documentação. Em vez de criar um aplicativo, você pode adicionar plataforma iOS seu registro existente. 

3. Documentação do Facebook inclui alguns códigos de objetivo-C no representante de aplicativo. Se você estiver usando **rápido**, você pode usar as traduções a seguir para AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Além de adicionar `FBSDKCoreKit.framework` ao seu projeto, também adicionar uma referência a `FBSDKLoginKit.framework` da mesma maneira. 

4. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que você está usando. 

**Objetivo-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Rápido**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Como: autenticar usuários com tecidos Twitter para iOS

Você pode usar tecidos para iOS entrar usuários em seu aplicativo usando Twitter. Autenticação de fluxo de cliente é preferível usando o `loginWithProvider:completion:` método, pois ela fornece uma aparência mais nativa do UX e permite a personalização adicional.

1. Configure seu back-end do aplicativo móvel para entrar no Twitter seguindo o tutorial de [como configurar o serviço de aplicativo para o logon do Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Adicione estrutura ao projeto seguindo a documentação de [tecidos para iOS - guia de Introdução] e configurando TwitterKit.

    > [AZURE.NOTE] Por padrão, o tecidos criam um aplicativo do Twitter para você. Você pode evitar a criação de um aplicativo registrando-se a chave do consumidor e o segredo do consumidor criado anteriormente usando os trechos de código a seguir.  Como alternativa, você pode substituir os valores de chave do consumidor e segredo do consumidor que você fornecer o serviço de aplicativo com os valores que você vê no [Painel de tecidos]. Se você escolher essa opção, certifique-se de definir a URL de retorno de chamada para um valor de espaço reservado, como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Se você optar por usar as senhas que você criou anteriormente, adicione o seguinte código ao seu representante de aplicativo:
    
    **Objetivo-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Rápido**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que você está usando. 

**Objetivo-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Rápido**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Como: autenticar os usuários com o Google entrar SDK para iOS

Você pode usar o Google entrar SDK para iOS entrar usuários em seu aplicativo usando uma conta do Google.  Google anunciado recentemente alterações em suas políticas de segurança OAuth.  Essas alterações de política exigem o uso do Google SDK no futuro.

1. Configure seu back-end do aplicativo móvel do Google entrar seguindo o tutorial de [como configurar o serviço de aplicativo para o logon do Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Instale o SDK do Google para iOS seguindo a documentação do [Google entrar para iOS - iniciar a integração](https://developers.google.com/identity/sign-in/ios/start-integrating) . Você pode ignorar a seção "Autenticar com um servidor back-end".

3. Adicione o seguinte ao seu representante `signIn:didSignInForUser:withError:` método, de acordo com o idioma que você está usando.

**Objetivo-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Rápido**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Certificar-se de que você também adicione o seguinte ao `application:didFinishLaunchingWithOptions:` no seu representante de aplicativo, substituindo "SERVER_CLIENT_ID" com a mesma ID que você usou para configurar o serviço de aplicativo na etapa 1.

**Objetivo-C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Rápido**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Adicione o seguinte código ao seu aplicativo em um UIViewController que implementa o `GIDSignInUIDelegate` protocolo, de acordo com o idioma que você está usando.  Você está desconectado antes sendo conectado novamente e, embora você não precise inserir suas credenciais novamente, você verá uma caixa de diálogo de consentimento.  Somente chame este método quando o token de sessão expirou.
 
 **Objetivo-C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Rápido**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Início rápido de aplicativos móveis Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinâmica]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Painel de estrutura]: https://www.fabric.io/home
[Tecidos para iOS - guia de Introdução]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
