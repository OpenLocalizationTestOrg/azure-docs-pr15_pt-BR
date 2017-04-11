<properties
    pageTitle="Começar com Hubs de evento em c# | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Azure evento Hubs; enviando eventos em c# e recebê-los em Java usando o EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Começar com Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de evento é um serviço que processa grandes quantidades de dados de evento (telemetria) de aplicativos e dispositivos conectados. Depois que você coleta dados em Hubs de evento, você pode armazenar os dados usando um cluster de armazenamento ou transformá-lo usando um provedor de análise em tempo real. Esse recurso de coleta e processamento de evento grande escala é um componente chave de arquiteturas de aplicativos modernas incluindo Internet das coisas (IoT).

Este tutorial mostra como usar o portal de clássico Azure para criar um Hub de evento. Ele também mostra como coletar mensagens a um Hub de evento usando um aplicativo de console escrito em c# e como recuperá-las em paralelo usando a biblioteca Java evento processador Host.

Para concluir este tutorial, você precisará o seguinte:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Uma conta do Azure active. <br/>Se você não tiver um, você pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

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
- [Visão geral de Hubs de evento][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
