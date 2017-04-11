<properties
    pageTitle="Roteamento e expressões de marca"
    description="Este tópico explica roteamento e marca expressões para hubs de notificação Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Expressões de roteamento e marca

##<a name="overview"></a>Visão geral

Expressões de marca permitem que você direcionar conjuntos específicos de dispositivos, ou mais especificamente registros, ao enviar uma notificação de envio por meio de Hubs de notificação.


## <a name="targeting-specific-registrations"></a>Direcionamento registros específicos

A única maneira de destino notificação específica registros é associar marcas-los, direcionar essas marcas. Conforme discutido no [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md), para receber notificações por push um aplicativo tem que registrar um identificador de dispositivo em um hub de notificação. Depois que um registro é criado em um hub de notificação, o back-end do aplicativo pode enviar notificações por push para ele.
O back-end do aplicativo pode escolher os registros de destino com uma notificação específica das seguintes maneiras:

1. **Transmitir**: todos os registros no hub do notificação recebem a notificação.
2. **Marca**: todos os registros que contêm a marca especificada recebem a notificação.
3. **Expressão de marca**: todos os registros cujo conjunto de marcas correspondem a expressão especificada recebem a notificação.

## <a name="tags"></a>Marcas

Uma marca pode ser qualquer cadeia de caracteres, até 120 caracteres contendo alfanuméricos e os seguintes caracteres não alfanuméricos: '_', ‘@’, '#', '. ',':', '-'. O exemplo a seguir mostra um aplicativo do qual você pode receber notificações do sistema sobre os grupos de música específico. Neste cenário, uma forma simples de notificações de rota é registros de etiqueta com marcas que representam as faixas diferentes, como a imagem a seguir.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Nesta figura, a mensagem marcadas **Beatles** atinge somente o tablet que registrada com a marca **Beatles**.

Para obter mais informações sobre como criar registros de marcas, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

Você pode enviar notificações para marcas utilizando os métodos de notificações de envio do `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe em [Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Você também pode usar Node ou as APIs REST de notificações por Push.  Aqui está um exemplo usando o SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Marcas não precisa ser previamente provisionado e podem se referir a vários conceitos de aplicativo específicos. Por exemplo, os usuários deste aplicativo de exemplo podem comentar faixas e deseja receber brindes, não apenas para os comentários em seus favoritos faixas, mas também para todos os comentários de seus amigos, independentemente da faixa na qual elas são comentários. A figura a seguir mostra um exemplo desse cenário:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Nesta figura, Alice está interessada em atualizações de Beatles e Paulo está interessado em atualizações para os Wailers. Paulo também está interessado em comentários de organização e organização está em interessado nas Wailers. Quando uma notificação é enviada para o comentário de organização em Beatles, Alice e Paulo recebê-lo.

Enquanto você pode codificar vários preocupações em marcas (por exemplo, "band_Beatles" ou "follows_Charlie"), marcas são cadeias de caracteres simples e não propriedades com valores. Um registro é correspondido apenas na presença ou ausência de uma marca específica.

Para um tutorial completo passo a passo sobre como usar marcas para enviar aos grupos de interesse, consulte [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Usando marcas para usuários de destino

Outra maneira de usar marcas é identificar todos os dispositivos de um usuário específico. Registros podem ser marcados com uma marca que contém uma id de usuário, como a imagem a seguir:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Nesta figura, o uid:Alice mensagens marcadas atinge todos os registros uid:Alice marcadas; Portanto, todos os dispositivos de Alice.


##<a name="tag-expressions"></a>Expressões de marca

Há casos em que uma notificação tem a um conjunto de registros que é identificado não por uma única marca, mas por uma expressão booliana em marcas de destino.

Considere a possibilidade de um aplicativo de esportes que envia um lembrete para todos em Belo Horizonte sobre um jogo entre o Red Sox e Cardinals. Se o aplicativo cliente registra marcas sobre interesse em equipes e local, a notificação deve ser direcionada para todos em Belo Horizonte quem está interessado no Sox vermelho ou o Cardinals. Esta condição pode ser expresso com a seguinte expressão booliana:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Expressões de marca podem conter todos os operadores booleanos, como e (& &), ou (|) e não (!). Eles também podem conter parênteses. Expressões de marca limitam-se a 20 marcas se contiverem apenas ORs; Caso contrário, eles são limitados a 6 marcas.

Aqui está um exemplo para o envio de notificações com expressões de marca usando o SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
