<properties
    pageTitle="Autenticação e autorização no serviço de aplicativo do Azure | Microsoft Azure"
    description="Referência conceitual e visão geral da autenticação / autorização recurso para o serviço de aplicativo do Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no serviço de aplicativo do Azure

## <a name="what-is-app-service-authentication--authorization"></a>O que é o aplicativo de serviço de autenticação / autorização?

Autenticação do serviço de aplicativo / autorização é um recurso que fornece uma maneira para seu aplicativo para entrar os usuários para que você não precisa alterar código no aplicativo back-end. Ele oferece uma maneira fácil de proteger seu aplicativo e trabalhar com dados por usuário.

Serviço de aplicativo usa uma identidade federada, em que um provedor de identidade de terceiros armazena contas e autentica usuários. O aplicativo depende de informações de identidade do provedor para que o aplicativo não precisa armazenar essas informações em si. Serviço de aplicativo oferece suporte a cinco provedores de identidade prontos para uso: Active Directory do Azure, Facebook, Google, Account da Microsoft e Twitter. Seu aplicativo pode usar qualquer número desses provedores de identidade para fornecer aos usuários com opções para como eles se conectar. Para expandir o suporte interno, você pode integrar o outro provedor de identidade ou [sua própria solução de identidade personalizadas][custom-auth].

Se você quiser começar imediatamente, consulte um dos seguintes tutoriais:

- [Adicione autenticação ao seu aplicativo iOS] [ iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]ou [Cordova])
- [Autenticação de usuário para os aplicativos da API do serviço de aplicativo do Azure][apia-user]
- [Começar a usar o serviço de aplicativo do Azure - parte 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Como a autenticação funciona em um serviço de aplicativo

Para autenticar usando um dos provedores de identidade, primeiro é necessário configurar o provedor de identidade para saber sobre o seu aplicativo. O provedor de identidade então fornecerá IDs e senhas que você fornecer ao aplicativo de serviço. Isso conclui a relação de confiança para que o serviço de aplicativo pode validar declarações de usuário, como tokens de autenticação do provedor de identidade.

Para inscrever-se um usuário usando um desses provedores, o usuário deve ser redirecionado para um ponto de extremidade que entra em usuários para o provedor. Se clientes estiver usando um navegador da web, você poderá fazer com que o serviço de aplicativo direcionar automaticamente todos os usuários não autenticados para o ponto de extremidade sinais em usuários. Caso contrário, você precisará direcione seus clientes para `{your App Service base URL}/.auth/login/<provider>`, onde `<provider>` é um dos seguintes valores: aad, facebook, google, microsoft ou twitter. Cenários de celular e API são explicados nas seções posteriormente neste artigo.

Os usuários que interagem com seu aplicativo por meio de um navegador da web terá um cookie configurar de forma que eles podem permanecer autenticados conforme eles procurar seu aplicativo. Para outros tipos de cliente, como celular, um token de web JSON (JWT), que devem ser apresentados na `X-ZUMO-AUTH` cabeçalho, será emitido para o cliente. O cliente de aplicativos Mobile SDKs tratará isso para você. Como alternativa, um token de identidade do Active Directory do Azure ou access podem ser diretamente incluídos na `Authorization` cabeçalho como [token de portador](https://tools.ietf.org/html/rfc6750).

Serviço de aplicativo irá validar qualquer cookie ou token que problemas de seu aplicativo para autenticar os usuários. Para restringir quem pode acessar seu aplicativo, consulte a seção [autorização](#authorization) posteriormente neste artigo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticação móvel com um provedor de SDK

Depois que tudo estiver configurado no back-end, você pode modificar os clientes móveis para entrar com o serviço de aplicativo. Há duas abordagens aqui:

- Use um SDK que um provedor de identidade fornecida publica para estabelecer identidade e, em seguida, obter acesso ao serviço de aplicativo.
- Use uma única linha de código para que o SDK do cliente de aplicativos Mobile pode entrar usuários.

>[AZURE.TIP] A maioria dos aplicativos deve usar um provedor de SDK para obter uma experiência mais consistente quando os usuários entrar, para usar o suporte de atualização e para obter outros benefícios que especifica o provedor.

Quando você usa um provedor SDK, os usuários possam entrar para uma experiência que integra o sistema operacional que o aplicativo é executado em mais próximo. Isso também lhe um token de provedor e algumas informações de usuário no cliente, que torna muito mais fácil consumir graph APIs e personalizar a experiência do usuário. Ocasionalmente em blogs e fóruns, você verá esse conhecido como o "fluxo de cliente" ou "cliente direcionado fluxo" como código no cliente sinais em usuários e o código do cliente tem acesso a um token de provedor.

Quando um token de provedor é obtido, ela precisa ser enviada ao aplicativo de serviço de validação. Quando o serviço de aplicativo valida o token, o serviço de aplicativo cria um novo token de serviço de aplicativo que é retornado para o cliente. O SDK do cliente de aplicativos Mobile tem métodos de auxiliar gerenciar essa troca e anexar o token automaticamente a todas as solicitações no back-end do aplicativo. Os desenvolvedores também podem manter uma referência para o token de provedor se eles assim escolher.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticação móvel sem um provedor de SDK

Se não desejar configurar um provedor de SDK, você pode permitir que o recurso de aplicativos móveis do serviço de aplicativo do Azure para entrar para você. O SDK do cliente de aplicativos Mobile será aberto um modo de exibição da web para o provedor de sua escolha e entrar do usuário. Ocasionalmente em blogs e fóruns, você verá isso conhecido como o "fluxo de servidor" ou "direcionado servidor fluxo" porque o servidor gerencia o processo de sinais em usuários e o SDK cliente nunca recebe o token de provedor.

Código para iniciar este fluxo está incluído no tutorial autenticação para cada plataforma. No final do fluxo, o SDK cliente tem um token de serviço de aplicativo e o token é anexado automaticamente a todas as solicitações no back-end do aplicativo.

### <a name="service-to-service-authentication"></a>Serviço de autenticação

Embora você pode fornecer aos usuários acesso ao seu aplicativo, você também pode confiar em outro aplicativo para chamar sua própria API. Por exemplo, você poderia ter um aplicativo web chamar uma API em outro aplicativo web. Neste cenário, você usa credenciais para uma conta de serviço em vez de credenciais do usuário para obter um token. Uma conta de serviço também é conhecido como um *serviço principal* na linguagem do Active Directory do Azure e autenticação que usa dessa conta também é conhecido como um cenário ao serviço.

>[AZURE.IMPORTANT] Como os aplicativos móveis executem em dispositivos de cliente, aplicativos móveis faça _não_ contar como confiáveis aplicativos e não deve usar um fluxo principal do serviço. Em vez disso, eles devem usar um fluxo de usuário que foi detalhado anteriormente.

Cenários de ao serviço, o serviço de aplicativo pode proteger seu aplicativo usando o Azure Active Directory. O aplicativo de chamada precisa apenas fornecer um token de autorização principal de serviço do Azure Active Directory obtidos fornecendo cliente do cliente secreta do Active Directory do Azure e ID. Um exemplo desse cenário que usa aplicativos de API do ASP.NET é explicado pelo tutorial, [autenticação principal do serviço para os aplicativos da API][apia-service].

Se você quiser usar a autenticação de serviço de aplicativo para lidar com um cenário de serviço para, você pode usar certificados de cliente ou autenticação básica. Para obter informações sobre os certificados de cliente no Azure, consulte [Como para configurar TLS comum autenticação para aplicativos Web.](../app-service-web/app-service-web-configure-tls-mutual-auth.md) Para obter informações sobre autenticação básica no ASP.NET, consulte [Filtros de autenticação do ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticação de conta de serviço de um aplicativo de lógica do serviço de aplicativo para um aplicativo de API é um caso especial que é detalhado na [usando sua API personalizado hospedado no serviço de aplicativo com aplicativos de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Como funciona a autorização no aplicativo de serviço

Você tem controle total sobre as solicitações que podem acessar o seu aplicativo. Autenticação do serviço de aplicativo / autorização pode ser configurada com qualquer um dos seguintes comportamentos:

- Permitir que somente solicitações autenticadas alcançar seu aplicativo.

    Se um navegador recebe uma solicitação anônima, serviço de aplicativo redirecionará a uma página para o provedor de identidade que você escolher para que os usuários possam entrar. Se a solicitação vier de um dispositivo móvel, a resposta retornada é uma resposta HTTP _401 não autorizado_ .

    Com essa opção, você não precisa escrever qualquer código de autenticação em todos os em seu aplicativo. Se você precisar autorização melhor, informações sobre o usuário estão disponíveis para seu código.

- Permitir que todas as solicitações atingir seu aplicativo, mas validar solicitações autenticadas e passar informações de autenticação nos cabeçalhos HTTP.

    Esta opção diferente decisões de autorização para o código do seu aplicativo. Ele fornece mais flexibilidade para lidar com solicitações de anônimas, mas você precisa escrever código.

- Permitir que todas as solicitações para atingir seu aplicativo e não tomar nenhuma ação nas informações de autenticação nas solicitações.

    Neste caso, a autenticação / autorização recurso está desativado. As tarefas de autenticação e autorização são totalmente para cima até o código do seu aplicativo.

Os comportamentos anterior são controlados pela opção **fazer quando solicitação não é autenticada** no portal do Azure. Se você escolher o *nome do provedor de *fazer login ** **, todas as solicitações precisam ser autenticados.** Permitir solicitação (nenhuma ação) * * diferente a decisão de autorização de seu código, mas ele ainda fornece informações de autenticação. Se você quiser que o seu código lidar com tudo, você pode desabilitar a autenticação / recurso de autorização.

## <a name="working-with-user-identities-in-your-application"></a>Trabalhando com identidades dos usuários em seu aplicativo

Serviço de aplicativo passa algumas informações de usuário para seu aplicativo usando cabeçalhos especiais. Solicitações externas proíbem esses cabeçalhos e será somente se apresentar definido pelo aplicativo de serviço de autenticação / autorização. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENTE-CAPITAL-NAME
* X-MS-CLIENTE-CAPITAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Código escrito em qualquer linguagem ou estrutura pode obter as informações que ele precisa desses cabeçalhos. Para aplicativos do ASP.NET 4.6, **ClaimsPrincipal** é definido automaticamente com os valores apropriados.

Seu aplicativo também pode obter detalhes do usuário adicionais por meio de um HTTP GET na `/.auth/me` ponto de extremidade do seu aplicativo. Um token válido que acompanha a solicitação retornará uma carga JSON com detalhes sobre o provedor que está sendo usado, o token de provedor subjacente e algumas outras informações do usuário. O servidor de aplicativos Mobile SDKs fornecem métodos de auxiliar para trabalhar com esses dados. Para obter mais informações, consulte [como usar o SDK do Azure Mobile aplicativos Node](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentação e recursos adicionais

### <a name="identity-providers"></a>Provedores de identidade
Os seguintes tutoriais mostram como configurar o serviço de aplicativo para usar provedores de autenticação diferentes:

- [Como configurar seu aplicativo para usar o logon do Active Directory do Azure][AAD]
- [Como configurar seu aplicativo para usar o logon do Facebook][Facebook]
- [Como configurar seu aplicativo para usar o logon do Google][Google]
- [Como configurar seu aplicativo para usar o logon de Account da Microsoft][MSA]
- [Como configurar seu aplicativo para usar o logon do Twitter][Twitter]

Se você quiser usar um sistema de identidade diferente os fornecidos aqui, você também pode usar o [suporte de autenticação personalizados no servidor Mobile aplicativos .NET SDK de visualizar][custom-auth], que podem ser usados em aplicativos web, aplicativos móveis ou aplicativos de API.

### <a name="web-applications"></a>Aplicativos da Web
Os seguintes tutoriais mostram como adicionar autenticação a um aplicativo web:

- [Começar a usar o serviço de aplicativo do Azure - parte 2][web-getstarted]

### <a name="mobile-applications"></a>Aplicativos móveis
Os seguintes tutoriais mostram como adicionar autenticação a seus clientes móveis usando o fluxo de direcionado server:

- [Adicione autenticação ao seu aplicativo iOS][iOS]
- [Adicionar autenticação para o seu aplicativo Android] [Android]
- [Adicionar autenticação para o seu aplicativo do Windows] [Windows]
- [Autenticação de adicionar seu aplicativo Xamarin.iOS] [Xamarin.iOS]
- [Autenticação de adicionar seu aplicativo Xamarin.Android] [Xamarin.Android]
- [Autenticação de adicionar seu aplicativo Xamarin.Forms] [Xamarin.Forms]
- [Adicionar autenticação para o seu aplicativo de Cordova] [Cordova]

Se você quiser usar o fluxo de cliente direcionado para Azure Active Directory, use os seguintes recursos:

- [Use a biblioteca de autenticação do Active Directory para iOS][ADAL-iOS]
- [Usar a biblioteca de autenticação do Active Directory para Android][ADAL-Android]
- [Usar a biblioteca de autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

Se você quiser usar o fluxo de cliente direcionado para o Facebook, use os seguintes recursos:

- [Usar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Se você quiser usar o fluxo de cliente direcionado para Twitter, use os seguintes recursos:

- [Use tecidos Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Se você quiser usar o fluxo de cliente direcionado para o Google, use os seguintes recursos:

- [Usar o SDK de entrada do Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Aplicativos de API
Os seguintes tutoriais mostram como proteger seus aplicativos de API:

- [Autenticação de usuário para os aplicativos da API do serviço de aplicativo do Azure][apia-user]
- [Autenticação principal do serviço para os aplicativos da API do serviço de aplicativo do Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
