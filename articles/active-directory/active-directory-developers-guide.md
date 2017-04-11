<properties
   pageTitle="Guia do desenvolvedor do Azure Active Directory | Microsoft Azure"
   description="Este artigo fornece um guia abrangente para recursos voltada para desenvolvedores do Active Directory do Azure."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Guia do desenvolvedor do Azure Active Directory

## <a name="overview"></a>Visão geral
Como uma gerenciamento de identidades como uma plataforma de serviço (IDMaaS), do Azure Active Directory (AD) fornece uma maneira eficiente de integrar o gerenciamento de identidades em seus aplicativos. Os artigos a seguir fornecem visões gerais na implementação e principais recursos do Azure AD. Sugerimos que você lê-las em ordem ou saltar para [Introdução](#getting-started) se você estiver pronto para se aprofundar.


1. [Os benefícios da integração do Azure AD](./develop/active-directory-how-to-integrate.md): Descubra por que a integração com o Azure AD oferece a melhor solução para entrar seguro e a autorização.

1. [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md): tirar proveito de autenticação simplificada no Azure AD para fornecer logon para seu aplicativo.

1. [Integração de aplicativos com o Azure AD](active-directory-integrating-applications.md): Saiba como adicionar, atualizar e remover aplicativos do Azure AD e sobre as diretrizes de identidade visual para aplicativos integrados.

1. [API do Azure AD Graph](active-directory-graph-api.md): usar a API do Azure AD Graph programaticamente acessar Azure AD através de pontos de extremidade da API REST. A API do Azure AD Graph também é acessada por meio do [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fornece uma API unificada que permite o acesso a vários Microsoft cloud serviço APIs, por meio de um único ponto de extremidade de API REST e com um token de acesso único.

1. [Bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md): facilmente autenticar usuários para obter tokens de acesso usando bibliotecas de autenticação do Azure AD para .NET, JavaScript, objetivo-C, Android e mais.


## <a name="getting-started"></a>Guia de Introdução

Estes tutoriais são adaptadas para várias plataformas e podem ajudá-lo a começar rapidamente a desenvolver com o Active Directory do Azure. Como pré-requisito, você deve [obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guias de início rápido de aplicativo móvel e computador

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Universal do Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universal do Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrar diretamente com OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guias de início rápido do aplicativo Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Conectar OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrar diretamente com OpenID conectar](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guias de início rápido do Web API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Consultar o guia de início rápido do diretório

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API de gráfico](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Orientações

Estes artigos descrevem como executar tarefas específicas usando o Azure Active Directory:

- [Obter um locatário do Azure AD](active-directory-howto-tenant.md)
- [Entrar em qualquer usuário do Azure AD usando o padrão de locatário vários aplicativos](active-directory-devhowto-multi-tenant-overview.md)
- Habilitar SSO entre-app usando ADAL, [Android](active-directory-sso-android.md) e em dispositivos [iOS](active-directory-sso-ios.md)
- [Tornar o seu aplicativo AppSource Certified do Azure AD](active-directory-devhowto-appsource-certified.md)
- [Listar seu aplicativo na Galeria de aplicativo Azure AD](active-directory-app-gallery-listing.md)
- [Enviar aplicativos web do Office 365 para o painel de vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Compreender o manifesto de aplicativo do Active Directory do Azure](active-directory-application-manifest.md)
- [Entender as diretrizes de identidade visual para os botões de aquisição de entrada e o aplicativo em seu aplicativo cliente](active-directory-branding-guidelines.md)
- [Visualização: Como criar aplicativos que entre usuários com contas tanto pessoais & corporativas ou escolar](active-directory-appmodel-v2-overview.md)
- [Visualização: Como criar aplicativos que podem se inscrevam & entrar consumidores](../active-directory-b2c/active-directory-b2c-overview.md)
- [Visualização: Configurando o tempo de vida token Azure AD](active-directory-configurable-token-lifetimes.md) usando o PowerShell. Consulte [operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e a [entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) para obter detalhes sobre como configurar por meio da API do Azure AD Graph.

## <a name="reference"></a>Referência

Estes artigos fornecem uma referência de base para o restante e a biblioteca de autenticação APIs, protocolos, erros, exemplos de código e pontos de extremidade.  

###  <a name="support"></a>Suporte
- [Perguntas marcada](http://stackoverflow.com/questions/tagged/azure-active-directory): localizar Azure Active Directory soluções estouro de pilha ao procurar por marcas [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
- Consulte o [Glossário de desenvolvedor do Azure AD](active-directory-dev-glossary.md) para obter definições de alguns dos termos comumente usados relacionados a integração e desenvolvimento de aplicativos.

### <a name="code"></a>Código

- [Bibliotecas de código-fonte aberto do Azure Active Directory](http://github.com/AzureAD): A maneira mais fácil para localizar a origem de uma biblioteca é usando nossa [lista da biblioteca](active-directory-authentication-libraries.md).

- [Exemplos do Azure Active Directory](https://github.com/azure-samples?query=active-directory): A maneira mais fácil para navegar na lista de amostras é usando o [índice de exemplos de código](active-directory-code-samples.md).

- [Active Directory autenticação biblioteca (ADAL) para .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentação de referência está disponível para [a última versão principal](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) e a [versão anterior principal](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API de gráfico

- [Referência da API do gráfico](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência do restante do Azure Active Directory Graph API. [Exibir a experiência de referência de API de gráfico interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Escopos de permissão de API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0 escopos de permissão que são usados para controlar o acesso de um aplicativo com dados de diretório em um locatário.

### <a name="authentication-and-authorization-protocols"></a>Protocolos de autenticação e autorização

- [Sobreposição de chave entrando no Azure AD](active-directory-signing-key-rollover.md): Saiba mais sobre Azure AD autenticação cadência de sobreposição chave e como atualizar a chave para os cenários mais comuns do aplicativo.

- [Protocolo OAuth 2.0: usando a concessão de autorização de código](active-directory-protocols-oauth-code.md): você pode usar a concessão de código de autorização do protocolo de OAuth 2.0, para autorizar o acesso aos aplicativos da Web e APIs da Web no seu Azure Active Directory de locatários.

- [Protocolo OAuth 2.0: Noções básicas sobre a concessão implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): Saiba mais sobre a concessão de autorização implícita, e se ele é adequado para seu aplicativo.

- [Protocolo OAuth 2.0: serviço para chamadas de serviço usando credenciais de cliente](active-directory-protocols-oauth-service-to-service.md): conceder as credenciais de cliente do OAuth 2.0 permite que um serviço da web (um cliente confidencial) para usar seus próprio credenciais para autenticar ao chamar outro serviço da web, em vez de representando um usuário. Neste cenário, o cliente normalmente é um serviço web intermediário, um serviço daemon ou site.

- [Protocolo OpenID conectar 1.0: entrar e autenticação](active-directory-protocols-openid-connect-code.md): protocolo o OpenID conectar 1.0 estende OAuth 2.0 para uso como um protocolo de autenticação. Um aplicativo cliente pode receber um id_token para gerenciar o processo de entrada ou aumentar o fluxo de código de autorização para receber o código de um id_token e a autorização.

- [Referência de protocolo SAML 2.0](active-directory-saml-protocol-reference.md): SAML 2.0 o protocolo permite que aplicativos fornecer aos usuários uma experiência de logon único.

- [Protocolo da Web Services Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory oferece suporte a Web Services Federation 1.2 de acordo com a especificação da Web serviços de Federação versão 1.2. Para obter mais informações sobre o documento de metadados de federação, consulte [Metadados de Federação](active-directory-federation-metadata.md).

- [Tipos de token e declarar com suporte](active-directory-token-and-claims.md): você pode usar este guia para entender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT).

## <a name="videos"></a>Vídeos

### <a name="build"></a>Construir

Estas apresentações de visão geral sobre como desenvolver aplicativos usando o Active Directory do Azure alto-falantes que trabalham diretamente na equipe de engenharia do recurso. As apresentações cobrem tópicos fundamentais, incluindo IDMaaS, autenticação, a federação de identidade e logon único.

- [Identidade do Microsoft: Estado da união e direção futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Active Directory do Azure: Gerenciamento de identidades como um serviço para aplicativos modernos](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Desenvolver aplicativos web modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desenvolver aplicativos nativos modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure sexta-feira
[Azure sexta-feira](https://azure.microsoft.com/documentation/videos/azure-friday/) é uma série de vídeos de 1:1 dedicada a levar até você curto (10 a 15 minutos) de sexta-feira entrevista com especialistas em uma variedade de tópicos Azure recorrente.  Use o recurso de filtro de serviços na página para ver todos os vídeos do Active Directory do Azure.

- [Identidade Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidade Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidade Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Social

- [Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes no mundo do Azure Active Directory.

- [Blog da equipe do Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informações Azure Active Directory específicas à API do gráfico.

- [Identidade de nuvem](http://www.cloudidentity.net): ideias sobre gerenciamento de identidades como um serviço, de um principal diretório PM do Azure Active.  

- [Azure Active Directory no Twitter](https://twitter.com/azuread): avisos Azure Active Directory 140 caracteres ou menos.

## <a name="windows-server-on-premises-development"></a>Desenvolvimento do Windows Server no local
Para obter orientações sobre como usar o desenvolvimento do Windows Server e serviços de Federação do Active Directory (ADFS), consulte:

- [Cenários do AD FS para desenvolvedores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): fornece uma visão geral dos componentes do AD FS e como ele funciona, com detalhes sobre os cenários de autenticação/autorização com suporte.
- [Passo a passo do AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): uma lista de artigos de treinamento, que fornecem instruções passo a passo sobre como implementar os fluxos de autenticação/autorização relacionados.
