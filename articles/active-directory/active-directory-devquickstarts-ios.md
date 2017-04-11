<properties
    pageTitle="Azure iOS AD Introdução | Microsoft Azure"
    description="Como criar um aplicativo iOS que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar o Azure AD em um aplicativo do iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL, para os clientes do iOS que precise acessar recursos protegidos.  Única finalidade do ADAL na vida é tornar mais fácil para o aplicativo obter tokens de acesso.  Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de lista de tarefas pendentes do objetivo C que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph usando o [protocolo de autenticação de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório usuários com um alias de determinado.

Para criar o aplicativo de trabalho concluído, você precisará:

2. Registre seu aplicativo com o Azure AD.
3. Instalar e configurar ADAL.
5. Use ADAL para obter tokens do Azure AD.

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Você também será preciso um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

> [AZURE.TIP] Experimente a visualização do nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/iOS) que ajudarão você a começar a trabalhar com o Active Directory do Azure em poucos minutos!  O portal do desenvolvedor o orientará durante o processo de registrar um aplicativo e integrar Azure AD seu código.  Quando tiver terminado, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que podem aceitar tokens e executar a validação. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. determine qual será o URI redirecionar para iOS*

Para iniciar com segurança seus aplicativos em determinados cenários SSO solicitamos que você crie um **URI redirecionar** em um formato específico. Um URI redirecionar é usado para garantir que os tokens retornam para o aplicativo correto que solicitado para eles.

O formato de iOS para um URI redirecionar é:

```
<app-scheme>://<bundle-id>
```

-   **esquema de aap** - isso é registrado no seu projeto XCode. É como outros aplicativos podem chamar você. Você pode encontrar isso em Info.plist -> tipos de URL -> identificador de URL. Você deve criar um se você ainda não tiver um ou mais configurado.
-   **id do pacote** - este é o identificador de pacote localizado em "identidade" un suas configurações de projeto no XCode.

Um exemplo para este código de início rápido seria: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. Registre o aplicativo de DirectorySearcher*
Para habilitar o aplicativo obter tokens, você precisará primeiro registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Azure AD gráfico:

-   Entrar no Portal de gerenciamento do Azure
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **Aplicativo cliente nativo**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   O **Uri redirecionar** é uma combinação de esquema e a cadeia de caracteres que Azure AD usará para retornar respostas token.  Insira um valor específico para o seu aplicativo com base nas informações acima.
-   Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia **Configurar** .
- Também na guia **Configurar** , localize a seção "Permissões para outros aplicativos".  Para o aplicativo "Azure Active Directory", adicione a permissão de **diretório da organização o acesso** em **Permissões delegados**.  Isso permitirá que seu aplicativo para consultar a API de gráfico para os usuários.

## <a name="3-install--configure-adal"></a>*3. Instale e Configure ADAL*
Agora que você tem um aplicativo no Azure AD, você pode instalar ADAL e escrever seu código relacionadas a identidade.  Ordem de ADAL sejam capazes de se comunicar com o Azure AD, você precisa fornecê-lo com algumas informações sobre seu registro de aplicativo.
-   Comece adicionando ADAL para o projeto de DirectorySearcher usando Cocapods.

```
$ vi Podfile
```
Adicione o seguinte para este podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Carrega agora o podfile usando cocoapods. Isso criará um novo espaço de trabalho XCode você carregará.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   No projeto de início rápido, abra o arquivo plist `settings.plist`.  Substitua os valores dos elementos na seção para refletir os valores que você insere no Portal do Azure.  Seu código fará referência esses valores sempre que ele usa ADAL.
    -   O `tenant` é o domínio de seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é o clientId do aplicativo que você copiou do portal.
    -   O `redirectUri` é o redirecionamento url registrado no portal.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. usar ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que seu aplicativo precisa de um token de acesso, ele simplesmente chama um completionBlock `+(void) getToken : `, e ADAL faz o resto.  

-   No `QuickStart` project, abrir `GraphAPICaller.m` e localize o `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` comentário, na parte superior.  Isso é onde você passa ADAL as coordenadas por meio de um CompletionBlock para se comunicar com o Azure AD e informá-lo como tokens de cache.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Agora, precisamos usar esse token para procurar por usuários no gráfico. Encontre o `// TODO: implement SearchUsersList` comente este método faz uma solicitação GET à API do Azure AD Graph a consulta para os usuários cuja UPN começa com o termo de pesquisa fornecidos.  Mas para consultar a API do gráfico, você precisa incluir uma access_token na `Authorization` cabeçalho da solicitação - é onde entra ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Quando o seu aplicativo solicita um token chamando `getToken(...)`, ADAL tentará retornar um token sem pedir a credenciais de usuário.  Se ADAL determinar que o usuário precisa entrar para obter um token, ele será exibir uma caixa de diálogo de login, coletar as credenciais do usuário e retornar um token durante a autenticação bem-sucedida.  Se ADAL for não é possível retornar um token por algum motivo, ele irá acionar um `AdalException`.
- Observe que o `AuthenticationResult` objeto contém um `tokenCacheStoreItem` objeto que pode ser usado para coletar informações sobre o seu aplicativo talvez seja necessário.  No QuickStart, `tokenCacheStoreItem` é usada para determinar se authenitcation já ocorreu.


## <a name="step-5-build-and-run-the-application"></a>Etapa 5: Criar e executar o aplicativo



Parabéns! Agora você tem um aplicativo iOS de trabalho que tem a capacidade de autenticar os usuários, com segurança chamar APIs de Web usando OAuth 2.0 e obtenha informações básicas sobre o usuário.  Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários.  Executar o aplicativo de início rápido e entrar em um desses usuários.  Procurar por outros usuários com base em seu UPN.  Feche o aplicativo e execute-o novamente.  Observe como a sessão do usuário permanecerá intacta.

ADAL facilita a incorporar todos esses recursos de identidade comuns em seu aplicativo.  Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais.  Tudo o que você realmente precisa saber é uma única chamada de API, `getToken`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Cenários adicionais
Agora você pode mover-se em cenários adicionais.  Talvez você queira experimentar:

- [Proteger uma Web Node API com Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
- Saiba [como habilitar SSO entre aplicativo IOS usando ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
