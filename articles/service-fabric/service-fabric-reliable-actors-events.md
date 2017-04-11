<properties
   pageTitle="Eventos de atores confiáveis | Microsoft Azure"
   description="Introdução a eventos de serviço tecidos confiável atores."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Eventos de ator
Eventos de ator oferecem uma maneira de enviar notificações de melhor esforço do ator para os clientes. Eventos de ator foram projetados para comunicação de ator para cliente e não devem ser usados para comunicação de ator ao ator.

Os trechos de código a seguir mostram como usar ator eventos em seu aplicativo.

Defina uma interface que descreve os eventos publicados por ator. Esta interface deve ser derivada do `IActorEvents` interface. Os argumentos dos métodos devem ser [serializáveis de contrato de dados](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar nulo, como evento notificações são uma maneira e melhor esforço.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Declare os eventos publicados por ator na interface do ator.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

No lado do cliente, implemente o manipulador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

No cliente, crie um proxy para o ator que publica o evento e assinar seus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

Em caso de failovers, ator talvez não consiga sobre um processo diferente ou de um nó. O proxy de ator gerencia as inscrições ativas e automaticamente assina-las novamente. Você pode controlar o intervalo de nova assinatura por meio do `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para cancelar a assinatura, use o `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Sobre o ator, publica os eventos conforme eles ocorrem. Se houver assinantes ao evento, o tempo de execução de atores envia-los a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Próximas etapas
 - [Reentrância ator](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnóstico do ator e monitoramento do desempenho](service-fabric-reliable-actors-diagnostics.md)
 - [Documentação de referência de ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)
