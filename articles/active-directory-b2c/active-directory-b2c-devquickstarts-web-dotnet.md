<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Como criar um aplicativo web que tem entrar, inscrição, e gerenciamento de perfil usando o Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-net-web-app"></a>B2C do Azure AD: Criar um aplicativo da web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Usando B2C Azure Active Directory (AD Azure), você pode adicionar recursos de gerenciamento de identidade de autoatendimento poderosos para seu aplicativo web em algumas etapas curtos. Este artigo aborda como criar um aplicativo web .NET controlador de exibição de modelo MVC () que inclua usuário inscrição, entrar e gerenciamento de perfil. O aplicativo incluirá suporte a inscrição e entrar usando um nome de usuário ou o email e usando contas sociais como o Facebook e do Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais.  Se você não tenha um, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md).  Certifique-se de:

- Inclua um **web app da API web** no aplicativo.
- Insira `https://localhost:44316/` como um **Redirecionar URI**. É a URL padrão para este exemplo de código.
- Copiar a **ID do aplicativo** que está atribuído a seu aplicativo.  Você precisará dele mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: inscrever-se, entrar e Editar perfil. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C também suporta uma inscrição combinado ou sinal na diretiva que não é exibida neste tutorial.  O inscrição ou entrar política é mostrado [neste](active-directory-b2c-devquickstarts-web-dotnet-susi.md)tutorial equivalente.

Quando você cria as três políticas, certifique-se a:

- Escolha o **ID de usuário inscrição** ou **Email inscrição** na lâmina de provedores de identidade.
- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha a declaração de **nome de exibição** como um aplicativo declaração em cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Posteriormente, você precisará os nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar seu três políticas, você está pronto para criar seu aplicativo.  

## <a name="download-the-code-and-configure-authentication"></a>Baixar o código e configurar a autenticação

O código para este exemplo [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para criar a amostra enquanto você trabalha, você pode [baixar o projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

O exemplo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo. sln Visual Studio para começar.

Seu aplicativo se comunica com o Azure AD B2C enviando mensagens de autenticação que especificam a política que desejam executar como parte da solicitação HTTP. Para aplicativos da web de .NET, você pode usar middleware OWIN da Microsoft para enviar solicitações de autenticação OpenID conectar, executar políticas, gerenciar sessões de usuário e muito mais.

Para começar, adicione os pacotes de NuGet middleware OWIN ao projeto usando o Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Em seguida, abra o `web.config` arquivo na raiz do projeto e insira valores de configuração do seu aplicativo na `<appSettings>` seção, substituindo os valores existentes.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, adicione uma classe de inicialização OWIN ao projeto chamado `Startup.cs`. Clique com botão direito no projeto, selecione **Adicionar** e **Novo Item**e pesquise por "OWIN". **Certificar-se de alterar a declaração de classe para `public partial class Startup` **. Podemos implementado parte dessa classe para você em outro arquivo. A middleware OWIN chamará o `Configuration(...)` método quando seu aplicativo é iniciado. Nesse método, fazer uma chamada para `ConfigureAuth(...)`, onde você configurar a autenticação de seu aplicativo.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra o arquivo `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros que você fornecer em `OpenIdConnectAuthenticationOptions` servir como coordenadas para seu aplicativo para se comunicar com o Azure AD. Você também precisa configurar a autenticação de cookies. A middleware OpenID conectar usa cookies para manter sessões de usuário, entre outras coisas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Enviar solicitações de autenticação para Azure AD
Seu aplicativo agora está configurado corretamente para se comunicar com o Azure AD B2C usando o protocolo de autenticação OpenID se conectar.  OWIN tratado de todos os detalhes de criar mensagens de autenticação, validando tokens do Azure AD e mantendo a sessão do usuário.  Tudo o que falta é Iniciar fluxo de cada usuário.

Quando um usuário seleciona **inscrever-se**, **entrar**ou **Editar perfil** no aplicativo da web, a ação associada é invocada em `Controllers\AccountController.cs`. Em cada caso, você pode usar métodos OWIN internos disparar a política direita:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Você também pode usar um `[Authorize]` marca no seu controladores que requer a execução de uma política de certas se o usuário não está conectado. Abrir `Controllers\HomeController.cs` e adicione o `[Authorize]` marca para o controlador de declarações.  OWIN selecionará a última diretiva configurada para ser executado quando a marca de autorizar é chamada.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Você também pode usar OWIN para sair do usuário do aplicativo. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Exibir informações do usuário
Quando você autentica usuários usando a conexão de OpenID, o Azure AD retorna um token de ID para o aplicativo que contém **declarações**. Estas são declarações sobre o usuário. Você pode usar declarações para personalizar o seu aplicativo.  

Abrir o `Controllers\HomeController.cs` arquivo. Você pode acessar declarações de usuário em seus controladores via o `ClaimsPrincipal.Current` objeto de segurança.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Você pode acessar qualquer declaração que seu aplicativo recebe da mesma maneira.  Uma lista de todas as declarações que recebe o aplicativo está disponível para você na página **declarações** .

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Por fim, você pode criar e executar o aplicativo. Inscreva-se para o aplicativo usando um nome de usuário ou endereço de email. Sair e entrar novamente o mesmo usuário. Edite perfil do usuário. Sair e inscrever-se como um usuário diferente. Observe que as informações exibidas na guia **declarações** correspondem às informações que você configurou em suas políticas.

## <a name="add-social-idps"></a>Adicionar IDPs sociais

Atualmente, o aplicativo suporta somente o usuário inscrição e entrar usando **contas locais**. Estas são contas armazenadas no seu diretório B2C que usam um nome de usuário e senha. Usando o Azure AD B2C, você pode adicionar suporte para outros **provedores de identidade** (IDPs) sem alterar nenhum código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nesses artigos. Para cada IDP desejar oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma identificação de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os provedores de identidade ao seu diretório B2C, você precisa editar cada uma das suas políticas de três para incluir as novas IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar suas políticas, execute o aplicativo novamente.  Você deve ver os novo IDPs adicionados como entrar e opções de inscrição em cada uma das sua identidade experiências.

Você pode experimentar com suas políticas e observar o efeito sobre o aplicativo de amostra. Adicionar ou remover IDPs, manipular declarações de aplicativo ou alterar os atributos de inscrição. Experimente até que você possa ver como políticas, solicitações de autenticação e OWIN unir.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
