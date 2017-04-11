<properties
    pageTitle="Adicionar autenticação no Android aos aplicativos Mobile | Serviço de aplicativo do Azure"
    description="Saiba como usar aplicativos Mobile em um serviço de aplicativo do Azure para autenticar os usuários de seu aplicativo Android por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Adicione autenticação ao seu aplicativo do Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo

Neste tutorial, você pode adicionar autenticação para o projeto de início rápido de lista de tarefas pendentes no Android usando um provedor de identidade que tem suporte. Este tutorial se baseia o tutorial de [Introdução aos aplicativos Mobile] , que devem ser concluídas primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ No Android Studio, abra o projeto a você concluiu projetado com o tutorial [Introdução aos aplicativos Mobile]. No menu **Executar** clique em **executar o aplicativo** e verificar se uma exceção não tratada com um código de status de 401 (não autorizado) é aumentada depois que o aplicativo for iniciado.

     Essa exceção acontece porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela de _TodoItem_ agora requer autenticação.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do aplicativo Mobile back-end.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Tokens de autenticação de cache no cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar dentre os seguintes tutoriais:

+ [Adicionar as notificações por push para o seu aplicativo Android](app-service-mobile-android-get-started-push.md) Saiba como configurar seu back-end do aplicativo móvel para usar Hubs de notificação do Azure para enviar notificações por push.

+ [Habilitar a sincronização offline para seu aplicativo do Android](app-service-mobile-android-get-started-offline-data.md) Saiba como adicionar suporte offline seu aplicativo usando um back-end do aplicativo Mobile. Sincronização offline permite aos usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução aos aplicativos Mobile]: app-service-mobile-android-get-started.md
