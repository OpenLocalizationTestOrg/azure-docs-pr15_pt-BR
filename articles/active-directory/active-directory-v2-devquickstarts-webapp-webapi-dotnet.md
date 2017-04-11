<properties
    pageTitle="Azure AD v 2.0 .NET Web App | Microsoft Azure"
    description="Como criar um aplicativo do .NET MVC Web web chamadas serviços usando contas da Microsoft e trabalho pessoal ou estudante para entrar."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Chamando um API da web a partir de um aplicativo da web de .NET

Com o ponto de extremidade de v 2.0, você pode adicionar rapidamente autenticação a seus aplicativos web e APIs da web com suporte para ambas as contas pessoais do Microsoft e contas corporativas ou de estudante.  Aqui, estamos criará um aplicativo web do MVC que assina usuários usando OpenID conectar, com ajuda do middleware OWIN da Microsoft.  O web app irá obter tokens de acesso OAuth 2.0 para uma web api protegidos por 2.0 OAuth que permite criar, ler e excluir em uma determinada do usuário "lista de tarefas pendentes".

Este tutorial se concentrará principalmente na usando MSAL para adquirir e usar os tokens de acesso em um aplicativo web, descrito em completo [aqui](active-directory-v2-flows.md#web-apps).  Como pré-requisitos, talvez você queira primeiro aprender como [Adicionar entrar básica para um aplicativo web](active-directory-v2-devquickstarts-dotnet-web.md) ou como proteger [uma web API corretamente](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Baixe o código de amostra

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Como alternativa, você pode [baixar o aplicativo concluído como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou clonar o aplicativo concluído:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrar um aplicativo
Criar um novo aplicativo no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **Id de aplicativo** atribuída ao seu aplicativo, você precisará dela em breve.
- Criar um **Aplicativo segredo** o tipo de **senha** e copiar até seu valor posteriormente
- Adicione a plataforma de **Web** para o aplicativo.
- Insira o correto **Redirecionar URI**. O uri de redirecionamento indica ao Azure AD onde respostas de autenticação devem ser direcionadas, o padrão para este tutorial é `https://localhost:44326/`.


## <a name="install-owin"></a>Instalar o OWIN
Adicione os pacotes de NuGet middleware OWIN para o `TodoList-WebApp` projeto usando o Package Manager Console.  A middleware OWIN será usado para emitir solicitações de entrada e saídas, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Entrar do usuário
Agora, configure a middleware OWIN para usar o [protocolo de autenticação de OpenID conectar](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Abrir o `web.config` arquivo na raiz do `TodoList-WebApp` do projeto e insira os valores de configuração do seu aplicativo no `<appSettings>` seção.
    -   O `ida:ClientId` é a **Id de aplicativo** atribuída ao seu aplicativo no portal do registro.
    - O `ida:ClientSecret` é o **Segredo do aplicativo** que você criou no portal do registro.
    -   O `ida:RedirectUri` é o **Uri redirecionar** inseridas no portal.
- Abrir o `web.config` arquivo na raiz do `TodoList-Service` do projeto e substitua o `ida:Audience` com a mesma **Id de aplicativo** acima.


- Abra o arquivo `App_Start\Startup.Auth.cs` e adicionar `using` instruções para as bibliotecas do acima.
- No mesmo arquivo, implementar o `ConfigureAuth(...)` método.  Os parâmetros que você fornecer em `OpenIDConnectAuthenticationOptions` servirá como coordenadas para seu aplicativo para se comunicar com o Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Use MSAL para obter tokens de acesso
No `AuthorizationCodeReceived` notificação, queremos usar [2.0 OAuth juntamente com OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) para resgatar a authorization_code para um token de acesso para o serviço de lista de tarefas pendentes.  MSAL pode facilitar esse processo para você:

- Primeiro, instale a versão de visualização do MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- E adicionar outro `using` instrução para o `App_Start\Startup.Auth.cs` arquivo para MSAL.
- Agora, adicione um novo método, o `OnAuthorizationCodeReceived` manipulador de eventos.  Esse manipulador usará MSAL adquirir um token de acesso para a API de lista de tarefas pendentes e armazenará o token em cache de token do MSAL posteriormente:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- Em aplicativos web, MSAL tem um cache de token extensível que pode ser usado para armazenar tokens.  Este exemplo implementa a `NaiveSessionCache` que usa o armazenamento de sessão de http para tokens de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Chamar a API da Web
Agora é hora de realmente usar o access_token que você adquiriu na etapa 3.  Abra o aplicativo web `Controllers\TodoListController.cs` arquivo, que torna todas as solicitações CRUD para a API de lista de tarefas pendentes.

- Você pode usar MSAL novamente aqui para buscar access_tokens do cache de MSAL.  Primeiro, adicione um `using` política do MSAL a esse arquivo.

    `using Microsoft.Identity.Client;`

- No `Index` ação, use MSAL `AcquireTokenSilentAsync` método para obter um access_token que podem ser usadas para ler dados do serviço de lista de tarefas pendentes:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- O exemplo adiciona o token resultante a solicitação GET HTTP como o `Authorization` cabeçalho, que usa o serviço de lista de tarefas pendentes para autenticar a solicitação.
- Se o serviço de lista de tarefas pendentes retorna um `401 Unauthorized` resposta, o access_tokens no MSAL tornaram inválido por algum motivo.  Nesse caso, você deve soltar qualquer access_tokens do cache de MSAL e mostrar uma mensagem que eles talvez precise entrar novamente, que irá reiniciar o fluxo de aquisição token de usuário.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- Da mesma forma, se MSAL for não é possível retornar um access_token por algum motivo, você deve instruir o usuário entrar novamente.  Isso é tão simple quanto captura qualquer `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- A exata mesmo `AcquireTokenSilentAsync` chamada é implementd no `Create` e `Delete` ações.  Em aplicativos web, você pode usar esse método MSAL para obter access_tokens sempre que você precisá-las em seu aplicativo.  MSAL executarão de aquisição, cache e atualizando tokens para você.

Finalmente, criar e executar o aplicativo!  Entre com uma Account da Microsoft ou uma conta do Azure AD e observe como a identidade do usuário é refletida na barra de navegação superior.  Adicionar e excluir alguns itens da lista de tarefas pendentes do usuário para ver que o 2.0 OAuth protegidas chamadas de API em ação.  Agora você tem um aplicativo web web API, ambas & protegido usando protocolos padrão do setor, que podem autenticar usuários com suas contas pessoais e de trabalho/escola.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Próximas etapas

Para obter recursos adicionais, consulte:
- [O guia do desenvolvedor v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Marca de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
