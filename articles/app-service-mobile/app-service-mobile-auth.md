<properties
    pageTitle="Autenticação e autorização em aplicativos móveis do Azure | Microsoft Azure"
    description="Referência conceitual e visão geral da autenticação / autorização de recursos para aplicativos do Azure móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticação e autorização em aplicativos móveis do Azure

## <a name="what-is-app-service-authentication--authorization"></a>O que é o aplicativo de serviço de autenticação / autorização?

> [AZURE.NOTE] Este tópico será migrado para um consolidado [autenticação de serviço do aplicativo / autorização](../app-service/app-service-authentication-overview.md) tópico, que abrange Web, celular e aplicativos de API.

Autenticação do serviço de aplicativo / autorização é um recurso que permite que seu aplicativo efetuar login no usuários sem alterações de código necessárias nos aplicativos back-end. Ele oferece uma maneira fácil de proteger seu aplicativo e trabalhar com dados por usuário.

Serviço de aplicativo usa uma identidade federada, na qual um 3º **provedor de identidade** ("IDP") armazena contas e autentica usuários, e o aplicativo usa esta identidade em vez de sua própria. Serviço de aplicativo oferece suporte a cinco provedores de identidade prontos para uso: _Active Directory do Azure_, _Facebook_, _Google_, _Conta da Microsoft_e _Twitter_. Você também pode expandir esse suporte para os aplicativos ao integrar sua própria solução de identidade personalizadas ou outro provedor de identidade.

Seu aplicativo pode utilizar qualquer número desses provedores de identidade, para que você pode fornecer aos usuários finais com opções para como eles login.

Se você desejar começar imediatamente, consulte um dos seguintes tutoriais:

- [Adicione autenticação ao seu aplicativo iOS]
- [Adicione autenticação ao seu aplicativo de Xamarin.iOS]
- [Adicione autenticação ao seu aplicativo de Xamarin.Android]
- [Adicione autenticação ao seu aplicativo do Windows]

## <a name="how-authentication-works"></a>Como funciona a autenticação

Para autenticar usando um dos provedores de identidade, primeiro é necessário configurar o provedor de identidade para saber sobre o seu aplicativo. O provedor de identidade, em seguida, fornecerá IDs e senhas que você fornecer volta para o aplicativo. Isso conclui a relação de confiança e permite que o serviço de aplicativo validar identidades fornecidas a ele.

Estas etapas são detalhadas nos tópicos a seguir:

- [Como configurar seu aplicativo para usar o logon do Active Directory do Azure]
- [Como configurar seu aplicativo para usar o logon do Facebook]
- [Como configurar seu aplicativo para usar o logon do Google]
- [Como configurar seu aplicativo para usar o logon de Account da Microsoft]
- [Como configurar seu aplicativo para usar o logon do Twitter]

Depois que tudo estiver configurado no back-end, você pode modificar seu cliente para efetuar login. Há duas abordagens aqui:

- Usando uma única linha de código, permitem que os aplicativos do Mobile SDK cliente entrar usuários.
- Utilize um SDK publicado por um provedor de identidade fornecida para estabelecer identidade e, em seguida, obter acesso ao serviço de aplicativo.

>[AZURE.TIP] A maioria dos aplicativos deve usar um provedor de SDK para obter uma experiência de logon mais sensação de nativo e para aproveitar o suporte de atualização e outros benefícios específicas do provedor.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Como funciona a autenticação sem um provedor de SDK

Se não desejar configurar um provedor de SDK, você pode permitir que aplicativos móveis executar o logon para você. O SDK do cliente de aplicativos Mobile será aberto um modo de exibição da web para o provedor de sua escolha e concluir o entrada em. Ocasionalmente, blogs e fóruns, você verá esse conhecido como o "fluxo de servidor" ou "direcionado servidor fluxo" desde o servidor está gerenciando o logon e o SDK cliente nunca recebe o token de provedor.

O código necessário para iniciar este fluxo é coberto no tutorial autenticação para cada plataforma. No final do fluxo, o SDK cliente tem um token de serviço de aplicativo e o token é anexado automaticamente a todas as solicitações no back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Como funciona a autenticação com um provedor de SDK

Trabalhar com um provedor SDK permite a experiência de log-in interagir mais junto com a plataforma de sistema operacional em que o aplicativo é executado. Isso também lhe um token de provedor e algumas informações de usuário no cliente, que torna muito mais fácil consumir graph APIs e personalizar a experiência do usuário. Ocasionalmente em blogs e fóruns você verá esse conhecido como o "fluxo de cliente" ou "cliente direcionado fluxo" desde código no cliente está manipulando login e o código do cliente tem acesso a um token de provedor.

Depois de obter um token de provedor, ela precisa ser enviada ao aplicativo de serviço de validação. No final do fluxo, o SDK cliente tem um token de serviço de aplicativo e o token é anexado automaticamente a todas as solicitações no back-end. O desenvolvedor também pode manter uma referência para o token de provedor se eles assim escolher.

## <a name="how-authorization-works"></a>Como funciona a autorização

Autenticação do serviço de aplicativo / autorização expõe várias opções para **fazer quando solicitação não é autenticada**. Antes de seu código recebe uma determinada solicitação, você pode ter verificação do serviço de aplicativo para ver se a solicitação é autenticada e se não, rejeitá-la e tentar fazer com que o usuário faça logon em antes de tentar novamente.

Uma opção é ter não autenticado redirecionar solicitações para um dos provedores de identidade. Em um navegador da web, isso realmente levaria o usuário para uma nova página. Entretanto, seu cliente móvel não poderá ser redirecionado dessa maneira e respostas não autenticadas receberão uma resposta HTTP _401 não autorizado_ . Considerando isso, a primeira solicitação que faz com que o cliente deve ser sempre o ponto de extremidade de login e, em seguida, você poderá fazer chamadas para qualquer outras APIs. Se você tentar chamar outra API antes de efetuar login, o cliente receberá um erro.

Se desejar mais granular controle sobre os pontos de extremidade exigir autenticação, também é possível escolher "nenhuma ação (Permitir solicitação)" para solicitações não autenticadas. Nesse caso, todas as decisões de autenticação são adiadas para o código do seu aplicativo. Isso também permite que você permitir o acesso a usuários específicos com base em regras de autorização personalizada.

## <a name="documentation"></a>Documentação

Os seguintes tutoriais mostram como adicionar autenticação aos seus clientes móveis usando o aplicativo de serviço:

- [Adicione autenticação ao seu aplicativo iOS]
- [Adicione autenticação ao seu aplicativo de Xamarin.iOS]
- [Adicione autenticação ao seu aplicativo de Xamarin.Android]
- [Adicione autenticação ao seu aplicativo do Windows]

Os seguintes tutoriais mostram como configurar o serviço de aplicativo para aproveitar provedores de autenticação diferentes:

- [Como configurar seu aplicativo para usar o logon do Active Directory do Azure]
- [Como configurar seu aplicativo para usar o logon do Facebook]
- [Como configurar seu aplicativo para usar o logon do Google]
- [Como configurar seu aplicativo para usar o logon de Account da Microsoft]
- [Como configurar seu aplicativo para usar o logon do Twitter]

Se desejar usar um sistema de identidade diferente os fornecidos aqui, você também pode aproveitar o [suporte de autenticação personalizados no servidor .NET SDK de visualizar](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Adicione autenticação ao seu aplicativo iOS]: app-service-mobile-ios-get-started-users.md
[Adicione autenticação ao seu aplicativo de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Adicione autenticação ao seu aplicativo de Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Adicione autenticação ao seu aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Como configurar seu aplicativo para usar o logon do Active Directory do Azure]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar seu aplicativo para usar o logon do Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar seu aplicativo para usar o logon do Google]: app-service-mobile-how-to-configure-google-authentication.md
[Como configurar seu aplicativo para usar o logon de Account da Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar seu aplicativo para usar o logon do Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
