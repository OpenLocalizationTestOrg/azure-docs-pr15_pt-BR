<properties
   pageTitle="Autorização em aplicativos de vários locatários | Microsoft Azure"
   description="Como realizar autorização em um aplicativo de vários locatários"
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

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Função-recurso baseados e autorização em aplicativos de vários locatários

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

Nossa [implementação de referência] é um aplicativo do ASP.NET Core 1.0. Neste artigo, examinaremos duas abordagens gerais para autorização, usando a autorização APIs fornecidas no ASP.NET Core 1.0.

-   **Autorização baseada em função**. Autorizar uma ação com base nas funções atribuídas a um usuário. Por exemplo, algumas ações requerem uma função de administrador.
-   **Autorização baseada em recursos**. Autorizar uma ação com base em um determinado recurso. Por exemplo, cada recurso tem um proprietário. O proprietário pode excluir o recurso; outros usuários não podem.

Um aplicativo típico irá utilizar uma mistura de ambos. Por exemplo, para excluir um recurso, o usuário deve ser o recurso proprietário _ou_ administrador.


## <a name="role-based-authorization"></a>Autorização baseada em função

As [Pesquisas de Tailspin] [ Tailspin] aplicativo define as funções a seguir:

- Administrador. Pode executar todas as operações de CRUD em qualquer pesquisa que pertence ao locatário.
- Criador. Podem criar novas pesquisas
- Leitor. Pode ler qualquer pesquisas pertencentes ao locatário

Funções se aplicam aos _usuários_ do aplicativo. No aplicativo de pesquisas, um usuário é um administrador, criador ou leitor.

Para uma discussão sobre como definir e gerenciar funções, consulte [funções do aplicativo].

Independentemente de como gerenciar as funções, seu código de autorização terá aparência semelhante. Core ASP.NET 1.0 introduz uma abstração chamada [diretivas de autorização][policies]. Com esse recurso, você define diretivas de autorização no código e, em seguida, aplica essas políticas para ações de controlador. A política é desassociada do controlador.

### <a name="create-policies"></a>Criar políticas

Para definir uma política, primeiro crie uma classe que implementa `IAuthorizationRequirement`. É mais fácil deriva `AuthorizationHandler`. No `Handle` método, examine o claim(s) relevantes.

Aqui está um exemplo do aplicativo Tailspin pesquisas:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Consulte [SurveyCreatorRequirement.cs]

Essa classe define o requisito de um usuário criar uma nova pesquisa. O usuário deve ser na função SurveyAdmin ou SurveyCreator.

Na sua classe de inicialização, defina uma política nomeada que inclui um ou mais requisitos. Se houver vários requisitos, o usuário precisa atender a _cada_ requisito seja autorizado. O código a seguir define duas diretivas:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Consulte [Startup.cs]

Este código também define o esquema de autenticação, que informa ao ASP.NET quais middleware de autenticação deve executar se a autorização falhar. Nesse caso, podemos especificar a middleware de autenticação de cookie, porque a middleware de autenticação de cookie pode redirecionar o usuário para uma página de "Proibido". O local da página proibido está definido na opção AccessDeniedPath para o middleware cookie; consulte [Configurando a middleware de autenticação].

### <a name="authorize-controller-actions"></a>Autorizar ações do controlador

Finalmente, para autorizar uma ação no controlador MVC, definir a política no `Authorize` atributo:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Em versões anteriores do ASP.NET, você deve definir a propriedade de **funções** no atributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Isso ainda é suportado no ASP.NET Core 1.0, mas ele apresenta algumas desvantagens em comparação com diretivas de autorização:

-   Ele considera um tipo de declaração específico. Políticas podem verificar para qualquer tipo de declaração. As funções são apenas um tipo de declaração.
-   O nome da função é embutidas no atributo. Com as diretivas, a lógica de autorização está em um só lugar, facilitando a atualização ou até mesmo de carga de definições de configuração.
-   Políticas de habilitar decisões de autorização mais complexas (por exemplo, idade > = 21) que não pode ser expresso por associação a função simples.

## <a name="resource-based-authorization"></a>Autorização de recurso com base

_Recursos com base em autorização_ ocorre sempre que a autorização depende de um recurso específico que será afetado por uma operação. No aplicativo Tailspin pesquisas, cada pesquisa tem um proprietário e colaboradores de zero-para-muitos.

-   O proprietário pode ler, atualizar, excluir, publicar e cancelar a pesquisa.
-   O proprietário pode atribuir colaboradores à pesquisa.
-   Os colaboradores podem ler e atualizar a pesquisa.

Observe que "proprietário" e "Colaborador" não são funções de aplicativo; eles são armazenados por pesquisa, do banco de dados do aplicativo. Para verificar se um usuário pode excluir uma pesquisa, por exemplo, o aplicativo verifica se o usuário é o proprietário de pesquisa.

Em ASP.NET Core 1.0, implemente autorização baseada em recursos derivados de **AuthorizationHandler** e substituindo o método de **lidar com** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Observe que essa classe é digitada altamente para objetos de pesquisa.  Registre a classe para DI na inicialização:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Para executar verificações de autorização, use a interface de **IAuthorizationService** , que você pode inserir na sua controladores. O código a seguir verifica se um usuário pode ler uma pesquisa:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Como podemos passar um `Survey` objeto, esta chamada chamará o `SurveyAuthorizationHandler`.

No seu código de autorização, uma boa abordagem é agregar todas as permissões do usuário função-recurso baseados e, em seguida, verificar a agregação definida contra a operação desejada.
Aqui está um exemplo do aplicativo pesquisas. O aplicativo define vários tipos de permissão:

- Administração
- Colaborador
- Criador
- Proprietário
- Leitor

O aplicativo também define um conjunto de possíveis operações em pesquisas:

- Criar
- Leitura
- Atualização
- Excluir
- Publicar
- Unpublsh

O código a seguir cria uma lista de permissões para um usuário específico e pesquisa. Observe que este código examina funções de aplicativo do usuário, tanto os campos de proprietário/colaborador da pesquisa.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Consulte [SurveyAuthorizationHandler.cs].

Em um aplicativo de vários locatário, você deve garantir que as permissões não "perda" dados do outro locatário. No aplicativo pesquisas, a permissão de Colaborador é permitida entre locatários &mdash; você pode atribuir uma pessoa de outro locatário como um contriubutor. Os outros tipos de permissão são restritos aos recursos que fazem parte de locatário do usuário. Para impor esse requisito, o código verifica a identificação de locatário antes de conceder a permissão. (O `TenantId` campo conforme atribuído quando a pesquisa é criada.)

A próxima etapa é verificar a operação (leitura, atualização, exclusão, etc) contra as permissões. O aplicativo de pesquisas implementa esta etapa usando uma tabela de pesquisa de funções:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Próximas etapas

- Leia o próximo artigo desta série: [Protegendo um back-end web API em um aplicativo de vários locatários][web-api]
- Para saber mais sobre autorização de recurso com base no ASP.NET 1.0 Core, consulte [Autorização com base do recurso][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[Funções de aplicativo]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implementação de referência]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurando o middleware de autenticação]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[exemplo de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
