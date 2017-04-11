<properties
    pageTitle="Introdução ao evento Hubs em Java com tempestade Apache | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Azure evento Hubs; enviando eventos com Java e recebê-los em um cluster de tempestade Apache."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>Começar com Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de evento é um sistema de inclusão altamente escaláveis que pode tomada de ar milhões de eventos por segundo, ativando um aplicativo para processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. Depois de coletados em Hubs de evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou um cluster de armazenamento.

Para obter mais informações, consulte [Visão geral de Hubs de evento][].

Este tutorial descreve como coletar mensagens a um Hub de evento usando um aplicativo de console em Java e recuperá-las em paralelo usando Apache tempestade.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos supor [Eclipse](https://www.eclipse.org/).

+ Uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  Executar a classe de **LogTopology** do Eclipse e, em seguida, aguarde a iniciar os receptores para todas as partições.

2.  Executar o projeto de **remetente** , pressione **Enter** na janela do console e ver os eventos aparecem na janela receptor.

    ![][22]

> [AZURE.NOTE] Somente neste tutorial, use tempestade no modo local para fins de desenvolvimento. Consulte a [Visão geral de tempestade de HDInsight][] e a documentação de [Tempestade Apache][] oficial para obter mais informações de implantações de tempestade e padrões.

## <a name="next-steps"></a>Próximas etapas

Os seguintes recursos estão disponíveis para desenvolver aplicativos integração Hubs de evento e tempestade.

- [Analisar dados de sensor com tempestade e HDInsight] é um tutorial de cenário completo usando Hubs de evento, tempestade e HBase para receber dados de sensor em um cluster de Hadoop.
- [Aplicativos de processamento de dados com SCP.NET e c# em tempestade e HDInsight de fluxo de desenvolver][] é um tutorial sobre como escrever canais de tempestade usando c#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de evento]: event-hubs-overview.md

[Apache tempestade]: https://storm.incubator.apache.org
[Visão geral de HDInsight tempestade]: ../hdinsight/hdinsight-storm-overview.md
[Analisar dados de sensor com tempestade e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desenvolver aplicativos de processamento de dados com SCP.NET e c# em tempestade e HDInsight de fluxo]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 