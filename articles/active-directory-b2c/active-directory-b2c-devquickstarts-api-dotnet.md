<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Como construir uma API de Web .NET usando Azure Active Directory B2C, protegidos usando os tokens de acesso OAuth 2.0 para autenticação."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure B2C diretório ativo: Construir uma API da web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com B2C do Azure Active Directory (AD Azure), você pode proteger um web API usando tokens de acesso OAuth 2.0. Esses tokens permitir que seus aplicativos cliente que usam o Azure AD B2C para autenticar à API. Este artigo mostra como criar uma API de "lista de tarefas pendentes".NET controlador de exibição de modelo MVC () que permite aos usuários tarefas CRUD. Web API é protegido através do Azure AD B2C e permite apenas usuários autenticados ao gerenciar sua lista de tarefas pendentes.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você não tenha um, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de:

- Inclua um **aplicativo web** ou **web API** no aplicativo.
- Use o **identificador de recursos uniforme de redirecionar** `https://localhost:44316/` para o aplicativo web. Este é o local padrão do cliente web app para este exemplo de código.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você precisará-la mais tarde.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). O cliente nesse exemplo de código contém três experiências de identidade: inscrever-se, entrar e Editar perfil. Você precisará criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando você cria sua três políticas, certifique-se a:

- Escolha o **ID de usuário inscrição** ou **Email inscrição** na lâmina de provedores de identidade.
- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha declarações de **nome para exibição** e a **ID do objeto** como declarações de aplicativo para cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Posteriormente, você precisará esses nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após ter criado com êxito as três políticas, você está pronto para criar seu aplicativo.

## <a name="download-the-code"></a>Baixe o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para criar a amostra enquanto você trabalha, você pode [baixar um projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

O aplicativo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo. sln Visual Studio para começar. O arquivo de solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp`é um aplicativo da web MVC que o usuário interage com. `TaskService`é web de back-end do aplicativo API que armazena a lista de tarefas pendentes de cada usuário.

## <a name="configure-the-task-web-app"></a>Configurar o aplicativo da web de tarefa

Quando um usuário interage com `TaskWebApp`, o cliente envia solicitações ao Azure AD e obtém volta tokens que podem ser usados para chamar o `TaskService` API da web. Para entrar no usuário e obter tokens, você precisará fornecer `TaskWebApp` com algumas informações sobre seu aplicativo. No `TaskWebApp` projeto, abrir o `web.config` arquivo na raiz do projeto e substitua os valores no `<appSettings>` seção.  Você pode deixar o `AadInstance`, `RedirectUri`, e `TaskServiceUrl` valores como-é.

```
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
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artigo não aborda a construção a `TaskWebApp` cliente.  Para saber como criar um aplicativo web usando o Azure AD B2C, consulte [nosso tutorial do .NET web app](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteger a API

Quando você tiver um cliente que chama a API em nome de usuários, você pode proteger `TaskService` usando tokens de portador OAuth 2.0. Sua API pode aceitar e validar tokens usando Interface de Web aberta da Microsoft para a biblioteca de .NET (OWIN).

### <a name="install-owin"></a>Instalar o OWIN
Comece instalando o pipeline de autenticação OAuth OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Insira os detalhes de B2C
Abrir o `web.config` arquivo na raiz do `TaskService` do projeto e substitua os valores na `<appSettings>` seção. Esses valores serão usados em toda a biblioteca de API e OWIN.  Você pode deixar o `AadInstance` valor inalterado.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de inicialização OWIN
Adicionar uma classe de inicialização OWIN para o `TaskService` projeto chamado `Startup.cs`.  Clique com botão direito no projeto, selecione **Adicionar** e **Novo Item**e procure por OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurar autenticação OAuth 2.0
Abra o arquivo `App_Start\Startup.Auth.cs`e implementar o `ConfigureAuth(...)` método:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteger o controlador de tarefa
Depois que o aplicativo está configurado para usar autenticação OAuth 2.0, você pode proteger sua web API adicionando um `[Authorize]` marca para o controlador de tarefa. Este é o controlador onde toda manipulação de lista de tarefas pendentes ocorrerá, portanto você deve proteger o controlador inteiro no nível de classe. Você também pode adicionar o `[Authorize]` marca para ações individuais para controle mais refinado.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obter informações de usuário do token
`TasksController`armazena tarefas em um banco de dados onde cada tarefa tem um usuário associado a tarefa "proprietário". O proprietário é identificado por **ID do objeto do usuário**. (Isso é por que é necessário para adicionar a identificação do objeto como um aplicativo declarar em todas as suas políticas.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Finalmente, criar e executar ambos `TaskWebApp` e `TaskService`. Inscreva-se para o aplicativo usando um endereço de email ou nome de usuário. Criar algumas tarefas na lista de tarefas pendentes do usuário e observe como elas são mantidas na API do mesmo depois de você parar e reiniciar o cliente.

## <a name="edit-your-policies"></a>Editar suas políticas

Após você ter protegido uma API usando Azure AD B2C, você pode experimentar políticas do seu aplicativo e exibir os efeitos (ou falta dele) na API. Você pode manipular as declarações de aplicativo nas políticas e alterar as informações do usuário que está disponíveis na web API. As declarações que você adicionar estará disponíveis para sua web .NET MVC API na `ClaimsPrincipal` objeto, conforme descrito neste artigo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
