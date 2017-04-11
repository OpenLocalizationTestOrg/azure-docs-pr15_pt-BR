<properties
    pageTitle="Começar com Hubs de evento em c# com tempestade Apache | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Azure evento Hubs; enviando eventos em c# e recebê-los em um cluster de tempestade Apache."
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
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Começar com Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de evento é um sistema de inclusão altamente escaláveis que pode tomada de ar milhões de eventos por segundo, ativando um aplicativo para processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. Depois de coletados em Hubs de evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou um cluster de armazenamento.

Para obter mais informações, consulte a [Visão geral de Hubs de evento].

Neste tutorial, você aprenderá a inclusão mensagens a um Hub de evento usando um aplicativo de console em c# e recuperá-las em paralelo usando Apache tempestade.

Para concluir este tutorial, você precisará do seguinte:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos supor [Eclipse](https://www.eclipse.org/)

+ Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  Executar a classe de **LogTopology** do Eclipse e, em seguida, aguarde a iniciar os receptores para todas as partições.

2.  Executar o projeto de **remetente** , pressione **Enter** na janela do console e ver os eventos aparecem na janela receptor.

    ![][22]

## <a name="next-steps"></a>Próximas etapas

Agora que você tenha criado um aplicativo de trabalho que cria um Hub de evento e envia e recebe dados, você pode mover em para os seguintes cenários:

- Um [aplicativo de exemplo que usa o evento Hubs][]completo.
- A amostra de [escala processamento de eventos com Hubs de evento][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 