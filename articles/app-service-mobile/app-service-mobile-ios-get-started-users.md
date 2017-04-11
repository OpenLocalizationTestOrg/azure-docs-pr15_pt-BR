<properties
    pageTitle="Adicionar autenticação IOS com aplicativos do Azure Mobile"
    description="Saiba como usar aplicativos do Azure Mobile para autenticar os usuários do aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Adicione autenticação ao seu aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Neste tutorial, você pode adicionar autenticação ao projeto [iOS rápidas começar] usando um provedor de identidade que tem suporte. Este tutorial se baseia o tutorial de [início rápido do iOS] , que devem ser concluídas primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **Executar** para iniciar o aplicativo. Uma exceção será elevada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas _TodoItem_ tabela agora requer autenticação.

##<a name="add-authentication"></a>Adicione autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[início rápido do iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
