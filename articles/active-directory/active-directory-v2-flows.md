<properties
    pageTitle="Tipos de ponto de extremidade v 2.0 | Microsoft Azure"
    description="Os tipos de aplicativos e cenários compatíveis com o ponto de extremidade do Azure AD v 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Tipos de aplicativos para o ponto de extremidade de v 2.0
O ponto de extremidade de v 2.0 oferece suporte a autenticação para uma variedade de arquiteturas de aplicativo moderna, que se baseiam os protocolos padrão do setor [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/ou [OpenID se conectar](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Este documento descreve brevemente os tipos de aplicativos que você pode criar, independentemente do idioma ou plataforma você preferir.  Ele ajudará a entender os cenários de alto níveis antes de você [ir direto no código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas
Cada aplicativo que usa o ponto de extremidade de v 2.0 precisa ser registrado no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  O processo de registro do aplicativo irá coletar & atribuir alguns valores para o seu aplicativo:

- Uma **Id de aplicativo** que identifica exclusivamente o seu aplicativo
- Um **URI redirecionar** que podem ser usados para direcionar respostas ao seu aplicativo
- Alguns outros valores de cenário específico.  Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Após registrado, o aplicativo se comunica com o Azure AD enviando solicitações para o ponto de extremidade do Active Directory do Azure v 2.0.  Fornecemos estruturas de código-fonte aberto e bibliotecas que cuidam dos detalhes dessas solicitações, ou você pode implementar a lógica de autenticação criando solicitações para esses pontos de extremidade:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplicativos Web
Para aplicativos web (.NET, PHP, Java, Ruby, Python, nó, etc.) que são acessados através de um navegador, você pode executar o usuário entrar usando a [Conexão de OpenID](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Em OpenID conectar o aplicativo web recebe um `id_token`, um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

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

Você pode aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token de v 2.0](active-directory-v2-tokens.md).

Em aplicativos de servidor web, o fluxo de autenticação de entrada leva estas etapas de alto níveis:

![Imagem de raias do aplicativo da Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

A validação do id_token usando uma chave de assinatura pública recebida do ponto de extremidade v 2.0 é suficiente para garantir que a identidade do usuário e definir um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código entrar web app em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .

Além de entrar simple, um aplicativo de servidor web talvez precise acessar algum outro serviço web como um API REST também.  Nesse caso o servidor web app pode participar de um fluxo OpenID conectar & OAuth 2.0 combinado, usando o [código de autorização de 2.0 OAuth fluxo](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Este cenário é coberto abaixo de nosso [guia de Introdução do Web App-WebAPI tópico](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web
Você pode usar o ponto de extremidade de v 2.0 para proteger serviços da web, como o RESTful Web API seu aplicativo.  Em vez de cookies id_tokens e sessão, APIs da Web use access_tokens OAuth 2.0 para proteger seus dados e autenticar solicitações de entrada.  O chamador de uma API Web acrescenta um access_token no cabeçalho da autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API da Web pode usar o access_token para verificar a identidade do chamador API e extrair informações sobre o chamador de declarações que são codificadas na access_token.  Você pode aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token de v 2.0](active-directory-v2-tokens.md).

Uma API Web pode fornecer aos usuários a energia no/opt-recusa de certas funcionalidades ou dados ao expor permissões, também conhecidas como [escopos](active-directory-v2-scopes.md).  Para um aplicativo chamado adquirir permissão para um escopo, o usuário deve consentimento para o escopo durante um fluxo.  O ponto de extremidade de v 2.0 executarão pedir a permissão de usuário e gravação essas permissões em todos os access_tokens que recebe a API da Web.  Todas as necessidades de API Web que me preocupar é Validando a access_tokens recebe em cada chamada e executar as verificações de autorização adequada.

Uma API Web pode receber access_tokens de todos os tipos de aplicativos, incluindo aplicativos de servidor web, área de trabalho e aplicativos móveis, aplicativos de única página, daemons de lado do servidor e até mesmo outras APIs da Web.  O fluxo de alto nível de autenticação de api da web é a seguinte:

![Imagem de raias de Web API](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para saber mais sobre authorization_codes, refresh_tokens e etapas detalhadas de access_tokens, leia sobre o [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para saber como proteger uma web api com OAuth2 access_tokens, confira os exemplos de código do web api em nossa [seção Introdução](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos
Aplicativos que são instalados em um dispositivo, como aplicativos de desktop e móveis, geralmente precisam acessar serviços de back-end ou APIs da Web que armazene dados e realizar várias funções em nome de um usuário.  Esses aplicativos podem adicionar entrar e a autorização para serviços de back-end usando o [código de autorização de 2.0 OAuth fluxo](active-directory-v2-protocols-oauth-code.md).  

Nesse fluxo, um aplicativo recebe um authorization_code do ponto de extremidade v 2.0 ao acesso do usuário, que representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado no momento.  O aplicativo pode trocar o authoriztion_code no plano de fundo para um access_token OAuth 2.0 e um refresh_token.  O aplicativo pode usar o access_token para autenticar Web APIs em solicitações HTTP e pode usar o refresh_token para obter access_tokens novo quando os mais antigos expirarem.

![Imagem do aplicativo nativo raias](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplicativos de única página (javascript)
Muitos aplicativos modernos tem um aplicativo de página única (SPA) front-end principalmente escrito em javascript e muitas vezes usando estruturas como AngularJS, Ember.js, Durandal etc.  O ponto de extremidade do Azure AD v 2.0 oferece suporte a esses aplicativos usando o [OAuth 2.0 implícito fluxo](active-directory-v2-protocols-implicit.md).

Nesse fluxo, o aplicativo recebe tokens do v 2.0 autorizar o ponto de extremidade diretamente, sem executar qualquer trocas de servidor para servidor back-end.  Isso permite que todos os lógica de autenticação e manipulação de sessão para tirar colocam inteiramente no cliente javascript, sem executar redirecionamentos de página extra.

![Imagem de raias fluxo implícito](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver esse cenário em ação, experimente um destes exemplos de código de aplicativo única página em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>Aplicativos de lado daemons/servidor
Aplicativos que contêm processos de execução demorada ou que operam sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web.  Esses aplicativos podem autenticar e obter símbolos usando a identidade do aplicativo (em vez de a identidade do usuário delegado) usando o cliente OAuth 2.0 fluxo de credenciais.

Nesse fluxo, o aplicativo obtém tokens interagindo diretamente com a `/token` ponto de extremidade:

![Imagem de raias de aplicativo daemon](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Para criar um aplicativo daemon, consulte o documeenation de credenciais do cliente em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) ou se referir a [Este aplicativo de amostra do .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitações atuais
Esses tipos de aplicativos não são suportados pelo ponto de extremidade v 2.0, mas estão no roteiro.  Limitações adicionais e restrições para o ponto de extremidade de v 2.0 são descritas no [artigo de limitações v 2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Web encadeada APIs (em nome de)
Arquiteturas de muitos incluem uma API de Web que precisa chamar outra API Web downstream, ambas protegidos pelo ponto de extremidade v 2.0.  Este cenário é comum em clientes nativos que têm um API Web back-end, que por sua vez chama um serviço Online da Microsoft como o Office 365 ou a API do gráfico.

Este cenário de Web API encadeado pode ser suportado usando a concessão de credencial de portador OAuth 2.0 Jwt, também conhecida como o [Fluxo de On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  No entanto, o fluxo de nome diante de atualmente não está implementado no ponto de extremidade v 2.0.  Para ver como esse fluxo funciona no Azure AD disponibilizado serviço, fazer check-out [em nome de código de exemplo no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
