<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Como criar um aplicativo web que chama um web API usando Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>B2C do Azure AD: Ligar uma web API de um aplicativo da web de .NET

Usando B2C Azure Active Directory (AD Azure), você pode adicionar recursos de gerenciamento de identidade de autoatendimento poderosos aos seus aplicativos web e web APIs em algumas etapas curtas. Este artigo abordará como criar um aplicativo da web de modelo de exibição de controlador de .NET (MVC) "lista de tarefas pendentes" que chama um web API usando tokens de portador

Este artigo não aborda como implementar entrar, inscrição e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na web chamada APIs depois que o usuário já está autenticado. Se você ainda não começou, você deve começar com o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md) aprender as Noções básicas do Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários.  Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais.  Se você não tenha um, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. Nesse caso, o web app e a API web serão representado por um único **ID do aplicativo**, pois elas compõem um aplicativo lógico. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de:

- Inclua um **web app da API web** no aplicativo.
- Insira `https://localhost:44316/` como uma **URL de resposta**. É a URL padrão para este exemplo de código.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você também precisará isso mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo web contém três experiências de identidade: inscrever-se, entrar e Editar perfil. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando você cria as três políticas, certifique-se a:

- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha as declarações de aplicativo **nome para exibição** e a **ID de objeto** em cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Ele deve ter o prefixo `b2c_1_`. Posteriormente, você precisará os nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após ter criado suas políticas de três, você está pronto para criar seu aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam no Azure AD B2C, comece com o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixe o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para criar a amostra enquanto você trabalha, você pode [baixar o projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

O aplicativo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo. sln Visual Studio para começar.

## <a name="configure-the-task-web-app"></a>Configurar o aplicativo da web de tarefa

Para obter `TaskWebApp` para se comunicar com o Azure AD B2C, você precisará fornecer alguns parâmetros comuns. No `TaskWebApp` projeto, abrir o `web.config` arquivo na raiz do projeto e substitua os valores no `<appSettings>` seção. Você pode deixar o `AadInstance`, `RedirectUri`, e `TaskServiceUrl` valores como eles estão.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Obter tokens de acesso e chamar a API de tarefa

Esta seção aborda como usar o token recebido durante entrar com Azure AD B2C para acessar uma web API que também está protegido com o Azure AD B2C.

Este artigo não aborda os detalhes de como proteger a API. Para saber como um web API autentica com segurança solicitações usando Azure AD B2C, confira o [web API Introdução artigo](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Salvar o sinal no token

Primeiro, autenticar o usuário (usando qualquer uma das suas políticas) e receber um token de entrada do Azure AD B2C.  Se você não tiver certeza de como executar políticas, volte e tente o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md) para aprender as Noções básicas do Azure AD B2C.

Abra o arquivo `App_Start\Startup.Auth.cs`.  Não há uma alteração importante que você deve fazer o `OpenIdConnectAuthenticationOptions` -deverá definir `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Obter um token nos controladores

O `TasksController` é responsável por comunicar-se com a web API, enviando solicitações HTTP à API para ler, criar e excluir tarefas.  Becuase que API é protegido por Azure AD B2C, é necessário primeiro recuperar o token que você salvou na etapa acima.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

O `BootstrapContext` contém o sinal no símbolo que você adquiriu executando uma das suas políticas de B2C.

### <a name="read-tasks-from-the-web-api"></a>Ler tarefas da web API

Quando você tiver um token, você pode anexá-lo em HTTP `GET` solicitação in a `Authorization` cabeçalho para chamar com segurança `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Criar e excluir tarefas na web API

Siga o mesmo padrão quando você envia `POST` e `DELETE` solicitações na Web API, usando o `BootstrapContext` recuperar o sinal no símbolo. Podemos implementado a ação criar para você. Você pode tentar terminando a ação de exclusão em `TasksController.cs`.

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Finalmente, criar e executar o aplicativo. Inscrever-se e entrar e criar tarefas para o usuário conectado. Sair e entrar como um usuário diferente. Crie tarefas para esse usuário. Observe como as tarefas são armazenados por usuário a API, porque a API extrai a identidade do usuário do símbolo que ele recebe.

Para referência, o exemplo concluído [é fornecido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
