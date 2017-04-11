<properties
    pageTitle="Azure AD v 2.0 .NET Web API | Microsoft Azure"
    description="Como criar uma Api de Web MVC .NET que aceita tokens de ambas as Account pessoal da Microsoft e contas de trabalho ou da escola."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Proteger uma web MVC API

Com o Active Directory do Azure o ponto de extremidade de v 2.0, você pode proteger um API de Web usando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , permitindo que os usuários com pessoal conta da Microsoft e o trabalho ou escola contas para o access com segurança sua API da Web.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

Na web do ASP.NET APIs, você pode fazer isso usando middleware OWIN da Microsoft incluído no .NET Framework 4,5.  Aqui, usaremos OWIN para construir uma "Lista de tarefas pendentes" MVC Web API que permite aos clientes criar e ler tarefas da lista de tarefas pendentes de um usuário.  Web API irá validar que solicitações de entrada contém um token de acesso válido e rejeitar todas as solicitações que não passam validação em um roteiro protegido.  Este exemplo foi criado usando o Visual Studio 2015.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

O aplicativo reduzido inclui todo o código clichê para uma API simples, mas está faltando todas as partes relacionadas a identidade. Se você não quiser acompanhá-lo, em vez disso, você pode clonar ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrar um aplicativo
Criar um novo aplicativo no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **Id de aplicativo** atribuída ao seu aplicativo, você precisará dela em breve.

Esta solução do visual studio também contém uma "TodoListClient", que é um aplicativo do WPF simple.  O TodoListClient é usado para demonstrar como um usuário sinais-in e como um cliente pode emitir solicitações à sua Web API.  Nesse caso, o TodoListClient e o TodoListService são representados pelo aplicativo do mesmo.  Para configurar o TodoListClient, você também deve:

- Adicione **a plataforma para o aplicativo** .


## <a name="install-owin"></a>Instalar o OWIN

Agora que você já se registrou um aplicativo, você precisa configurar o seu aplicativo para se comunicar com o ponto de extremidade de v 2.0 para validar solicitações de entrada e tokens.

- Para começar, abra a solução e adicionar os pacotes de NuGet middleware OWIN ao projeto TodoListService usando o Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurar a autenticação de OAuth

- Adicionar uma classe de inicialização de OWIN ao projeto TodoListService chamado `Startup.cs`.  Certo, clique no projeto--> **Adicionar** --> **Novo Item** --> Procurar por "OWIN".  A middleware OWIN chamará o `Configuration(…)` método quando seu aplicativo é iniciado.
- Altere a declaração de classe para `public partial class Startup` -nós já tiver implementado parte dessa classe para você em outro arquivo.  No `Configuration(…)` método, fazer uma chamada para ConfgureAuth(...) para configurar a autenticação de seu aplicativo web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Abra o arquivo `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método, que irá configurar a API da Web para aceitar tokens do ponto de extremidade v 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Agora você pode usar `[Authorize]` atributos para proteger seus controladores e ações com a autenticação de portador OAuth 2.0.  Decorar o `Controllers\TodoListController.cs` classe com uma marca de autorizar.  Isso irá forçar o usuário entrar antes de acessar essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando um chamador autorizado com êxito invoca um da `TodoListController` APIs, a ação talvez seja necessário o acesso às informações sobre o chamador.  OWIN fornece acesso às declarações dentro do token de portador via o `ClaimsPrincpal` objeto.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Por fim, abra o `web.config` arquivo na raiz do projeto TodoListService e insira os valores de configuração na `<appSettings>` seção.
  - Seu `ida:Audience` é a **Id de aplicativo** do aplicativo que você inseriu no portal.

## <a name="configure-the-client-app"></a>Configurar o aplicativo de cliente
Antes de poder ver o serviço de lista de Todo em ação, você precisa configurar o cliente de lista de Todo para que possa obter tokens do ponto de extremidade v 2.0 e fazer chamadas para o serviço.

- No projeto TodoListClient, abra `App.config` e insira os valores de configuração na `<appSettings>` seção.
  - Seu `ida:ClientId` Id de aplicativo que você copiou do portal.

Por fim, limpar, criar e executar cada projeto!  Agora você tem uma API de Web MVC .NET que aceita tokens de ambas as contas pessoais do Microsoft e contas de trabalho ou da escola.  Entrar na TodoListClient e chame sua web api para adicionar tarefas à lista de tarefas pendentes do usuário.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Próximas etapas
Agora, você pode mover em tópicos adicionais.  Talvez você queira experimentar:

[Chamando um API da Web a partir de um aplicativo Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Para obter recursos adicionais, consulte:
- [O guia do desenvolvedor v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Marca de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
