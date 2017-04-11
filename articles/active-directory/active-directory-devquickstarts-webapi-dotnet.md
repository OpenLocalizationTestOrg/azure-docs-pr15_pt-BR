<properties
    pageTitle="Azure AD .NET Introdução | Microsoft Azure"
    description="Como criar uma API de Web de MVC .NET que se integra ao Azure AD para autenticação e autorização."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteger uma API Web usando tokens de portador do Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você estiver criando um aplicativo que fornece acesso a recursos protegidos, você precisará saber como proteger os recursos do access injustificadas.
Azure AD torna mais simples e direto proteger uma web API usando Tokens de acesso OAuth portador 2.0 com apenas algumas linhas de código.

Nos aplicativos web do Asp.NET, você pode fazer isso usando a implementação da middleware OWIN voltado para a comunidade incluído no .NET Framework 4,5 da Microsoft.  Aqui, usaremos OWIN para criar uma web de "Lista de tarefas pendentes" API que:
-   Designa qual API protegidas.
-   Valida que as chamadas de API Web contêm um Token de acesso válido.

Para fazer isso, você precisará:

1. Registrar um aplicativo com o Azure AD
2. Configure seu aplicativo para usar o pipeline de autenticação OWIN.
3. Configurar um aplicativo cliente para chamar à faça lista Web API

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Cada um é uma solução do Visual Studio 2013.  Você também será preciso um locatário do Azure AD no qual deseja registrar seu aplicativo.  Se você não tenha um, [Saiba como obter uma](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar um aplicativo com o Azure AD*
Para proteger seu aplicativo, você precisará primeiro criar um aplicativo em seu locatário e forneça Azure AD algumas informações importantes.

-   Entrar no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **aplicativo Web e/ou WebAPI**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais.  Digite "Para a lista de tarefas pendentes serviço".
    -   O **Uri redirecionar** é uma combinação de esquema e a cadeia de caracteres que Azure AD usaria para retornar qualquer tokens seu aplicativo solicitado. Insira `https://localhost:44321/` para esse valor.
-   Após concluir registro, navegue até o guia de **Configurar** e localize o campo de **URI de ID do aplicativo** .  Insira um identificador de locatário específicas para esse valor, por exemplo`https://contoso.onmicrosoft.com/TodoListService`
- Salve a configuração.  Deixe o portal aberta - também será preciso registrar seu aplicativo cliente em breve.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar seu aplicativo para usar o pipeline de autenticação OWIN*

Agora que você já se registrou um aplicativo com o Azure AD, você precisa configurar o seu aplicativo para se comunicar com o Azure AD para validar solicitações de entrada e tokens.

-   Para começar, abra a solução e adicionar os pacotes de NuGet middleware OWIN ao projeto TodoListService usando o Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Adicionar uma classe de inicialização de OWIN ao projeto TodoListService chamado `Startup.cs`.  Certo, clique no projeto--> **Adicionar** --> **Novo Item** --> Procurar por "OWIN".  A middleware OWIN chamará o `Configuration(…)` método quando seu aplicativo é iniciado.
-   Altere a declaração de classe para `public partial class Startup` -nós já tiver implementado parte dessa classe para você em outro arquivo.  No `Configuration(…)` método, fazer uma chamada para ConfgureAuth(...) para configurar a autenticação de seu aplicativo web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra o arquivo `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método.  Os parâmetros que você fornecer em `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirá como coordenadas para seu aplicativo para se comunicar com o Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Agora você pode usar `[Authorize]` atributos para proteger seus controladores e ações com autenticação de portador JWT.  Decorar o `Controllers\TodoListController.cs` classe com uma marca de autorizar.  Isso irá forçar o usuário entrar antes de acessar essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando um chamador autorizado com êxito invoca um da `TodoListController` APIs, a ação talvez seja necessário o acesso às informações sobre o chamador.  OWIN fornece acesso às declarações dentro do token de portador via o `ClaimsPrincpal` objeto.  
- Um requisito comum para web APIs é validar os "escopos" presentes no token - Isso garante que o usuário final aceitou as permissões necessárias para acessar o serviço de lista de Todo:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Por fim, abra o `web.config` arquivo na raiz do projeto TodoListService e insira os valores de configuração na `<appSettings>` seção.
  - O `ida:Tenant` é o nome do seu locatário do Azure AD, por exemplo, "contoso.onmicrosoft.com".
  - Seu `ida:Audience` é o URI de ID de aplicativo do aplicativo que você inseriu no Portal do Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. configurar um aplicativo cliente e executar o serviço*
Antes de poder ver o serviço de lista de Todo em ação, você precisa configurar o cliente de lista de Todo para que possa obter tokens de AAD e fazer chamadas para o serviço.

- Navegue de volta para o [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
- Criar um novo aplicativo em seu locatário do Azure AD e selecione o **Aplicativo cliente nativo** no prompt resultante.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   Insira `http://TodoListClient/` para o valor de **Uri de redirecionamento** .
- Após concluir registro, AAD atribuirá seu aplicativo uma exclusiva **Id do cliente**. Você precisará desse valor nas próximas etapas, portanto copiá-lo na guia Configurar.
- Também na guia **Configurar** , localize a seção "Permissões para outros aplicativos". Clique em "Adicionar aplicativo". Selecione "Todos os aplicativos" no menu suspenso "Mostrar" e clique na marca de seleção superior. Localize e clique em seu à faça lista serviço e clique na marca de seleção inferior para adicionar o aplicativo. Selecione "Para fazer lista serviço de"acesso no menu suspenso "Delegada permissões" e, em seguida, salve a configuração.


- No Visual Studio, abra `App.config` na TodoListClient do projeto e insira os valores de configuração na `<appSettings>` seção.
  - O `ida:Tenant` é o nome do seu locatário do Azure AD, por exemplo, "contoso.onmicrosoft.com".
  - Seu `ida:ClientId` ID de aplicativo que você copiou do Portal do Azure.
  - Seu `todo:TodoListResourceId` é o URI de ID de aplicativo do aplicativo de serviço de lista de fazer inseridas no Portal do Azure.

Por fim, limpar, criar e executar cada projeto!  Se você ainda não começou, agora é o tempo para criar um novo usuário em seu locatário com um *. domínio onmicrosoft.com.  Entrar para o cliente de lista de tarefas pendentes com aquele usuário e adicionar algumas tarefas à lista de tarefas pendentes do usuário.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Agora você pode mover-se em mais cenários de identidade adicional.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
