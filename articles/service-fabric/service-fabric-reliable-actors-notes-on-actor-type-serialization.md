<properties
   pageTitle="Confiáveis anotações atores ator digite serialização | Microsoft Azure"
   description="Discute os requisitos básicos para definir classes serializáveis que podem ser usadas para definir estados de serviço tecidos confiável atores e interfaces"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Anotações no serviço tecidos confiável atores digite serialização


Os argumentos de todos os métodos, tipos de resultados das tarefas retornadas por cada método em uma interface de ator e objetos armazenados no Gerenciador de estado de um ator devem ser [Serializáveis de contrato de dados](https://msdn.microsoft.com/library/ms731923.aspx).. Isso também se aplica aos argumentos dos métodos definidos em [interfaces de evento do ator](service-fabric-reliable-actors-events.md#actor-events). (Métodos de interface de evento do ator sempre retornam nulo.)

## <a name="custom-data-types"></a>Tipos de dados personalizados

Neste exemplo, a seguinte interface ator define um método que retorna um tipo de dados personalizado chamado `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

A interface é impelemented por um ator, que usa o Gerenciador de estado para armazenar um `VoicemailBox` objeto:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Neste exemplo, o `VoicemailBox` objeto é serializado quando:
 - O objeto é transmitido entre uma instância de ator e um chamador.
 - O objeto é salvo no Gerenciador de estado onde é mantido em disco e replicado para outros nós.
 
A estrutura de ator confiável usa serialização DataContract. Portanto, os objetos de dados personalizados e seus membros devem ser anotados com os atributos **DataContract** e **DataMember** , respectivamente

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas
 - [Ator do ciclo de vida e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
 - [Timers ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventos de ator](service-fabric-reliable-actors-events.md)
 - [Reentrância ator](service-fabric-reliable-actors-reentrancy.md)
 - [Polimorfismo ator e padrões de design orientados a objeto](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnóstico do ator e monitoramento do desempenho](service-fabric-reliable-actors-diagnostics.md)
