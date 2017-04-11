<properties
    pageTitle="Azure AD .NET Introdução | Microsoft Azure"
    description="Como criar um aplicativo Web MVC de .NET que se integra ao Azure AD para entrar."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>Entrada do aplicativo da Web do ASP.NET & sair com o Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna simples e direto para terceirização de gerenciamento de identidades do web app, fornecendo única entrada e saída com apenas algumas linhas de código.  Nos aplicativos web do Asp.NET, você pode fazer isso usando a implementação da middleware OWIN voltado para a comunidade incluído no .NET Framework 4,5 da Microsoft.  Aqui, usaremos OWIN para:
-   Entrar do usuário no aplicativo usando o Azure AD como provedor de identidade.
-   Exiba algumas informações sobre o usuário.
-   Assine o usuário sair do aplicativo.

Para fazer isso, você precisará:

1. Registrar um aplicativo com o Azure AD
2. Configure seu aplicativo para usar o pipeline de autenticação OWIN.
3. Use OWIN para emitir solicitações de entrada e saídas para Azure AD.
4. Imprima dados sobre o usuário.

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Você também será preciso um locatário do Azure AD no qual deseja registrar seu aplicativo.  Se você não tenha um, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar um aplicativo com o Azure AD*
Para ativar seu aplicativo autenticar os usuários, você precisará primeiro registrar um novo aplicativo no seu locatário.

- Entrar no Portal de gerenciamento do Azure.
- No painel de navegação à esquerda, clique no **Active Directory**.
- Selecione o locatário onde você deseja registrar o aplicativo.
- Clique na guia **aplicativos** e clique em Adicionar na parte inferior registradora.
- Siga os avisos e criar um novo **aplicativo Web e/ou WebAPI**.
    - O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   A **URL de logon** é a URL base do aplicativo.  Padrão de esqueleto é `https://localhost:44320/`.
    - O **URI de ID de aplicativo** é um identificador exclusivo para o seu aplicativo.  A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo,`https://contoso.onmicrosoft.com/my-first-aad-app`
- Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia Configurar.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar seu aplicativo para usar o pipeline de autenticação OWIN*
Aqui, estamos vai configurar o middleware OWIN para usar o protocolo de autenticação OpenID se conectar.  OWIN será usado para emitir solicitações de entrada e saídas, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-   Para começar, adicione os pacotes de NuGet middleware OWIN para o projeto usando o Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Adicionar uma classe de inicialização de OWIN ao projeto chamado `Startup.cs` direita, clique no projeto--> **Adicionar** --> **Novo Item** --> Procurar por "OWIN".  A middleware OWIN chamará o `Configuration(...)` método quando seu aplicativo é iniciado.
-   Altere a declaração de classe para `public partial class Startup` -nós já tiver implementado parte dessa classe para você em outro arquivo.  No `Configuration(...)` método, fazer uma chamada para ConfgureAuth(...) para configurar a autenticação de seu aplicativo web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra o arquivo `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros que você fornecer em `OpenIDConnectAuthenticationOptions` servirá como coordenadas para seu aplicativo para se comunicar com o Azure AD.  Você também precisará configurar a autenticação de Cookie - o middleware OpenID conectar usa cookies nos bastidores.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Por fim, abra o `web.config` arquivo na raiz do projeto e insira os valores de configuração na `<appSettings>` seção.
    -   Seu aplicativo `ida:ClientId` é o Guid que você copiou do Portal do Azure na etapa 1.
    -   O `ida:Tenant` é o nome do seu locatário do Azure AD, por exemplo, "contoso.onmicrosoft.com".
    -   Seu `ida:PostLogoutRedirectUri` indica ao Azure AD onde um usuário deve ser redirecionado após uma solicitação de saída for concluída com êxito.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. use OWIN para emitir solicitações de entrada e saídas para Azure AD*
Seu aplicativo agora está configurado corretamente para se comunicar com o Azure AD usando o protocolo de autenticação OpenID se conectar.  OWIN tratado de todos os detalhes de boa aparência de criar mensagens de autenticação, validando tokens do Azure AD e mantendo a sessão do usuário.  Tudo o que resta é dar aos usuários uma maneira de entrar e sair.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   Agora, abra `Views\Shared\_LoginPartial.cshtml`.  Isso é onde você vai mostrar o usuário links de entrada e saída do seu aplicativo e imprima o nome do usuário em uma exibição.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. exibir informações do usuário*
Ao autenticar usuários com OpenID conectar, o Azure AD retorna um id_token para o aplicativo que contém "declarações" ou declarações sobre o usuário.  Você pode usar essas declarações para personalizar seu aplicativo:

- Abrir o `Controllers\HomeController.cs` arquivo.  Você pode acessar declarações do usuário em seus controladores via o `ClaimsPrincipal.Current` objeto de segurança.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Finalmente, criar e executar o aplicativo!  Se você ainda não começou, agora é o tempo para criar um novo usuário em seu locatário com um *. domínio onmicrosoft.com.  Entre com aquele usuário e observe como a identidade do usuário é refletida na barra de navegação superior.  Sair e entrar novamente como outro usuário em seu locatário.  Se você se sente particularmente ambicioso, registrar e execute outra instância deste aplicativo (com seu próprio clientId), além de inspeção Consulte logon único em ação.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Agora, você pode mover em tópicos mais avançados.  Talvez você queira experimentar:

[Proteger uma Web API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
