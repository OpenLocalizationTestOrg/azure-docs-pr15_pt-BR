<properties
    pageTitle="Introdução ao evento Hubs em Java | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Azure evento Hubs; enviando eventos com Java e recebê-los usando o EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="core"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Começar com Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de evento é um sistema de inclusão altamente escaláveis que pode tomada de ar milhões de eventos por segundo, ativando um aplicativo para processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. Depois de coletados em Hubs de evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou um cluster de armazenamento.

Para obter mais informações, consulte [Visão geral de Hubs de evento][].

Este tutorial mostra como a inclusão de mensagens a um Hub de evento usando um aplicativo de console em Java e recuperá-las em paralelo usando a biblioteca Java evento processador Host.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento de Java. Para este tutorial, podemos assumirá [Eclipse](https://www.eclipse.org/).

+ Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  Execute o projeto **receptor** .

    ![][21]

2.  Execute o projeto do **remetente** .

    ![][22]

## <a name="next-steps"></a>Próximas etapas

Agora que você tenha criado um aplicativo de trabalho que cria um Hub de evento e envia e recebe dados, você pode mover em para os seguintes cenários:

- Um [aplicativo de exemplo que usa o evento Hubs][]completo.
- A amostra de [escala processamento de eventos com Hubs de evento][] .

Para obter mais informações, consulte o [Java Developer Center](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 