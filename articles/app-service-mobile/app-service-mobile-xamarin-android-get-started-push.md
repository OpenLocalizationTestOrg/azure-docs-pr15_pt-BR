<properties
    pageTitle="Adicionar notificações por push para o seu aplicativo de Xamarin.Android | Serviço de aplicativo do Azure"
    description="Saiba como usar o serviço de aplicativo do Azure e Hubs de notificação do Azure para enviar notificações por push para seu aplicativo Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações por push para o seu aplicativo de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Visão geral


Neste tutorial, você adicionar notificações por push para o projeto [Xamarin.Android rápido iniciar](app-service-mobile-windows-store-dotnet-get-started.md) para que uma notificação de envio é enviada para o dispositivo sempre que um registro é inserido.

Se você não usa o projeto de servidor de início rápido baixado, você precisará do pacote de extensão de notificação de envio. Consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.


##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ Uma conta do Google ativa. Você pode se inscrever para uma conta do Google em [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Componente do cliente de mensagens de nuvem do Google](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Habilitar Firebase nuvem de mensagens

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar solicitações de envio

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Atualizar o projeto de servidor para enviar as notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurar o projeto de cliente para notificações por push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Adicionar código de notificações de envio para o seu aplicativo

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Teste as notificações por push em seu aplicativo

Você pode testar o aplicativo usando um dispositivo virtual no emulador. Há etapas de configuração adicionais necessárias durante a execução em um emulador.

1. Certifique-se de que você está implantando ou depuração em um dispositivo virtual que tenha Google APIs definida como destino, conforme mostrado abaixo no Gerenciador de dispositivo Android Virtual (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicionar uma conta do Google para o dispositivo Android clicando em **aplicativos** > **configurações** > **Adicionar conta**, siga as instruções.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Executar o aplicativo de lista de tarefas pendentes como antes e inserir um novo item de todo. Desta vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a registradora de notificação para exibir o texto completo da notificação.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
