<properties
   pageTitle="Trabalhando com identidades baseada em declarações em aplicativos de vários locatários | Microsoft Azure"
   description="Como um uso declarações para autorização e validação de emissor"
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

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Trabalhando com identidades baseada em declarações em aplicativos de vários locatários

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

## <a name="claims-in-azure-ad"></a>Declarações no Azure AD

Quando um usuário entra, Azure AD envia um token de ID que contém um conjunto de declarações sobre o usuário. Uma declaração é simplesmente um pedaço de informação, expresso como um par chave/valor. Por exemplo, `email` = `bob@contoso.com`.  Declarações tem um emissor &mdash; neste caso, o Azure AD &mdash; qual é a entidade que autentica o usuário e cria as declarações. Você confia as declarações porque você confia o emissor. (Por outro lado, se você não confiar o emissor, não confiar as declarações!)

Em um alto nível:

1.  A autenticação do usuário.
2.  IDP envia um conjunto de declarações.
3.  O aplicativo normaliza ou aumenta as declarações (opcionais).
4.  O aplicativo usa as declarações para tomar decisões de autorização.

Em OpenID se conectar, o conjunto de declarações que você obtém é controlado pelo [parâmetro de escopo] da solicitação de autenticação. No entanto, o Azure AD emite um conjunto limitado de declarações por meio de OpenID conectar; consulte [suporte Token e tipos de declaração]. Se desejar obter mais informações sobre o usuário, você precisará usar a API do Azure AD Graph.

Aqui estão alguns das declarações de AAD que um aplicativo pode que importa normalmente:

Tipo de declaração no token de ID |    Descrição
-----------------------|--------------
AUD | Quem o token foi emitido para. Esta será a ID do cliente. do aplicativo Geralmente, você não deve precisa se preocupar com isso acontecer, porque a middleware valida automaticamente. Exemplo:`"91464657-d17a-4327-91f3-2ed99386406f"`
grupos   | Uma lista de grupos AAD dos quais o usuário é membro. Exemplo:`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | O [emissor] do token OIDC. Exemplo:`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nome    | Nome de exibição do usuário. Exemplo:`"Alice A."`
identificação de objeto | O identificador de objeto do usuário no AAD. Esse valor é o identificador imutável e não reutilizável do usuário. Use este valor, não o email, como um identificador exclusivo para usuários; endereços de email podem mudar. Se você usar a API do Azure AD gráfico em seu aplicativo, a ID do objeto é aquele valor usado para informações de perfil de consulta. Exemplo:`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
funções   | Uma lista de funções de aplicativo para o usuário. Exemplo:`["SurveyCreator"]`
TID | ID de locatário. Esse valor é um identificador exclusivo para o site principal no Azure AD. Exemplo:`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | Um nome de exibição legíveis humanos do usuário. Exemplo:`"alice@contoso.com"`
UPN | Nome de usuário principal. Exemplo:`"alice@contoso.com"`

Esta tabela lista os tipos de declaração conforme aparecem no token de ID. No ASP.NET Core 1.0, o middleware OpenID conectar converte alguns dos tipos de declaração quando ele preenche a coleção de declarações para o usuário principal:

-   identificação de objeto >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Transformações de declarações

Durante o fluxo de autenticação, talvez você queira modificar as declarações que obteve de IDP. No ASP.NET Core 1.0, você pode executar a transformação de declarações dentro do evento **AuthenticationValidated** da middleware OpenID se conectar. (Consulte [eventos de autenticação]).

As declarações que você adicionar durante **AuthenticationValidated** são armazenadas no cookie de autenticação de sessão. Eles não obter enviados de volta ao Azure AD.

Aqui estão alguns exemplos de transformação de declarações:

-   **Normalização de declarações**ou fazer declarações consistente entre os usuários. Isso é particularmente relevante se você estiver recebendo declarações de vários IDPs, que podem usar tipos de declaração diferentes informações semelhantes.
Por exemplo, o Azure AD envia uma declaração de "upn" que contém o email do usuário. Outros IDPs podem enviar uma declaração de "email". O código a seguir converte a declaração "upn" em uma declaração "email":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Adicionar **valores de declaração de padrão** para declarações que não estão presentes &mdash; por exemplo, atribuindo um usuário a uma função de padrão. Em alguns casos, isso pode simplificar lógica de autorização.
- Adicione **tipos de declarações personalizados** com informações específicas do aplicativo sobre o usuário. Por exemplo, você pode armazenar algumas informações sobre o usuário em um banco de dados. Você pode adicionar uma declaração personalizada com essas informações para a permissão de autenticação. A declaração é armazenada em um cookie, portanto você só precisa para colocá-lo do banco de dados uma vez por sessão de login. Por outro lado, você também queira evitar a criação de cookies muito grandes, para que você precisa considerar a desvantagem entre tamanho de cookie versus pesquisas de banco de dados.   

Após concluir o fluxo de autenticação, as declarações estão disponíveis em `HttpContext.User`. Neste ponto, você deve tratá-los como um conjunto de somente leitura &mdash; por exemplo, usá-los para tomar decisões de autorização.

## <a name="issuer-validation"></a>Validação do emissor
Conectar-se OpenID, a declaração de emissor ("iss") identifica IDP que emitiu o token de ID. Parte do fluxo de autenticação de OIDC é verificar se a declaração de Emissor corresponde o emissor real. A middleware OIDC trata isso para você.

No Azure AD, o valor de emissor seja exclusivo por locatário do AD (`https://sts.windows.net/<tenantID>`). Portanto, o aplicativo deve fazer uma verificação adicional, para garantir que o emissor representa um locatário permitidos para entrar no aplicativo.

Para um aplicativo de locatário do único, você pode verificar apenas que o emissor é seu próprio locatário. Na verdade, a middleware OIDC faz isso automaticamente por padrão. Em um aplicativo de vários locatário, você precisa permitir vários emissores, correspondente os locatários diferentes. Aqui está uma abordagem geral para usar:

-   Nas opções de middleware OIDC, defina **ValidateIssuer** como falso. Isso desativa a verificação automática.
-   Quando um locatário inscreve, armazene o locatário e o emissor em seu banco de dados do usuário.
-   Sempre que um usuário entra, procure o emissor do banco de dados. Se o emissor não foi encontrado, significa locatário não se inscreveu. Você pode redirecioná-las para uma página de inscrição.
-  Você também poderia lista negra determinados locatários; Por exemplo, para clientes que não pagam sua assinatura.

Para uma discussão mais detalhada, consulte [inscrição e integração em um aplicativo de vários locatários de locatários][signup].

## <a name="using-claims-for-authorization"></a>Usando autorização das declarações

Com declarações, a identidade do usuário não está mais uma entidade monolítica. Por exemplo, um usuário pode ter um endereço de email, número de telefone, aniversário, gênero, etc. Talvez IDP do usuário armazena todas essas informações. Mas ao autenticar o usuário, você receberá um subconjunto delas como declarações normalmente. Nesse modelo, a identidade do usuário é simplesmente um pacote de declarações. Quando você toma decisões de autorização sobre um usuário, você irá procurar determinados conjuntos de declarações. Em outras palavras, a pergunta "Usuário X executar ação Y" basicamente torna-se "tiver X declaração de usuário Z".

Aqui estão alguns padrões básicos para declarações de verificação.

-  Para verificar que o usuário tenha um determinado pedido com um determinado valor:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Este código verifica se o usuário tem uma declaração de função com o valor "Administrador". Trata corretamente quando o usuário tem nenhuma declaração de função ou várias declarações de função.

    A classe **ClaimTypes** define constantes para tipos de declaração usadas. No entanto, você pode usar qualquer valor de cadeia de caracteres para o tipo de declaração.

-   Para obter um único valor de um tipo de declaração, quando você espera para ter no máximo um valor:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Para obter todos os valores de um tipo de declaração:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Para obter mais informações, consulte [função-recurso baseados e autorização em aplicativos de vários locatários][authorization].

## <a name="next-steps"></a>Próximas etapas

- Leia o próximo artigo desta série: [inscrição e integração em um aplicativo de vários locatários de locatários][signup]
- Saiba mais sobre [autorização baseada em declarações] na documentação do ASP.NET Core 1.0

<!-- Links -->
[parte de uma série]: guidance-multitenant-identity.md
[parâmetro de escopo]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Suporte para Token e tipos de declaração]: ../active-directory/active-directory-token-and-claims.md
[emissor]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventos de autenticação]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorização baseada em declarações]: https://docs.asp.net/en/latest/security/authorization/claims.html
[exemplo de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
