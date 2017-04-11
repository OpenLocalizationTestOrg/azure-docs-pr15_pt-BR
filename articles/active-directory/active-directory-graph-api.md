<properties
   pageTitle="Gráfico do Active Directory do Azure API | Microsoft Azure"
   description="Um guia de visão geral e início rápido para a API de gráfico que permite o acesso de programação ao Azure AD através de pontos de extremidade da API REST."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Gráfico do Active Directory do Azure API

> [AZURE.IMPORTANT] Funcionalidade do Azure AD Graph API também está disponível por meio do [Microsoft Graph](https://graph.microsoft.io/), uma API unificada que inclui APIs de outros serviços da Microsoft como Outlook, OneDrive, o OneNote, planejador e Office Graph, acessível por meio de uma única empresa e com um token de acesso único.

A API do Azure Active Directory Graph fornece acesso através de programação ao Azure AD através de pontos de extremidade da API REST. Aplicativos podem usar a API do gráfico para executar criar, ler, atualizar e excluir operações (CRUD) nos dados de diretório e objetos. Por exemplo, a API de gráfico suporta as seguintes operações comuns para um objeto de usuário:

- Criar um novo usuário em um diretório

- Obter propriedades detalhadas de um usuário, como seus grupos

- Atualizar as propriedades de um usuário, como seu local e o número de telefone, ou alterar sua senha

- Verificar a associação a um grupo de um usuário de acesso baseado em função

- Desabilitar uma conta de usuário ou excluí-la totalmente

Além dos objetos de usuário, você pode executar operações semelhantes em outros objetos como grupos e aplicativos. Para chamar a API do gráfico em uma pasta, o aplicativo deve ser registrado com Azure AD e ser configurado para permitir acesso ao diretório. Normalmente, isso é feito por meio de um fluxo de consentimento administrador ou usuário.

Para começar a usar a API de gráfico do Azure Active Directory, consulte o [Guia de início rápido do gráfico API](active-directory-graph-api-quickstart.md)ou veja a [documentação de referência de API de gráfico interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Recursos

A API do Graph fornece os seguintes recursos:

- **Pontos de extremidade de API REST**: A API do gráfico é um serviço RESTful composto dos pontos de extremidade que são acessados usando solicitações HTTP padrão. A API de gráfico oferece suporte a tipos de conteúdo XML ou JSON Javascript Object Notation () para solicitações e respostas. Para obter mais informações, consulte [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Autenticação com Azure AD**: cada solicitação à API do gráfico deve ser autenticada anexando uma JSON Web Token (JWT) no cabeçalho da autorização da solicitação. Esse token é adquirida fazendo uma solicitação para o ponto de extremidade token do Azure AD e fornecendo credenciais válidas. Você pode usar o fluxo de credenciais de cliente OAuth 2.0 ou o código de autorização conceder fluxo adquirir um token para chamar o gráfico. Para obter mais informações, [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorização baseada em função (RBAC)**: grupos de segurança são usados para executar RBAC na API do gráfico. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo pode chamar a operação de [Verificar associações de grupo (transitivos)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , que retorna true ou false.

- **Consulta diferencial**: se você quiser verificar se há alterações em um diretório entre dois períodos de tempo sem precisar fazer consultas frequentes à API do gráfico, você pode fazer uma solicitação de consulta diferencial. Este tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para obter mais informações, consulte [Azure AD Graph API diferencial consulta](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Extensões de diretório**: se você estiver desenvolvendo um aplicativo que precisa ler ou escrever propriedades exclusivas para objetos de diretório, você pode registrar e usar valores de extensão usando a API do gráfico. Por exemplo, se seu aplicativo requer uma propriedade de ID do Skype para cada usuário, você pode registrar a nova propriedade no diretório e ela estará disponível em todos os objetos de usuário. Para obter mais informações, consulte [extensões de esquema do Azure AD Graph API Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Protegido por escopos de permissão**: AAD Graph API expõe escopos de permissão que habilitam o acesso seguro/consentiu aos dados AAD e suportam a uma variedade de tipos de aplicativo de cliente, incluindo:
 - acesso aos dados por meio de autorização aqueles com uma interface de usuário que são dadas delegado do assinado do usuário (delegado)
  - aqueles que usam aplicativo-definem controle de acesso baseado em função como clientes de daemon do serviço (funções do aplicativo)

    Delegados tanto escopos de permissão de função de aplicativo representam um privilégio exposto pela API do gráfico e podem ser solicitados por aplicativos clientes através de de permissões de registro do aplicativo [recursos no portal de clássico do Azure](https://manage.windowsazure.com). Clientes podem verificar os escopos de permissão concedida a eles por meio da inspeção a declaração de escopo ("scp") recebida no token de acesso para permissões delegadas e as funções ("funções") reivindicar para permissões de função do aplicativo. Saiba mais sobre [permissão de API do Azure AD Graph escopos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Cenários

A API do gráfico permite muitos cenários de aplicativos. Cenários a seguir são as mais comuns:

- **Aplicativo de linha de negócios (único Locatário)**: neste cenário, um desenvolvedor de enterprise funciona para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo web que interage com o Azure AD para realizar tarefas como atribuir uma licença a um usuário. Esta tarefa requer acesso à API do gráfico, portanto os registradores de desenvolvedor a única inquilinos aplicativo no Azure AD e configura permissões leitura e gravação para a API do gráfico. Em seguida, o aplicativo é configurado para usar seus próprio credenciais ou aqueles do usuário atualmente entrar adquirir um token para chamar a API do gráfico.

- **Software como um aplicativo de serviço (vários Locatário)**: neste cenário, um fornecedor de software independente (ISV) está desenvolvendo o aplicativo da web de vários locatários hospedado que fornece recursos de gerenciamento de usuário para outras organizações que usam o Azure AD. Esses recursos exigem acesso aos objetos de diretório e, portanto, o aplicativo precisa chamar a API do gráfico. O desenvolvedor registra o aplicativo no Azure AD, configura para exigir a leitura e gravação permissões de API do gráfico e, em seguida, permite acesso externo para que outras organizações podem consentimento para usar o aplicativo em seu diretório. Quando um usuário em outra organização autentica o aplicativo pela primeira vez, eles são mostrados uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando.  Concedendo consentimento depois dará ao aplicativo aqueles solicitada permissões à API Graph na pasta do usuário. Para obter mais informações sobre a estrutura de consentimento, consulte [Visão geral do Framework consentimento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Consulte também

[Guia de início rápido do Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Documentação do AD restante de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guia do desenvolvedor do Azure Active Directory](active-directory-developers-guide.md)
