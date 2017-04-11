<properties
    pageTitle="Adicionar notificações por Push para o iOS aplicativo aos aplicativos do Azure Mobile"
    description="Saiba como usar aplicativos do Azure Mobile para enviar notificações por push para o seu aplicativo iOS."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações por Push para o seu aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionar notificações por push para o projeto de [início rápido do iOS] para que uma notificação de envio é enviada para o dispositivo sempre que um registro é inserido.

Se você não usa o projeto de servidor de início rápido baixado, você precisará do pacote de extensão de notificação de envio. Consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

O [iOS simulator não oferece suporte para notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Você precisará de um dispositivo iOS física e uma [associação de programa de desenvolvedor do Apple](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Configurar o Hub de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrar o aplicativo para notificações por push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Atualizar back-end para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicione as notificações por push ao aplicativo

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Notificações de envio de teste

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Mais

* Modelos oferecem flexibilidade para enviar coloca de várias plataformas e coloca localizadas. [Como usar iOS biblioteca de cliente para aplicativos do Azure móvel](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra como registrar modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[início rápido do iOS]: app-service-mobile-ios-get-started.md
