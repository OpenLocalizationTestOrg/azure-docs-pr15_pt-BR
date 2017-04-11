<properties
    pageTitle="Começar com Hubs de evento em c# | Microsoft Azure"
    description="Siga este tutorial para começar usando Hubs de evento do Azure com c# e usando o EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Começar com Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de evento é um serviço que processa grandes quantidades de dados de evento (telemetria) de aplicativos e dispositivos conectados. Depois que você coleta dados em Hubs de evento, você pode armazenar os dados usando um cluster de armazenamento ou transformá-lo usando um provedor de análise em tempo real. Esse recurso de coleta e processamento de evento grande escala é um componente chave de arquiteturas de aplicativos modernas incluindo Internet das coisas (IoT).

Este tutorial mostra como usar o portal de clássico Azure para criar um Hub de evento. Ele também mostra como coletar mensagens a um Hub de evento usando um aplicativo de console escrito em c# e como recuperá-los em paralelo usando a biblioteca de c# [Evento processador Host][] .

Para concluir este tutorial, você precisará o seguinte:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Uma conta do Azure active. Se você não tiver um, você pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. De dentro do Visual Studio, abra o projeto de **receptor** que você criou anteriormente.

2. Clique com botão direito a solução **receptor** , e em seguida, clique em **Adicionar**e clique em **Projeto existente**.
 
3. Localize o arquivo Sender.csproj existente e, em seguida, clique duas vezes nela para adicioná-lo à solução.
 
4. Novamente, a solução de **receptor** de atalho e, em seguida, clique em **Propriedades**. A página de propriedades de **receptor** é exibida.

5. **Projeto de inicialização**, clique no botão de **vários projetos de inicialização** . Configurar a caixa de **ação** para projetos **receptor** tanto o **remetente** para **Iniciar**.

    ![][19]

6. Clique em **dependências do projeto**. Na caixa de **projetos** , clique em **remetente**. Na caixa **depende** , verifique se o **que receptor** está marcada.

    ![][20]

7. Clique em **Okey** para descartar a caixa de diálogo de **Propriedades** .

1.  Pressione F5 para executar o projeto **receptor** no Visual Studio, em seguida, aguarde a iniciar os receptores para todas as partições.

    ![][21]

2.  O projeto de **remetente** será executado automaticamente. Pressione **Enter** na janela do console e ver os eventos aparecem na janela receptor.

    ![][22]

Pressione **Ctrl + C** na janela do **remetente** para encerrar o aplicativo de remetente e pressione **Enter** na janela do receptor para desligar esse aplicativo.

## <a name="next-steps"></a>Próximas etapas

Agora que você tenha criado um aplicativo de trabalho que cria um Hub de evento e envia e recebe dados, você pode mover em para os seguintes cenários:

- Um [aplicativo de exemplo que usa o evento Hubs][]completo.
- A amostra de [escala processamento de eventos com Hubs de evento][] .
- [Visão geral de Hubs de evento][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento processador Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
