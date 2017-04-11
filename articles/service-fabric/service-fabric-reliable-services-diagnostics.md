<properties
   pageTitle="Diagnóstico de serviços confiáveis com estado | Microsoft Azure"
   description="Funcionalidade de diagnóstico para serviços confiáveis com informações de estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico para serviços confiáveis com informações de estado
A classe de estado confiável serviços StatefulServiceBase emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que podem ser usados para depurar o serviço, fornecem visões como o tempo de execução está funcionando e ajudar a solucionar problemas.

## <a name="eventsource-events"></a>EventSource eventos
O nome de EventSource para a classe de estado confiável serviços StatefulServiceBase é "Microsoft-ServiceFabric-Services". Eventos de origem este evento aparecem na janela de [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajude a coletar e/ou exibindo EventSource eventos são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure diagnósticos](../cloud-services/cloud-services-dotnet-diagnostics.md)e a [Biblioteca de TraceEvent da Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos

|Nome do evento|Identificação do evento|Nível|Descrição do evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativo|Emitido quando a tarefa de RunAsync do serviço é iniciada|
|StatefulRunAsyncCancellation|2|Informativo|Emitido quando a tarefa de RunAsync de serviço foi cancelada|
|StatefulRunAsyncCompletion|3|Informativo|Emitido quando serviço RunAsync tarefa é concluída|
|StatefulRunAsyncSlowCancellation|4|Aviso|Emitido quando a tarefa do serviço RunAsync for muito demorada para concluir o cancelamento|
|StatefulRunAsyncFailure|5|Erro|Emitida quando a tarefa do serviço RunAsync gera uma exceção|

## <a name="interpret-events"></a>Interpretar eventos

Eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o gravador de serviço para entender o ciclo de vida de um serviço, bem como o intervalo para quando um serviço é iniciado, cancelado ou concluído. Isso pode ser útil quando depurar problemas de serviço ou Noções básicas sobre o ciclo de vida do serviço.

Criadores de serviço devem prestar atenção eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitida sempre que a tarefa de RunAsync() do serviço gera uma exceção. Normalmente, uma exceção acionada indica um erro ou bugs no serviço. Além disso, a exceção faz com que o serviço falhar, para que ele é movido para um nó diferente. Isso pode ser uma operação cara e pode atrasar solicitações de entrada enquanto o serviço é movido. Autores de serviço devem determinar a causa de exceção e, se possível, reduzi-la.

StatefulRunAsyncSlowCancellation é emitida sempre que uma solicitação de cancelamento da tarefa RunAsync leva mais de quatro segundos. Quando um serviço for muito demorado para concluir o cancelamento, ele afeta a capacidade do serviço rapidamente ser reiniciado em outro nó. Isso pode afetar a disponibilidade geral do serviço.
