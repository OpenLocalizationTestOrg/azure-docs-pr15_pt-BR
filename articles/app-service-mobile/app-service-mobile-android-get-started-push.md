<properties
    pageTitle="Adicione as notificações por Push ao aplicativo Android com aplicativos móveis do Azure"
    description="Saiba como usar aplicativos do Azure Mobile para enviar notificações por push para seu aplicativo do Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Adicionar notificações por Push para o seu aplicativo Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionar notificações por push para o projeto de [início rápido Android] para que uma notificação de envio é enviada para o dispositivo sempre que um registro é inserido.

Se você não usar o projeto de servidor de início rápido baixado, é necessário o pacote de extensão de notificação de envio. Para obter mais informações, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte:

* Um IDE dependendo de back-end do seu projeto:

    * [Android Studio](https://developer.android.com/sdk/index.html) se este aplicativo tem um back-end node.

    * [2013 de comunidade do Visual Studio](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior se este aplicativo tem um back-end do .net.

* Android 2.3 ou mais recente, revisão do repositório do Google 27 ou superior e serviços do Google Play 9.0.2 ou superior para mensagens de nuvem Firebase.

* Conclua o [Android início rápido].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que dá suporte a mensagens de nuvem Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Ativar notificações por push para o project server

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações por push para o seu aplicativo

Nesta seção, você atualizar seu aplicativo cliente do Android para lidar com notificações por push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do SDK Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. Mensagens de nuvem do Google tem algumas API níveis requisitos mínimos de desenvolvimento e teste, a propriedade **minSdkVersion** no manifesto deve compatíveis com.

Se você estiver testando com um dispositivo mais antigo, consulte [Configurar o Google reproduzir SDK dos serviços] para determinar como baixo você pode definir esse valor e defini-lo adequadamente.

### <a name="add-google-play-services-to-the-project"></a>Adicionar Google Play Services para o projeto

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Adicionar código

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar o aplicativo em relação ao serviço móvel publicado

Você pode testar o aplicativo anexando diretamente um telefone Android com um cabo USB, ou usando um dispositivo virtual no emulador.

## <a name="more"></a>Mais

<!-- URLs -->
[Início rápido Android]: app-service-mobile-android-get-started.md

[Configurar serviços do Google Play SDK]:https://developers.google.com/android/guides/setup
