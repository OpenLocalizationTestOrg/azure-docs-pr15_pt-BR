<properties
   pageTitle="Autenticação em aplicativos de vários locatários | Microsoft Azure"
   description="Como um aplicativo de vários locatários pode autenticar os usuários do Azure AD"
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
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="authentication-in-multitenant-apps-using-azure-ad-and-openid-connect"></a>Autenticação em aplicativos de vários locatários, usando o Azure AD e conecte OpenID

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série](guidance-multitenant-identity.md). Há também um [aplicativo de exemplo] completo que acompanha esta série.

Este artigo descreve como um aplicativo de vários locatários pode autenticar os usuários do Azure Active Directory (AD Azure), usando OpenID conectar (OIDC) para autenticar.

## <a name="overview"></a>Visão geral

Nossa [implementação de referência](guidance-multitenant-identity-tailspin.md) é um aplicativo do ASP.NET Core 1.0. O aplicativo usa o interno middleware OpenID conectar para executar o fluxo de autenticação OIDC. O diagrama a seguir mostra o que acontece quando o usuário entra no, em um alto nível.

![Fluxo de autenticação](media/guidance-multitenant-identity/auth-flow.png)

1.  O usuário clica no botão "entrar" no aplicativo. Esta ação é tratada pelo controlador MVC.
2.  Controlador MVC retorna uma ação **ChallengeResult** .
3.  A middleware intercepta os **ChallengeResult** e cria uma resposta 302, que redireciona o usuário para a página de entrada do Azure AD.
4.  O usuário autentica com Azure AD.
5.  Azure AD envia um token de ID para o aplicativo.
6.  A middleware valida o token de ID. Neste ponto, o usuário agora é autenticado dentro do aplicativo.
7.  A middleware redireciona o usuário voltar ao aplicativo.

## <a name="register-the-app-with-azure-ad"></a>Registrar o aplicativo com o Azure AD

Para habilitar OpenID conectar, o provedor de SaaS registra o aplicativo dentro de seu próprios locatário Azure AD.

Para registrar o aplicativo, siga as etapas em [Aplicativos de integração com o Active Directory do Azure](../active-directory/active-directory-integrating-applications.md), na seção [Adicionar um aplicativo](../active-directory/active-directory-integrating-applications.md#adding-an-application).

Na página **Configurar** :

-   Anote a ID do cliente.
-   Em **aplicativo é vários locatários**, selecione **Sim**.
-   Definir a **URL de resposta** para uma URL onde Azure AD enviará a resposta de autenticação. Você pode usar a URL base do aplicativo.
  - Observação: O caminho de URL pode ser qualquer item, desde que o nome do host corresponde ao seu aplicativo implantado.
  - Você pode definir várias URLs de resposta. Durante o desenvolvimento, você pode usar um `localhost` endereço, para executar o aplicativo localmente.
-   Gerar um segredo de cliente: em **chaves**, clique no menu suspenso que diz **Selecione duração** e escolha anos de 1 ou 2. A chave será visível quando você clicar em **Salvar**. Certifique-se de copiar o valor, pois não serão exibidos novamente quando você recarregar a página de configuração.

## <a name="configure-the-auth-middleware"></a>Configurar o middleware auth

Esta seção descreve como configurar a autenticação middleware no ASP.NET Core 1.0 para vários locatários autenticação com OpenID se conectar.

Em sua classe de inicialização, adicione a middleware OpenID conectar:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Consulte [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Para obter mais informações sobre a classe de inicialização, consulte [Inicialização do aplicativo](https://docs.asp.net/en/latest/fundamentals/startup.html) na documentação do ASP.NET Core 1.0.

Defina as seguintes opções de middleware:

- **ClientId**. ID do cliente do aplicativo, que você recebeu quando você registrou o aplicativo no Azure AD.
- **Autoridade**. Para um aplicativo de vários locatários, defina isso para `https://login.microsoftonline.com/common/`. Esta é a URL da empresa comuns Azure AD, permitindo que os usuários de qualquer locatário Azure AD para entrar. Para obter mais informações sobre o ponto de extremidade comuns, consulte [esta postagem de blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- No **TokenValidationParameters**, defina **ValidateIssuer** como false. Isso significa que o aplicativo será responsável por validar o valor de emissor do token de ID. (A middleware ainda valida o token em si.) Para obter mais informações sobre como validar o emissor, consulte [validação de emissor](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Defina esta igual ao caminho na URL resposta que você registrou no Azure AD. Por exemplo, se a URL de resposta é `http://contoso.com/aadsignin`, **CallbackPath** deve ser `aadsignin`. Se você não definir essa opção, o valor padrão é `signin-oidc`.
- **PostLogoutRedirectUri**. Especifique uma URL para redirecionar os usuários após o sinal de check-out. Isso deve ser uma página que permite solicitações anônimas &mdash; normalmente a home page.
- **SignInScheme**. Defina como `CookieAuthenticationDefaults.AuthenticationScheme`. Essa configuração significa que depois que o usuário é autenticado, as declarações de usuário são armazenadas localmente em um cookie. Esse cookie é como o usuário permaneça conectado durante a sessão do navegador.
- **Eventos.** Retornos de chamada de evento; consulte [eventos de autenticação](#authentication-events).

Adicione a middleware de autenticação de Cookie para o pipeline. Este middleware é responsável por escrever as declarações de usuário para um cookie e, em seguida, ler o cookie durante carregamentos de página subsequentes.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## <a name="initiate-the-authentication-flow"></a>Iniciar o fluxo de autenticação

Para iniciar o fluxo de autenticação no ASP.NET MVC, retornam uma **ChallengeResult** da contoller:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Isso faz com que a middleware retornar uma resposta (encontrado) 302 que redireciona para o ponto de extremidade de autenticação.

## <a name="user-login-sessions"></a>Sessões de logon do usuário

Conforme mencionado, quando o usuário entra pela primeira vez, a autenticação de Cookie middleware grava as declarações de usuário para um cookie. Depois disso, solicitações HTTP são autenticadas lendo o cookie.

Por padrão, o middleware cookie grava um [cookie de sessão][session-cookie], que obtém uma vez excluiu o usuário fecha o navegador. Na próxima vez que o usuário Avançar visitar o site, eles precisarão entrar novamente. Entretanto, se você definir **IsPersistent** como verdadeiro na **ChallengeResult**, a middleware grava um cookie persistente, para que o usuário permaneça conectado após fechar o navegador. Você pode configurar a expiração de cookies; consulte [Opções de cookies controlando][cookie-options]. Cookies persistentes são mais convenientes para o usuário, mas possam ser inadequados para alguns aplicativos (digamos, um aplicativo bancário) onde deseja que o usuário para entrar em cada vez.

## <a name="about-the-openid-connect-middleware"></a>Sobre a middleware OpenID conectar

A middleware OpenID conectar no ASP.NET oculta a maioria dos detalhes de protocolo. Esta seção contém algumas observações sobre a implementação, que podem ser úteis para entender o fluxo de protocolo.

Primeiro, vamos examinar o fluxo de autenticação em termos de ASP.NET (ignorando os detalhes do fluxo de protocolo OIDC entre o aplicativo e o Azure AD). O diagrama a seguir mostra o processo.

![Fluxo de entrada](media/guidance-multitenant-identity/sign-in-flow.png)

Neste diagrama, há dois controladores MVC. Controlador de conta manipula solicitações de entrada e o controlador de Home referente a home page.

Aqui está o processo de autenticação:

1. O usuário clica no botão "Entrar" e o navegador envia uma solicitação GET. Por exemplo: `GET /Account/SignIn/`.
2. Os retornos de controlador de conta um `ChallengeResult`.
3. A middleware OIDC retorna uma resposta de HTTP 302, redirecionando para Azure AD.
4. O navegador envia a solicitação de autenticação para Azure AD
5. O usuário entra no Azure AD, e Azure AD envia de volta uma resposta de autenticação.
6. A middleware OIDC cria uma entidade de segurança de declarações e passa para a autenticação de Cookie middleware.
7. A middleware cookie serializa o capital de declarações e define um cookie.
8. A middleware OIDC redireciona para a URL de retorno de chamada do aplicativo.
10. O navegador segue o redirecionamento, enviar o cookie na solicitação.
11. A middleware cookie desserializa o cookie um declarações principal e define `HttpContext.User` igual de capital de declarações. A solicitação é roteada para controlador MVC.

### <a name="authentication-ticket"></a>Permissão de autenticação

Se a autenticação for bem-sucedida, a middleware OIDC cria uma permissão de autenticação, que contém uma entidade de segurança de declarações que detém declarações do usuário. Você pode acessar o tíquete dentro do evento **AuthenticationValidated** ou **TicketReceived** .

> [AZURE.NOTE] Até que o fluxo de autenticação inteira seja concluído, `HttpContext.User` ainda detém uma anônimo principal, _não_ o usuário autenticado. O capital anônimo tem uma coleção de declarações vazia. Após a conclusão de autenticação e os redirecionamentos de aplicativo, a middleware cookie desserializa o cookie de autenticação e conjuntos de `HttpContext.User` para uma entidade de segurança de declarações que representa o usuário autenticado.

### <a name="authentication-events"></a>Eventos de autenticação

Durante o processo de autenticação, a middleware OpenID conectar gera uma série de eventos:

- **RedirectToAuthenticationEndpoint**. Chamado direita antes do middleware redireciona para o ponto de extremidade de autenticação. Você pode usar esse evento para modificar a URL de redirecionamento; Por exemplo, para adicionar parâmetros de solicitação. Consulte [adicionando o prompt de consentimento do administrador](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt) para obter um exemplo.

- **AuthorizationResponseReceived**. Chamado após a middleware recebe a resposta de autenticação do provedor de identidade (IDP), mas antes do middleware valida a resposta.  

- **AuthorizationCodeReceived**. Chamada com o código de autorização.

- **TokenResponseReceived**. Chamado após a middleware obtém um acesso token do IDP. Aplica-se somente ao fluxo de código de autorização.

- **AuthenticationValidated**. Chamada quando o middleware valida o token de ID. Neste ponto, o aplicativo tem um conjunto de declarações validadas sobre o usuário. Você pode usar esse evento para executar validação adicional nas declarações ou transformar declarações. Consulte [Trabalhando com declarações](guidance-multitenant-identity-claims.md).

- **UserInformationReceived**. Chamado se o middleware obtém o perfil de usuário do ponto de extremidade de informações de usuário. Aplica-se somente ao fluxo de código de autorização e somente quando `GetClaimsFromUserInfoEndpoint = true` nas opções de middleware.

- **TicketReceived**. Chamado quando a autenticação é concluída. Este é o último evento, supondo que a autenticação é bem sucedida. Após este evento é tratado, o usuário estiver conectado no aplicativo.

- **AuthenticationFailed**. Chamado se a autenticação falhar. Use este evento para lidar com falhas de autenticação &mdash; por exemplo, redirecionando para uma página de erro.

Para fornecer retornos de chamada para esses eventos, defina a opção de **eventos** na middleware. Há duas maneiras diferentes para declarar manipuladores de eventos: embutido com lambdas ou em uma classe que deriva de **OpenIdConnectEvents**.

Alinhado com lambdas:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Derivados de **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

A segunda abordagem é recomendada se seus retornos de chamada de evento tem qualquer lógica substancial, portanto, elas não bagunçar sua classe de inicialização. Nossa implementação de referência usa essa abordagem; consulte [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### <a name="openid-connect-endpoints"></a>OpenID Conectar pontos finais

Azure AD suporta [OpenID conectar descoberta](https://openid.net/specs/openid-connect-discovery-1_0.html), na qual o provedor de identidade (IDP) retorna um documento de metadados JSON de um [ponto de extremidade de conhecidos](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). O documento de metadados contém informações como:

-   A URL do ponto de extremidade autorização. Isso é onde o aplicativo redireciona para autenticar o usuário.
-   A URL do ponto de extremidade "terminar sessão", onde o aplicativo vai para o usuário fazer logoff.
-   A URL para acessar as chaves de assinatura, que o cliente usa para validar os tokens OIDC que ele recebe de IDP.

Por padrão, o middleware OIDC sabe como buscar esses metadados. Defina a opção de **autoridade** na middleware e a middleware constrói a URL para os metadados. (Você pode substituir a URL de metadados, definindo a opção de **MetadataAddress** ).

### <a name="openid-connect-flows"></a>OpenID conectar fluxos

Por padrão, o middleware OIDC usa fluxo híbrida com o modo de resposta de postagem de formulário.

-   _Fluxo de híbrido_ significa que o cliente pode obter um token de ID e um código de autorização a mesma viagem no servidor de autorização.
-   _Modo de resposta de postagem de formulário_ significa que o servidor de autorização usa uma solicitação POST HTTP para enviar o código de identificação token e a autorização para o aplicativo. Os valores são urlencoded formulário (tipo de conteúdo = "aplicativo/x-www-form-urlencoded").

Quando o middleware OIDC redireciona para o ponto de extremidade de autorização, a URL de redirecionamento inclui todos os parâmetros de cadeia de caracteres de consulta necessários para OIDC. Para o fluxo de híbrido:

-   client_id. Esse valor é definido na opção **ClientId**
-   escopo = "openid perfil", o que significa que é uma solicitação OIDC e queremos que o perfil do usuário.
-   response_type = "id_token de código". Especifica o fluxo de híbrido.
-   response_mode = "form_post". Isso especifica a resposta de postagem de formulário.

Para especificar um fluxo de diferentes, defina a propriedade **ResponseType** na lista opções. Por exemplo:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

## <a name="next-steps"></a>Próximas etapas

- Leia o próximo artigo desta série: [Trabalhando com identidades baseada em declarações em aplicativos de vários locatários][claims]


[claims]: guidance-multitenant-identity-claims.md
[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[exemplo de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
