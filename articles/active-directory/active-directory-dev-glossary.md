<properties
   pageTitle="Glossário de desenvolvedor do Active Directory do Azure | Microsoft Azure"
   description="Uma lista de termos para comumente usados conceitos de desenvolvedor do Active Directory do Azure e recursos."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glossário de desenvolvedor do Azure Active Directory
Este artigo contém definições para alguns dos principais Azure Active Directory (AD) desenvolvedor conceitos, que são úteis quando aprendendo sobre desenvolvimento de aplicativos do Azure AD.

## <a name="access-token"></a>token de acesso
Um tipo de [token de segurança](#security-token) emitido por um [servidor de autorização](#authorization-server)e usado por um [aplicativo cliente](#client-application) para acessar [protegido servidor do recurso](#resource-server). Geralmente na forma de um [Token de Web de JSON (JWT)][JWT], o token incorpora a autorização concedida ao cliente pelo [proprietário do recurso](#resource-owner), para um nível de acesso solicitado. O token contém todos os aplicável [declarações](#claim) sobre o assunto, permitindo que o aplicativo cliente usá-lo como um formulário de credencial ao acessar um determinado recurso. Isso também elimina a necessidade do proprietário do recurso expor credenciais para o cliente.

Tokens de acesso às vezes citados como "Usuário + aplicativo" ou "Aplicativo somente", dependendo das credenciais que está sendo representadas. Por exemplo, quando um aplicativo cliente usa a:

- [Conceder a autorização de "Código de autorização"](#authorization-grant), o usuário final autentica primeiro como o proprietário do recurso, delegando a autorização para o cliente para acessar o recurso. O cliente autentica posteriormente ao obter o token de acesso. O token pode ser chamado às vezes mais especificamente como um símbolo de "Usuário + aplicativo", ele representa o usuário que autorizado o aplicativo cliente e o aplicativo.
- [Conceder autorização "Credenciais de cliente"](#authorization-grant), o cliente fornece a autenticação exclusiva, funcionando sem autenticação/autorização do recurso-proprietário, para que o token às vezes pode ser chamado de um token de "Somente aplicativo".

Consulte a [referência de Token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-manifest"></a>manifesto do aplicativo  
Um recurso fornecido pelo [portal clássico Azure][AZURE-classic-portal], que produz uma representação de JSON de configuração de identidade do aplicativo, usada como um mecanismo para atualizar o [aplicativo] de associado[ AAD-Graph-App-Entity] e [ServicePrincipal] [ AAD-Graph-Sp-Entity] entidades. Consulte [Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure] [ AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>objeto de aplicativo  
Quando você registrar/atualiza um aplicativo no [Azure portal clássico][AZURE-classic-portal], o portal cria/atualizações um objeto de aplicativo e um [objeto de serviço](#service-principal-object) de correspondente de locatário. O objeto de aplicativo *define* o aplicativo da configuração de identidade globalmente (em todos os locatários onde ele tenha acesso), fornecendo um modelo do qual seu correspondente s principal do serviço são *derivadas* para uso localmente em tempo de execução (em um locatário específico).

Consulte [aplicativo e objetos de serviço Principal] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="application-registration"></a>registro de aplicativo  
Para permitir que um aplicativo integram e delegar funções de gerenciamento de identidades e acesso ao Azure AD, ele deve ser registrado com um [locatário](#tenant)do Azure AD. Quando você registrar seu aplicativo com o Azure AD, você fornece uma configuração de identidade para seu aplicativo, permitindo que ele integrar com o Azure AD e usar recursos como:

- Robusta de gerenciamento de logon único usando o gerenciamento de identidades do Azure AD e [Conectar OpenID] [ OpenIDConnect] implementação de protocolo
- Comunicação acesso aos [recursos protegidos](#resource-server) por [aplicativos cliente](#client-application), através de implementação de [servidor de autorização](#authorization-server) de 2.0 OAuth do Azure AD
- [Consentimento framework](#consent) para gerenciar o acesso do cliente aos recursos protegidos, com base em autorização de proprietário do recurso.

Consulte [integrando aplicativos com o Active Directory do Azure] [ AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>autenticação
O ato de desafiador uma festa para credenciais legítimas, fornecendo a base para a criação de um objeto de segurança a ser usado para identidade e controle de acesso. Por exemplo, durante um [OAuth2 autorização conceder](#authorization-grant) o participante autenticar é a função de [proprietário do recurso](#resource-owner) ou [aplicativo cliente](#client-application), dependendo a concessão usada preenchendo.

## <a name="authorization"></a>autorização
O ato de conceder uma permissão de principal de segurança autenticada para fazer algo. Há dois casos de uso principal no modelo de programação do Azure AD:

- Durante um fluxo de [autorização de OAuth2 conceder](#authorization-grant) : quando o [proprietário do recurso](#resource-owner) concede autorização para o [aplicativo cliente](#client-application), permitindo que o cliente para acessar os recursos do proprietário do recurso.
- Durante o acesso ao recurso pelo cliente: conforme implementado pelo [servidor de recursos](#resource-server), usando a [declaração](#claim) valores apresentam no [token de acesso](#access-token) para tomar decisões de controle de acesso com base em-los.

## <a name="authorization-code"></a>código de autorização
Uma breve vivia "token" fornecido a um [aplicativo cliente](#client-application) pelo [ponto de extremidade de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma da OAuth2 quatro [autorização concede](#authorization-grant). O código é retornado para o aplicativo cliente em resposta a autenticação de um [proprietário do recurso](#resource-owner), indicando que o proprietário do recurso tem delegado autorização para acessar os recursos solicitados. Como parte do fluxo, o código posterior é trocado por um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto de extremidade de autorização
Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server), usado para interagir com o [proprietário do recurso](#resource-owner) para fornecer um [conceder autorização](#authorization-grant) durante uma autorização OAuth2 conceder fluxo. Dependendo do fluxo de conceder autorização usado, a concessão real fornecida pode variar, incluindo um [código de autorização](#authorization-code) ou o [token de segurança](#security-token).

Consulte a especificação OAuth2 [autorização conceder tipos] [ OAuth2-AuthZ-Grant-Types] e [ponto de extremidade de autorização] [ OAuth2-AuthZ-Endpoint] seções e a [especificação de OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>concessão de autorização
Uma credencial que representa a [do proprietário do recurso](#resource-owner) [autorização](#authorization) para acessar seus recursos protegidos, concedidos a um [aplicativo cliente](#client-application). Um aplicativo cliente pode usar um dos [quatro tipos de concessão definidos pela estrutura de autorização de OAuth2] [ OAuth2-AuthZ-Grant-Types] para obter uma concessão, dependendo do tipo de cliente/requisitos: "concessão de autorização de código", "credenciais cliente conceder", "implícito conceder" e "credenciais de senha de proprietário do recurso conceder". A credencial retornada ao cliente é um [token de acesso](#access-token), ou um [código de autorização](#authorization-code) (trocados posteriormente para um token de acesso), dependendo do tipo de concessão de autorização usado.

## <a name="authorization-server"></a>servidor de autorização
Conforme definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], o servidor responsável pela emissão acesso tokens para o [cliente](#client-application) após o [proprietário do recurso](#resource-owner) de autenticação e obter sua autorização com êxito. Um [aplicativo cliente](#client-application) interage com o servidor de autorização no tempo de execução por meio de sua [autorização](#authorization-endpoint) e pontos de extremidade [token](#token-endpoint) , de acordo com o OAuth2 definido [autorização concede](#authorization-grant).

No caso de integração de aplicativos do Azure AD, Azure AD implementa a função de servidor de autorização para aplicativos do Azure AD e serviço da Microsoft APIs, por exemplo [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>declaração
Um [token de segurança](#security-token) contém declarações, que fornecem declarações sobre uma entidade (como um [aplicativo cliente](#client-application) ou [proprietário do recurso](#resource-owner)) a outra entidade (como o [servidor de recurso](#resource-server)). Declarações são pares de valor do nome que retransmissão fatos sobre o assunto token (por exemplo, a entidade de segurança que foi autenticado pelo [servidor de autorização](#authorization-server)). As declarações presentes em um token de determinado dependem de diversas variáveis, incluindo o tipo de token, o tipo de credencial usado para autenticar o assunto, a configuração do aplicativo, etc.

Consulte a [referência de token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>aplicativo cliente  
Conforme definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], um aplicativo que torna protegido solicitações de recurso em nome do [proprietário do recurso](#resource-owner). O termo "cliente" não significa quaisquer características de implementação de hardware específico (por exemplo, se o aplicativo executa em um servidor, uma área de trabalho ou outros dispositivos).  

Um aplicativo cliente solicita [autorização](#authorization) de um proprietário de recursos para participar de um fluxo de [conceder autorização de OAuth2](#authorization-grant) e pode acessar APIs/dados em nome do proprietário do recurso. O Framework de autorização de OAuth2 [define dois tipos de clientes][OAuth2-Client-Types], "confidencial" e "público", com base na capacidade do cliente para manter a confidencialidade de suas credenciais. Aplicativos podem implementar um [cliente da web (confidencial)](#web-client) que é executado em um servidor web, um [cliente nativo (público)](#native-client) instalado em um dispositivo ou um [cliente baseado em agente de usuário (pública)](#user-agent-based-client) que é executado no navegador de um dispositivo.

## <a name="consent"></a>consentimento
O processo de um [proprietário do recurso](#resource-owner) conceder autorização para um [aplicativo cliente](#client-application), específicas [permissões](#permissions) para acessar recursos protegidos, em nome do proprietário do recurso. Dependendo das permissões do solicitado pelo cliente, um administrador ou usuário será solicitado a consentimento para permitir o acesso aos seus dados de organização/indivíduo respectivamente. Observe, em um cenário de [vários locatários](#multi-tenant-application) , do aplicativo [serviço principal](#service-principal-object) também é registrado no locatário do usuário consenting.

## <a name="id-token"></a>Token de ID
Uma [Conexão de OpenID] [ OpenIDConnect-ID-Token] [token de segurança](#security-token) fornecido por um [ponto de extremidade de autorização](#authorization-endpoint), que contém [declarações](#claim) referentes a autenticação de um usuário final [proprietário do recurso [servidor de autorização](#authorization-server) ](#resource-owner). Como um token de acesso, tokens de ID também são representados como um assinada digitalmente [JSON Web Token (JWT)][JWT]. Ao contrário de um token de acesso porém, declarações de um token de ID não são usadas para fins relacionados ao acesso de recurso e controle de acesso especificamente.

Consulte a [referência de token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="multi-tenant-application"></a>aplicativo de vários locatário
Uma classe do [aplicativo cliente](#client-application) que permite entrar e [consentimento](#consent) pelos usuários provisionados em qualquer Azure AD [locatário](#tenant), incluindo locatários diferente da qual o cliente está registrado. Por outro lado, um aplicativo registrado como único-locatário, permitir somente entrada suplementos de contas de usuário provisionados no mesmo locatário que a onde o aplicativo está registrado. Aplicativos [cliente nativo](#native-client) são vários locatários por padrão, enquanto aplicativos [web cliente](#web-client) têm a capacidade de selecionar entre único e vários locatários.

Consulte [como entrar em qualquer usuário do Azure AD usando o padrão de locatário vários aplicativos] [ AAD-Multi-Tenant-Overview] para obter mais detalhes.

## <a name="native-client"></a>cliente nativo
Um tipo de [aplicativo cliente](#client-application) que é instalado nativamente em um dispositivo. Como todo o código é executado em um dispositivo, ela é considerada um cliente "público" devido a seu impossibilidade armazenar credenciais em particular/confidencialidade. Consulte [perfis e tipos de cliente OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões
Um [aplicativo cliente](#client-application) obtém acesso a um [servidor recurso](#resource-server) declarando solicitações de permissão. Dois tipos estão disponíveis:

- Permissões de "Delegadas", que solicitam acesso [baseado em escopo](#scopes) em delegada autorização do assinado do [proprietário do recurso](#resource-owner), são apresentados ao recurso em tempo de execução como [declarações de "scp"](#claim) no do cliente [token de acesso](#access-token).
- Permissões de "Aplicativo", que solicitarem acesso [baseado em função](#roles) sob as credenciais/identidade do aplicativo cliente, são apresentadas ao recurso em tempo de execução como ["funções" declarações](#claim) no token de acesso do cliente.

Eles também superfície durante o processo de [consentimento](#consent) , fazendo o administrador ou o proprietário do recurso a oportunidade de conceder/negar o acesso do cliente aos recursos do seu locatário.

Solicitações de permissão são configuradas nos "Aplicativos" / "Configurar" guia no [portal clássico Azure][AZURE-classic-portal], em "Permissões para outros aplicativos", por selecionando desejado "delegada permissões" e "Permissões de aplicativo" (o último requer a participação na função de Administrador Global). Porque um [cliente pública](#client-application) não puder manter credenciais, ele só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitação delegada e permissões do aplicativo. Do cliente [objeto application](#application-object) armazena as permissões declaradas na sua [propriedade requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietário do recurso
Conforme definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], uma entidade capaz de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, ele é conhecido como um usuário final. Por exemplo, quando um [aplicativo cliente](#client-application) deseja acessar a caixa de correio de um usuário por meio da [API do Microsoft Graph][Microsoft-Graph], ela exige a permissão do proprietário do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos
Conforme definido pelo [Framework de autorização de OAuth2][OAuth2-Role-Def], um servidor que hospeda protegido recursos, capazes de aceitar e responder a protegidos solicitações de recurso por [aplicativos cliente](#client-application) que apresentam um [token de acesso](#access-token). Também conhecido como um recurso protegido servidor ou aplicativo de recurso.

Um servidor de recurso expõe APIs e impõe acesso a seus recursos protegidos por meio de [escopos](#scopes) e [funções](#roles), o uso da estrutura de autorização OAuth 2.0. Exemplos incluem a API do Azure AD Graph que fornece acesso aos dados do Azure AD locatário e as APIs do Office 365 que fornecem acesso a dados como email e calendário. Ambos também estão acessíveis por meio da [API do Microsoft Graph][Microsoft-Graph].  

Como um aplicativo cliente, configuração de identidade do aplicativo do recurso é estabelecida via [registro](#application-registration) em um locatário do Azure AD, fornecendo o aplicativo e o objeto de serviço. Algumas APIs fornecida pela Microsoft, como a API do Azure AD Graph, previamente registrou disponibilizados em todos os locatários durante a configuração de objetos de serviço.

## <a name="roles"></a>funções
Como [escopos](#scopes), funções fornecem uma maneira para um [servidor de recursos](#resource-server) controlar o acesso a seus recursos protegidos. Há dois tipos: uma função de "usuário" implementa o controle de acesso baseado em função para os usuários/grupos que precisam de acesso ao recurso, enquanto uma função de "aplicativo" implementa o mesmo para [aplicativos cliente](#client-application) que requerem acesso.

Funções são definidas pelo recurso cadeias de caracteres (por exemplo "aprovador de despesas," "Somente leitura", "Directory.ReadWrite.All") gerenciadas no [portal clássico Azure] [ AZURE-classic-portal] por meio [manifesto do aplicativo](#application-manifest)do recurso e armazenado na [appRoles propriedade do recurso][AAD-Graph-Sp-Entity]. O portal de clássico Azure também é usado para atribuir usuários às funções de "usuário" e configurar [permissões do aplicativo](#permissions) do cliente para acessar uma função de "aplicativo".

Para uma discussão detalhada das funções de aplicativo expostos pela API de gráfico do Azure AD, consulte [Escopos de permissão de API do gráfico][AAD-Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, consulte [controle de acesso em aplicativos de nuvem usando o Azure AD baseado em função][Duyshant-Role-Blog].

## <a name="scopes"></a>escopos
Assim como as [funções](#roles), escopos fornecem uma maneira para um [servidor de recursos](#resource-server) controlar o acesso a seus recursos protegidos. Escopos são usados para implementar [baseada em escopo] [ OAuth2-Access-Token-Scopes] controle de acesso, para um [aplicativo cliente](#client-application) que foi dado delegado acesso ao recurso pelo seu proprietário.

Escopos são definidos pelo recurso cadeias de caracteres (por exemplo "Mail.Read", "Directory.ReadWrite.All"), gerenciado no [portal clássico Azure] [ AZURE-classic-portal] por meio [manifesto do aplicativo](#application-manifest)do recurso e armazenado na [oauth2Permissions propriedade do recurso][AAD-Graph-Sp-Entity]. O portal de clássico Azure também é usado para configurar o cliente aplicativo [delegada permissões](#permissions) para acessar um escopo.

Uma convenção de nomenclatura de prática recomendada, é usar um formato "resource.operation.constraint". Para uma discussão detalhada dos escopos expostos pela API de gráfico do Azure AD, consulte [Escopos de permissão de API do gráfico][AAD-Graph-Perm-Scopes]. Para escopos expostos por serviços do Office 365, consulte [referência de permissões de API do Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança
Um documento assinado contendo declarações, como um token de OAuth2 ou declaração SAML 2.0. Para um OAuth2 [conceder autorização](#authorization-grant), um [token de acesso](#access-token) (OAuth2) e um [Token de ID](OpenID Connect) são tipos de tokens de segurança, que são implementadas como um [Token de Web de JSON (JWT)][JWT].

## <a name="service-principal-object"></a>objeto de serviço
Quando você registrar/atualiza um aplicativo no [Azure portal clássico][AZURE-classic-portal], o portal cria/atualizações um [objeto de aplicativo](#application-object) e um objeto de principal correspondente do serviço de locatário. O objeto de aplicativo *define* a configuração do aplicativo identidade globalmente (em todos os locatários onde o aplicativo associado recebeu acesso), e é o modelo do qual seu correspondente s principal do serviço são *derivadas* para uso localmente em tempo de execução (em um locatário específico).

Consulte [aplicativo e objetos de serviço Principal] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="sign-in"></a>entrar
O processo de um [aplicativo cliente](#client-application) iniciando a autenticação do usuário final e capturando estado relacionado, com o objetivo de adquirir um [token de segurança](#security-token) e o escopo da sessão de aplicativo ao estado. Estado pode incluir artefatos como informações de perfil de usuário e informações derivam das declarações do token.

A função de entrada de um aplicativo geralmente é usada para implementar o logon único (SSO). Ele também pode ser precedido por uma função de "inscrição", como ponto de entrada para um usuário final obter acesso a um aplicativo (após entrar primeiro). A função de inscrição é usada para coletar e manter estado adicional específico do usuário e pode exigir [consentimento do usuário](#consent).

## <a name="sign-out"></a>saída
O processo de autenticação sem um usuário final, desanexação o estado do usuário associada à sessão de [aplicativo cliente](#client-application) durante a [entrada](#sign-in)

## <a name="tenant"></a>locatário
Uma instância de um diretório de Azure AD é considerada como um locatário do Azure AD. Ele fornece uma variedade de recursos, incluindo:

- um serviço de registro para aplicativos integrados
- autenticação de contas de usuário e aplicativos registrados
- Pontos de extremidade restante necessários para dar suporte a vários protocolos incluindo OAuth2 e SAML, incluindo o [ponto de extremidade de autorização](#authorization-endpoint), [ponto de extremidade de token](#token-endpoint) e o ponto de extremidade "comum" usado por [aplicativos de vários locatários](#multi-tenant-application).

Um locatário também está associado a um anúncio Azure ou assinatura do Office 365 durante a configuração da assinatura, fornecendo recursos de gerenciamento de acesso e de identidade para a assinatura. Veja [como obter um locatário do Active Directory do Azure] [ AAD-How-To-Tenant] para obter detalhes sobre as diversas maneiras como você pode obter acesso para um locatário. Consulte [como Azure assinaturas estão associadas com o Active Directory do Azure] [ AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre assinaturas e um locatário do Azure AD.

## <a name="token-endpoint"></a>ponto de extremidade de token
Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server) para oferecer suporte a OAuth2 [autorização concede](#authorization-grant). Dependendo a concessão, ele pode ser usado para adquirir um [token de acesso](#access-token) (e token relacionados "Atualizar") para um [cliente](#client-application)ou [token de ID](#ID-token) quando usada com a [OpenID conectar] [ OpenIDConnect] protocolo.

## <a name="user-agent-based-client"></a>Cliente baseado em agente de usuário
Um tipo de [aplicativo cliente](#client-application) que downloads do código de um servidor web e executa dentro de um agente de usuário (por exemplo, um navegador da web), como um aplicativo de página única (SPA). Como todo o código é executado em um dispositivo, ela é considerada um cliente "público" devido a seu impossibilidade armazenar credenciais em particular/confidencialidade. Consulte [perfis e tipos de cliente OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="user-principal"></a>capital de usuário
De maneira similar a que um objeto principal do serviço é usado para representar uma instância do aplicativo, um objeto de usuário é outro tipo de entidade de segurança, que representa um usuário. A [entidade de usuário] do Azure AD Graph[ AAD-Graph-User-Entity] define o esquema de um objeto de usuário, incluindo propriedades relacionadas ao usuário como nome e sobrenome, nome de usuário principal, participação de função do diretório, etc. Isso fornece a configuração de identidade de usuário do Azure AD estabelecer uma entidade de segurança do usuário em tempo de execução. O capital de usuário é usado para representar um usuário autenticado para Single Sign-On, [consentimento](#consent) delegação de gravação, tornando decisões de controle de acesso, etc.

## <a name="web-client"></a>cliente da Web
Um tipo de [aplicativo cliente](#client-application) que executa o código de tudo em um servidor web e pode funcionar como um cliente "confidencial" armazenando com segurança suas credenciais no servidor. Consulte [perfis e tipos de cliente OAuth2] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
O [Guia do desenvolvedor do Azure AD] [ AAD-Dev-Guide] é o portal para usar para todo o desenvolvimento Azure AD tópicos, incluindo uma visão geral de [integração] de aplicativos relacionados[ AAD-How-To-Integrate] e Noções básicas de [autenticação do Azure AD e cenários de autenticação compatíveis][AAD-Auth-Scenarios].

Use a seguinte seção de comentários de Disqus para fornecer comentários e ajudar a refinar e nosso conteúdo de forma.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
