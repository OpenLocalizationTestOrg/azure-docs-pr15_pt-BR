<properties
   pageTitle="Funções de aplicativo | Microsoft Azure"
   description="Como realizar autorização usando funções de aplicativo"
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
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  <a name="application-roles-in-multitenant-applications"></a>Funções do aplicativo em aplicativos vários locatários

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

Funções de aplicativo são usadas para atribuir permissões a usuários. Por exemplo, as [Pesquisas de Tailspin] [ Tailspin] aplicativo define as funções a seguir:

- Administrador. Pode executar todas as operações de CRUD em qualquer pesquisa que pertence ao locatário.
- Criador. Podem criar novas pesquisas.
- Leitor. Pode ler qualquer pesquisas que fazem parte de locatário.

Você pode ver que os funções eventualmente obtém convertidas em permissões, durante a [autorização]. Mas, a primeira pergunta é como atribuir e gerenciar funções. Identificamos três opções principais:

-   [Funções de aplicativo do Azure AD](#roles-using-azure-ad-app-roles)
-   [Grupos de segurança do Azure AD](#roles-using-azure-ad-security-groups)
-   [Gerenciador de função do aplicativo](#roles-using-an-application-role-manager).

## <a name="roles-using-azure-ad-app-roles"></a>Funções usando as funções de aplicativo do Azure AD

Essa é a abordagem que usamos no aplicativo Tailspin pesquisas.

Nesta abordagem, o provedor de SaaS a define as funções do aplicativo adicionando-o ao manifesto do aplicativo. Depois que um cliente se inscreve, o administrador do diretório do AD do cliente atribui usuários às funções. Quando um usuário entra, funções atribuídas do usuário são enviadas como declarações.

> [AZURE.NOTE] Se o cliente tiver Azure AD Premium, o administrador pode atribuir um grupo de segurança para uma função e membros do grupo herdarão a função de aplicativo. Essa é uma maneira conveniente para gerenciar funções, porque o proprietário do grupo não precisa ser um administrador do AD.

Vantagens dessa abordagem:

-   Modelo de programação simples.
-   As funções são específicas do aplicativo. As declarações de função para um aplicativo não são enviadas para outro aplicativo.
-   Se o cliente remove o aplicativo do seu locatário do AD, as funções desaparecem.
-   O aplicativo não precisa qualquer permissões extras do Active Directory, diferente de seu perfil de usuário de leitura.

Desvantagens:

- Os clientes sem Azure AD Premium não podem atribuir grupos de segurança para funções. Para esses clientes, todas as atribuições de usuário devem ser feitas por um administrador do AD.
- Se você tiver uma web de back-end API, que é separado do aplicativo da web, atribuições de função para o aplicativo web não se aplicam à web API. Para obter mais discussões desse ponto, consulte [Protegendo um back-end web API].

### <a name="implementation"></a>Implementação

**Defina as funções.** O provedor de SaaS declara as funções de aplicativo no do [manifesto do aplicativo]. Por exemplo, aqui está a entrada de manifesto para o aplicativo de pesquisas:

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

O `value` propriedade aparece na declaração de função. O `id` propriedade é o identificador exclusivo para a função definida. Sempre gerar um novo valor GUID para `id`.

**Atribuir aos usuários**. Quando um novo cliente inscreve, o aplicativo é registrado no locatário do AD do cliente. Neste ponto, um administrador do AD do locatário pode atribuir usuários a funções.

> [AZURE.NOTE] Conforme mencionado anteriormente, os clientes com o Azure AD Premium também podem atribuir grupos de segurança para funções.

A seguinte captura de tela do portal do Azure mostra três usuários. Alice foi atribuída diretamente para uma função. Paulo herdadas uma função como um membro de um grupo de segurança denominado "Pesquisas administrador", que é atribuído a uma função. Carlos não está atribuído a qualquer função.

![Usuários atribuídos](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] Como alternativa, o aplicativo pode atribuir funções programaticamente, usando a API do Azure AD Graph.  No entanto, isso exige o aplicativo para obter permissões de gravação para o diretório do AD do cliente. Um aplicativo com essas permissões pode fazer muitas prejuízo &mdash; o cliente é confiar no aplicativo não para mexer seu diretório. Muitos clientes podem ser dispostos conceder a esse nível de acesso.

**Obter declarações de função**. Quando um usuário entra, o aplicativo recebe funções atribuídas do usuário em uma declaração com tipo `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.  

Um usuário pode ter várias funções ou nenhuma função. No seu código de autorização, não pressupõem que o usuário tem exatamente uma função reivindicar. Em vez disso, escreva código que verifica se um valor específico de declaração está presente:

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## <a name="roles-using-azure-ad-security-groups"></a>Funções usando grupos de segurança do Azure AD

Nesta abordagem, funções são representadas como grupos de segurança do AD. O aplicativo atribui permissões a usuários com base em suas associações de grupo de segurança.

Vantagens:

-   Para clientes que não têm Azure AD Premium, essa abordagem habilita o cliente usar grupos de segurança para gerenciar as atribuições de função.

Desvantagens:

- Complexidade. Como cada locatário envia declarações de grupo diferente, o aplicativo deve controlar de quais grupos de segurança correspondem aos quais funções de aplicativo, para cada site principal.
- Se o cliente remove o aplicativo do seu locatário do AD, os grupos de segurança permanecem no seu diretório AD.

### <a name="implementation"></a>Implementação

No manifesto do aplicativo, defina o `groupMembershipClaims` propriedade para "SecurityGroup". Isso é necessário para obter declarações de associação de grupo de AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Quando um novo cliente se inscreve, o aplicativo instrui o cliente para criar grupos de segurança para as funções necessitados ao aplicativo. O cliente precisa, em seguida, insira o objeto de grupo IDs no aplicativo. O aplicativo armazena esses em uma tabela que mapeia as identificações de grupo para funções do aplicativo, por locatário.

> [AZURE.NOTE] Como alternativa, o aplicativo pode criar grupos programaticamente, usando a API do Azure AD Graph.  Isso seria menos propensa. No entanto, ele requer que o aplicativo para obter "ler e gravar todos os grupos" permissões para o diretório do AD do cliente. Muitos clientes podem ser dispostos conceder a esse nível de acesso.

Quando um usuário entra:

1.  O aplicativo recebe grupos do usuário, como declarações. O valor de cada declaração é a ID do objeto de um grupo.
2.  Azure AD limita o número de grupos enviado no token. Se o número de grupos exceder esse limite, o Azure AD envia uma declaração de "excedente" especial. Se essa declaração estiver presente, o aplicativo deve consultar a API do Azure AD Graph para obter todos os grupos aos quais o usuário pertence. Para obter detalhes, consulte [autorização nos aplicativos de nuvem usando grupos do AD], sob a seção intitulada "Grupos reivindicar excedente".
3.  O aplicativo procura o objeto IDs no seu próprio banco de dados, para localizar as funções do aplicativo correspondente para atribuir ao usuário.
4.  O aplicativo adiciona um valor de declaração personalizada para o capital de usuário que expresse a função de aplicativos. Por exemplo: `survey_role` = "SurveyAdmin".

Diretivas de autorização devem usar a declaração de função personalizado, não o grupo reivindicar.

## <a name="roles-using-an-application-role-manager"></a>Funções usando um aplicativo do Gerenciador de função

Com essa abordagem, funções de aplicativo não são armazenadas no AD Azure todo. Em vez disso, o aplicativo armazena as atribuições de funções para cada usuário no seu próprio banco de dados &mdash; por exemplo, usando a classe **RoleManager** ASP.NET identidade.

Vantagens:

-   O aplicativo tem total controle sobre as atribuições de usuário e funções.

Desvantagens:

- Mais complexa, difícil manutenção.
- Não é possível usar grupos de segurança do AD para gerenciar atribuições de função.
- Armazena informações de usuário no banco de dados do aplicativo, onde ele pode obter fora de sincronia com o diretório do AD do locatário, como os usuários são adicionados ou removidos.   

Há vários exemplos existentes para essa abordagem. Por exemplo, consulte [criar um aplicativo do ASP.NET MVC com auth e DB do SQL e implantar o serviço de aplicativo do Azure].

## <a name="next-steps"></a>Próximas etapas

- Leia o próximo artigo desta série: [autorização baseada em função e baseado em recursos em aplicativos de vários locatários][autorização]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de uma série]: guidance-multitenant-identity.md
[autorização]: guidance-multitenant-identity-authorize.md
[Protegendo um API da web de back-end]: guidance-multitenant-identity-web-api.md
[Criar um aplicativo do ASP.NET MVC com auth e DB do SQL e implantar o serviço de aplicativo do Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[manifesto do aplicativo]: ../active-directory/active-directory-application-manifest.md
[exemplo de aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
