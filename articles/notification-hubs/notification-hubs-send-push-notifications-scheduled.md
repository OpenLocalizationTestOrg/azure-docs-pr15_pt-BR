<properties
    pageTitle="Como enviar notificações agendadas | Microsoft Azure"
    description="Este tópico descreve como usar notificações agendada com Hubs de notificação do Azure."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notificações por push, notificação, agendamento de notificações de envio por push"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas


##<a name="overview"></a>Visão geral

Se você tiver um cenário em que você deseja enviar uma notificação em algum momento no futuro, mas não têm uma maneira fácil de despertar seu código de back-end para enviar a notificação. Nível padrão Hubs de notificação oferece suporte a um recurso que permite agendar notificações para cima para 7 dias no futuro.

Ao enviar uma notificação, basta use a classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK Hubs notificação conforme mostrado no exemplo a seguir:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Além disso, você pode cancelar uma notificação agendada anteriormente usando seu notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Não há nenhum limite no número de notificações agendadas, que você pode enviar.