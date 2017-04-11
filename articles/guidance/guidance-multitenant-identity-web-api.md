<properties
   pageTitle="Proteger uma web de back-end API em um aplicativo de vários locatários | Microsoft Azure"
   description="Como proteger um API da web de back-end"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="securing-a-backend-web-api-in-a-multitenant-application"></a>Proteger uma web de back-end API em um aplicativo de vários locatários

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

O aplicativo de [Pesquisas de Tailspin] usa um back-end da web API para gerenciar operações CRUD em pesquisas. Por exemplo, quando um usuário clica em "Meus pesquisas", o aplicativo da web envia uma solicitação HTTP na Web API:

```
GET /users/{userId}/surveys
```

Web API retorna um objeto JSON:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Web API não permitir solicitações anônimas, para que o aplicativo web deve autenticar usando tokens de portador OAuth 2.

> [AZURE.NOTE] Esse é um cenário de servidor para servidor. O aplicativo não faz quaisquer chamadas AJAX à API do cliente do navegador.

Há duas abordagens principais que você pode realizar:

- Identidade de usuário delegado. O aplicativo da web autentica com a identidade do usuário.
- Identidade do aplicativo. O aplicativo da web autentica com sua ID de cliente, usando o fluxo de credencial de cliente OAuth2.

O aplicativo de Tailspin implementa identidade de usuário delegado. Aqui estão as principais diferenças:

**Identidade de usuário delegado**

- O token de portador enviado à web API contém a identidade do usuário.
- Web API toma decisões de autorização com base na identidade do usuário.
- O aplicativo web precisa tratar erros de 403 (proibido) da web API, se o usuário não está autorizado a executar uma ação.
- Normalmente, o aplicativo web ainda faz algumas decisões de autorização que afetam a interface do usuário, como mostrando ou ocultando elementos de interface do usuário.)
- A API web potencialmente pode ser usada pela clientes não confiáveis, como um aplicativo de JavaScript ou um aplicativo cliente nativo.

**Identidade do aplicativo**

- Web API não obter informações sobre o usuário.
- Web API não poderá realizar qualquer autorização com base na identidade do usuário. Todas as decisões de autorização são feitas pelo aplicativo da web.  
- A API da web não pode ser usada por um cliente não confiável (aplicativo cliente nativo ou JavaScript).
- Essa abordagem pode ser um pouco mais simples de implementar, porque não há nenhuma lógica de autorização na API da Web.

Em qualquer um dos abordagem, o aplicativo da web deve obter um token de acesso, que é a credencial necessária para chamar a API da web.

- Para identidade de usuário delegado, o token tem provenientes de IDP, que pode emitir um token em nome de usuário.

- Credenciais de cliente, um aplicativo pode obter o token do IDP ou hospedar seu próprio servidor token. (Mas não gravar um servidor token do zero; use uma estrutura bem testada como [IdentityServer3].) Se você autenticar com o Azure AD, altamente recomenda-se para obter o token de acesso do Azure AD, mesmo com o fluxo de credencial de cliente.

O restante deste artigo pressupõe que o aplicativo é autenticar com o Azure AD.

![Obtendo o token de acesso](media/guidance-multitenant-identity/access-token.png)

## <a name="register-the-web-api-in-azure-ad"></a>Registrar web API no Azure AD

Em ordem para Azure AD emitir o token de portador para a web API, você precisa configurar algumas coisas no Azure AD.

1. [Registrar web API no Azure AD].

2. Adicionar a ID do cliente do aplicativo web ao manifesto do aplicativo web API, no `knownClientApplications` propriedade. Consulte [atualizar os manifestos de aplicativo].

3. [Conceder a permissão de aplicativo web para chamar a API da web].

  No Portal de gerenciamento do Azure, você pode definir dois tipos de permissões: "Permissões de aplicativo" de identidade de aplicativo (fluxo de credencial de cliente) ou "Delegada permissões" de identidade de usuário delegado.

  ![Permissões de delegado](media/guidance-multitenant-identity/delegated-permissions.png)

## <a name="getting-an-access-token"></a>Obtendo um token de acesso

Antes de chamar web API, o aplicativo da web obtém um acesso token do Azure AD. Em um aplicativo .NET, use o [Azure AD autenticação biblioteca (ADAL) para .NET][ADAL].

No fluxo de código de autorização OAuth 2, o aplicativo troca um código de autorização para um token de acesso. O código a seguir usa ADAL para obter o token de acesso. Este código é chamado durante a `AuthorizationCodeReceived` evento.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Aqui estão os vários parâmetros que são necessárias:

- `authority`. Derivado da identificação de locatário do usuário conectado. (Não a ID de locatário do provedor SaaS)  
- `authorizationCode`. o código de auth volta obtido do IDP.
- `clientId`. ID do cliente. do aplicativo da web
- `clientSecret`. Segredo de cliente do aplicativo da web.
- `redirectUri`. O redirecionamento URI que você definiu para OpenID conectar. Isso é onde IDP chama de volta com o token.
- `resourceID`. O URI de ID de aplicativo da web API, que você criou quando você registrou web API no Azure AD
- `tokenCache`. Um objeto que armazena os tokens de acesso. Consulte [cache de Token].

Se `AcquireTokenByAuthorizationCodeAsync` é bem sucedida, ADAL armazena o token. Posteriormente, você pode obter o token do cache chamando AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

onde `userId` é a ID de objeto do usuário, que está localizado no `http://schemas.microsoft.com/identity/claims/objectidentifier` reivindicar.

## <a name="using-the-access-token-to-call-the-web-api"></a>Usando o token de acesso para chamar a API da web

Quando você tiver o token, envie-o no cabeçalho da autorização das solicitações de HTTP na Web API.

```
Authorization: Bearer xxxxxxxxxx
```

O seguinte método de extensão do aplicativo de pesquisas define o cabeçalho de autorização em uma solicitação HTTP, usando a classe **HttpClient** .

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Consulte [HttpClientExtensions.cs].

## <a name="authenticating-in-the-web-api"></a>Autenticação na web API

A web API tem a autenticar o token de portador. Em ASP.NET Core 1.0, você pode usar o [Microsoft.AspNet.Authentication.JwtBearer] [ JwtBearer] pacote. Este pacote fornece middleware que permite que o aplicativo receber OpenID conectar tokens de portador.

Registrar o middleware em sua web API `Startup` classe.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Consulte [Startup.cs].

- **Audiência**. Defina esta opção para a URL de ID de aplicativo de web API, que você criou quando você registrou web API Azure AD.
- **Autoridade**. Para um aplicativo de vários locatários, defina isso para `https://login.microsoftonline.com/common/`.
- **TokenValidationParameters**. Para um aplicativo de vários locatários, defina **ValidateIssuer** como falso. Isso significa que o aplicativo irá validar o emissor.
- **Eventos** é uma classe que deriva de **JwtBearerEvents**.

### <a name="issuer-validation"></a>Validação do emissor

Valide o emissor do token no evento **JwtBearerEvents.ValidatedToken** . O emissor será enviado a declaração "iss".

No aplicativo de pesquisas, web API não processa [locatário inscrição]. Portanto, ele apenas verifica se o emissor já está no banco de dados de aplicativo. Caso contrário, ele gera uma exceção, o que faz a autenticação falha.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Consulte [SurveysJwtBearerEvents.cs].

Você também pode usar o evento **ValidatedToken** para fazer a [transformação de declarações]. Lembre-se de que as declarações provém diretamente Azure AD, portanto se o aplicativo web fez as transformações de declarações, aqueles não são refletidas no token de portador que web API recebe.

## <a name="authorization"></a>Autorização

Para uma discussão geral de autorização, consulte [autorização baseada em função e recurso][Authorization]. 

A middleware JwtBearer lida com as respostas de autorização. Por exemplo, para restringir a uma ação de controlador para usuários autenticados, use o atributo **[autorizar]** e especifique **JwtBearerDefaults.AuthenticationScheme** como o esquema de autenticação:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```

Isso retorna um código de 401 status se o usuário não é autenticado.

Para restringir uma ação de controlador por authorizaton política, especifique o nome de política do atributo **[autorizar]** :

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Isso retorna um código de 401 status se o usuário não é autenticado e 403, se o usuário é autenticado, mas não autorizado. Registre a política na inicialização:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```

## <a name="next-steps"></a>Próximas etapas

- Leia o próximo artigo desta série: [tokens de acesso de cache em um aplicativo de vários locatários][token cache]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[parte de uma série]: guidance-multitenant-identity.md
[Pesquisas de Tailspin]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registrar web API no Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Atualizar os manifestos de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Conceder a permissão de aplicativo web para chamar a API da web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Cache de token]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[inscrição do locatário]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[transformação de declarações]: guidance-multitenant-identity-claims.md#claims-transformations
[Authorization]: guidance-multitenant-identity-authorize.md
[exemplo de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[token cache]: guidance-multitenant-identity-token-cache.md
