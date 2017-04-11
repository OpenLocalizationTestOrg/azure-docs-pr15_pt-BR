<properties
    pageTitle="Azure AD v 2.0 .NET Web App | Microsoft Azure"
    description="Como criar um aplicativo Web MVC de .NET que assina contas de trabalho ou da escola e usuários com os dois Account pessoal da Microsoft."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Adicionar entrada ao aplicativo web do MVC .NET

Com o ponto de extremidade de v 2.0, você pode adicionar rapidamente autenticação seus aplicativos web com suporte para ambas as contas pessoais do Microsoft e contas de trabalho ou da escola.  Nos aplicativos web do ASP.NET, você pode fazer isso usando middleware OWIN da Microsoft incluído no .NET Framework 4,5.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

 Aqui será criamos um aplicativo web que usa OWIN para entrar o usuário, exibir algumas informações sobre o usuário e assinar o usuário sair do aplicativo.
 
 ## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

O aplicativo concluído é fornecido no final deste tutorial também.

## <a name="register-an-app"></a>Registrar um aplicativo
Criar um novo aplicativo no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **Id de aplicativo** atribuída ao seu aplicativo, você precisará dela em breve.
- Adicione a plataforma de **Web** para o aplicativo.
- Insira o correto **Redirecionar URI**. O uri de redirecionamento indica ao Azure AD onde respostas de autenticação devem ser direcionadas, o padrão para este tutorial é `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalar e configurar a autenticação de OWIN
Aqui, estamos vai configurar o middleware OWIN para usar o protocolo de autenticação OpenID se conectar.  OWIN será usado para emitir solicitações de entrada e saídas, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-   Para começar, abra o `web.config` arquivo na raiz do projeto e insira os valores de configuração do seu aplicativo no `<appSettings>` seção.
    -   O `ida:ClientId` é a **Id de aplicativo** atribuída ao seu aplicativo no portal do registro.
    -   O `ida:RedirectUri` é o **Uri redirecionar** inseridas no portal.

-   Em seguida, adicione os pacotes de NuGet middleware OWIN para o projeto usando o Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Adicionar um "OWIN inicialização classe" ao projeto chamado `Startup.cs` direita, clique no projeto--> **Adicionar** --> **Novo Item** --> Procurar por "OWIN".  A middleware OWIN chamará o `Configuration(...)` método quando seu aplicativo é iniciado.
-   Altere a declaração de classe para `public partial class Startup` -nós já tiver implementado parte dessa classe para você em outro arquivo.  No `Configuration(...)` método, fazer uma chamada para ConfigureAuth(...) para configurar a autenticação de seu aplicativo web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Abra o arquivo `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros que você fornecer em `OpenIdConnectAuthenticationOptions` servirá como coordenadas para seu aplicativo para se comunicar com o Azure AD.  Você também precisará configurar a autenticação de Cookie - o middleware OpenID conectar usa cookies nos bastidores.

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
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação
Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade de v 2.0 usando o protocolo de autenticação OpenID se conectar.  OWIN tratado de todos os detalhes de boa aparência de criar mensagens de autenticação, validando tokens do Azure AD e mantendo a sessão do usuário.  Tudo o que resta é dar aos usuários uma maneira de entrar e sair.

- Você pode usar autorizar marcas em seus controladores para exigir que o usuário entra antes de acessar uma determinada página.  Abrir `Controllers\HomeController.cs`e adicione o `[Authorize]` marca para o controlador de sobre.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   Você também pode usar OWIN diretamente emitir solicitações de autenticação de dentro do seu código.  Abrir `Controllers\AccountController.cs`.  As ações SignIn() e SignOut(), emita desafio OpenID conectar e solicitações de saída, respectivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   Agora, abra `Views\Shared\_LoginPartial.cshtml`.  Isso é onde você vai mostrar o usuário links de entrada e saída do seu aplicativo e imprima o nome do usuário em uma exibição.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Exibir informações do usuário
Ao autenticar usuários com OpenID conectar, o ponto de extremidade de v 2.0 retorna um id_token para o aplicativo que contém [declarações](active-directory-v2-tokens.md#id_tokens)ou declarações sobre o usuário.  Você pode usar essas declarações para personalizar seu aplicativo:

- Abrir o `Controllers\HomeController.cs` arquivo.  Você pode acessar declarações do usuário em seus controladores via o `ClaimsPrincipal.Current` objeto de segurança.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Executar

Finalmente, criar e executar o aplicativo!   Entre com uma Account da Microsoft ou uma conta corporativa ou escolar e observe como a identidade do usuário é refletida na barra de navegação superior.  Agora você tem um aplicativo web protegido usando protocolos padrão do setor que podem autenticar usuários com suas contas pessoais e de trabalho/escola.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Próximas etapas

Agora, você pode mover em tópicos mais avançados.  Talvez você queira experimentar:

[Proteger uma API Web com o o ponto de extremidade de v 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obter recursos adicionais, consulte:
- [O guia do desenvolvedor v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Marca de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
