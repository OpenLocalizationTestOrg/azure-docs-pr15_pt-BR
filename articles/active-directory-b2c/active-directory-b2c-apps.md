<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Os tipos de aplicativos que você pode criar no Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure B2C diretório ativo: Tipos de aplicativos

Azure Active Directory (AD Azure) B2C dá suporte à autenticação para uma variedade de arquiteturas de aplicativo moderna. Todos eles se baseiam os protocolos padrão do setor [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID se conectar](active-directory-b2c-reference-protocols.md). Este documento descreve brevemente os tipos de aplicativos que você pode criar, independentemente do idioma ou plataforma você preferir. Ele também ajuda você a entender os cenários de alto nível antes de você [começar a criar aplicativos](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Noções básicas
Cada aplicativo que usa o Azure AD B2C deve ser registrado no seu [diretório B2C](active-directory-b2c-get-started.md) através do [Portal do Azure](https://portal.azure.com/). O processo de registro de aplicativo coleta e atribui alguns valores para o seu aplicativo:

- Uma **ID de aplicativo** que identifica exclusivamente o seu aplicativo.
- Um **URI redirecionar** que podem ser usados para direcionar respostas de volta para seu aplicativo.
- Quaisquer outros valores de cenário específico. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-b2c-app-registration.md).

Depois que o aplicativo é registrado, ele se comunica com o Azure AD enviando solicitações para o ponto de extremidade do Azure AD v 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada solicitação que é enviada ao Azure AD B2C Especifica uma **política**. Uma política controla o comportamento do Azure AD. Você também pode usar esses pontos de extremidade para criar um conjunto altamente personalizável de experiências de usuário. Políticas comuns incluem inscrição, entrar e políticas de perfil-Editar. Se você não estiver familiarizado com políticas, leia sobre o Azure AD B2C [estrutura de políticas extensível](active-directory-b2c-reference-policies.md) antes de continuar.

A interação de cada aplicativo com um ponto de extremidade de v 2.0 segue um padrão de alto nível semelhante:

1. O aplicativo direciona o usuário para o ponto de extremidade de v 2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O usuário conclui a política de acordo com a definição de política.
4. O aplicativo recebe algum tipo de token de segurança do ponto de extremidade v 2.0.
5. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso protegido.
6. O servidor de recurso valida o token de segurança para verificar que o acesso pode ser concedido.
7. O aplicativo atualiza periodicamente o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Estas etapas podem diferir ligeiramente com base no tipo de aplicativo que você está construindo. Bibliotecas de abrir fonte podem abordar os detalhes para você.

## <a name="web-apps"></a>Aplicativos Web
Para aplicativos web (incluindo .NET, PHP, Java, Ruby, Python e Node) que são hospedados em um servidor e acessados através de um navegador, Azure AD B2C suporta [OpenID conectar](active-directory-b2c-reference-protocols.md) para todas as experiências de usuário. Isso inclui entrar, inscrição e gerenciamento de perfil. A implementação do Azure AD B2C OpenID conectar, seu aplicativo web inicia essas experiências de usuário por emitir solicitações de autenticação para Azure AD. O resultado da solicitação é um `id_token`. Esse token de segurança representa a identidade do usuário. Ele também fornece informações sobre o usuário na forma de declarações:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo no [token de B2C referência](active-directory-b2c-reference-tokens.md).

Em um aplicativo web, cada execução de uma [política](active-directory-b2c-reference-policies.md) leva estas etapas de alto nível:

![Imagem de raias do aplicativo da Web](./media/active-directory-b2c-apps/webapp.png)

Validação do `id_token` usando uma chave de assinatura pública recebida do Azure AD é suficiente para verificar a identidade do usuário. Isso também define um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código entrar web app no nosso [que Introdução seção](active-directory-b2c-overview.md#getting-started).

Além de facilitando entrar simple, um aplicativo de servidor web talvez precise acessar um serviço web de back-end também. Nesse caso, o aplicativo da web pode executar um pouco diferente [fluxo OpenID conectar](active-directory-b2c-reference-oidc.md) e adquira tokens usando códigos de autorização e tokens de atualização. Este cenário é descrito na próxima [seção APIs da Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>APIs da Web
Você pode usar o Azure AD B2C proteger serviços da web como web RESTful do seu aplicativo API. Web APIs pode usar OAuth 2.0 para proteger seus dados, por solicitações HTTP de entrada usando tokens de autenticação. O chamador de uma web API acrescenta um token do cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API pode usar o token para verificar a API identidade do chamador e para extrair informações sobre o chamador de declarações que são codificadas no token. Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo no [token do Azure AD B2C referência](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C atualmente suporta somente web APIs que são acessados por seus próprios clientes conhecidos. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS, um aplicativo do Android e um API da web de back-end. Essa arquitetura é totalmente suportada. Permitir que um cliente de parceiro, como outro aplicativo iOS acessar a mesma web que API não é suportado atualmente. Todos os componentes do aplicativo concluído devem compartilhar uma ID de aplicativo único.

Uma web API pode receber tokens de muitos tipos de clientes, incluindo aplicativos web, área de trabalho e aplicativos móveis, aplicativos de única página, daemons de servidor e outra web APIs. Aqui está um exemplo do fluxo de concluída para um aplicativo web que chama um web API:

![Imagem de raias de aplicativo Web API da Web](./media/active-directory-b2c-apps/webapi.png)

Para saber mais sobre os códigos de autorização, tokens de atualização e as etapas para obter tokens, leia sobre o [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma web API usando Azure AD B2C, confira web Tutoriais de API no nosso [guia de seção Introdução](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos
Aplicativos que são instalados em dispositivos, como aplicativos de desktop e móveis, geralmente precisam acessar serviços back-end ou web APIs em nome de usuários. Você pode adicionar experiências de gerenciamento de identidade personalizadas para seus aplicativos nativos e com segurança serviços de back-end de chamada usando o Azure AD B2C e o [fluxo de código de autorização OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Nesse fluxo, o aplicativo executa [políticas](active-directory-b2c-reference-policies.md) e recebe uma `authorization_code` do Azure AD depois que o usuário conclui a política. O `authorization_code` representa permissão do aplicativo para chamar serviços de back-end em nome de usuário que está conectado no momento. O aplicativo pode trocar o `authorization_code` em segundo plano para um `id_token` e um `refresh_token`.  O aplicativo pode usar o `id_token` para autenticar a uma web de back-end API em solicitações HTTP. Ele também pode usar o `refresh_token` para obter um novo `id_token` quando um mais antigo expira.

> [AZURE.NOTE]
    Azure AD B2C atualmente suporta somente os tokens que são usados para acessar um serviço da web de back-end do aplicativo. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS, um aplicativo do Android e um API da web de back-end. Essa arquitetura é totalmente suportada. Permitindo que o seu aplicativo iOS acessar um API da web de parceiro usando os tokens de acesso OAuth 2.0 não é suportado atualmente. Todos os componentes do aplicativo concluído devem compartilhar uma ID de aplicativo único.

![Imagem do aplicativo nativo raias](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitações atuais
Azure AD B2C não oferece suporte para os seguintes tipos de aplicativos, mas eles estão na roadmapy. Restrições relacionadas ao Azure AD B2C e limitações adicionais são descritas no [restrições e limitações](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Aplicativos de única página (JavaScript)
Muitos aplicativos modernos têm um única página aplicativo front-end escrito principalmente em JavaScript. Eles geralmente usam uma estrutura como AngularJS, Ember.js ou Durandal. A disponibilidade geral serviço Azure AD dá suporte a esses aplicativos, usando o fluxo de implícito OAuth 2.0. No entanto, esse fluxo ainda não está disponível no Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Aplicativos de daemons/servidor
Aplicativos que contêm processos de execução demorada ou que operam sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos como web APIs. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade do usuário delegado) e usando o cliente OAuth 2.0 fluxo de credenciais.

Esse fluxo não é suportado atualmente por Azure AD B2C. Esses aplicativos podem obter tokens somente depois que um fluxo de usuário interativo ocorreu.

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API encadeia (em nome de fluxo)
Arquiteturas de muitos incluem um API que precisa chamar outra web downstream API, onde ambos estão protegidos por Azure AD B2C da web. Este cenário é comum em clientes nativos que têm um API Web back-end. Isso chama um serviço online da Microsoft como a API do Azure AD Graph.

Este cenário encadeada web API pode ser suportado usando a concessão de credencial de portador OAuth 2.0 JWT, também conhecido como o fluxo em nome de.  No entanto, o fluxo em nome de atualmente não está implementado no Azure AD B2C.
