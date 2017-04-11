<properties
    pageTitle="O que é Azure evento Hubs? | Microsoft Azure"
    description="Visão geral e descrição dos Hubs de evento do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>O que é Azure evento Hubs?

Azure Hubs de evento é um serviço de ingresso de dados altamente escaláveis que pode incluir milhões de eventos por segundo para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. Hubs de evento atua como a "porta frontal" para um pipeline de evento e depois que os dados coletados a um Hub de evento, que pode ser transformado e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de armazenamento/processamento em lotes. Hubs de evento separa a produção de um fluxo de eventos de consumo desses eventos, para que os consumidores de evento podem acessar os eventos no seu próprio cronograma. Para obter mais informações e detalhes técnicos, consulte [Visão geral de Hubs de evento](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Recursos de Hubs de eventos

Hubs de evento é um serviço que fornece o evento e processamento de telemetria em escala de massa, com alta confiabilidade e baixa latência de processamento de eventos. Esse serviço é especialmente útil para:

- Instrumentação de aplicativo
- Experiência do usuário ou processamento de fluxo de trabalho
- Cenários de Internet das coisas (IoT)

Alguns outros recursos de Hubs de eventos importantes incluem o comportamento de rastreamento de aplicativos móveis, tráfego informações das web farms, captura de eventos de jogo no console jogos ou telemetria coletadas do máquinas industriais ou veículos conectados.

## <a name="next-steps"></a>Próximas etapas

Para obter informações detalhadas sobre Hubs de evento, consulte os tópicos a seguir.

- [Visão geral de Hubs de evento](event-hubs-overview.md)
- [Guia de programação de Hubs de evento](event-hubs-programming-guide.md)
- [Perguntas Frequentes de suporte e disponibilidade de Hubs de evento](event-hubs-availability-and-support-faq.md)
- Começar a usar um [tutorial de Hubs de evento][]
- Um [aplicativo de exemplo que usa o evento Hubs][] de completo

[Tutorial de Hubs de evento]: event-hubs-csharp-ephcs-getstarted.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
