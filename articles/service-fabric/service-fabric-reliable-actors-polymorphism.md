<properties
   pageTitle="Polimorfismo no framework atores confiável | Microsoft Azure"
   description="Crie hierarquias de interfaces .NET e tipos no framework atores confiável reutilizar definições de API e funcionalidade."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo no framework atores confiável

A estrutura de atores confiável permite construir atores usando muitas das mesmas técnicas que você deseja usar no design orientados a objeto. Uma dessas técnicas é polimorfismo, que permite tipos e interfaces para herdar de mais generalizado pais. Herança no framework atores confiável geralmente segue o modelo de .NET com algumas restrições adicionais.

## <a name="interfaces"></a>Interfaces

A estrutura de atores confiável requer que você defina pelo menos uma interface sejam implementadas pelo seu tipo de ator. Essa interface é usada para gerar uma classe proxy que pode ser usada por clientes para se comunicar com seus atores. Interfaces podem herdar de outras interfaces desde que cada interface implementado por um tipo de ator e todos os seus pais basicamente deriva IActor. IActor é a interface de base definidas pelo plataforma de atores. Portanto, o exemplo de polimorfismo clássico usando formas poderia ser algo parecido com isto:

![Hierarquia de interface para atores de forma][shapes-interface-hierarchy]


## <a name="types"></a>Tipos de

Você também pode criar uma hierarquia de tipos de ator, que são derivadas da classe de ator base fornecida pela plataforma. No caso de formas, você pode ter uma base `Shape` tipo:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes de `Shape` pode substituir métodos da base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Observação a `ActorService` atributo do tipo de ator. Esse atributo instrui o framework ator confiável que ele deve criar automaticamente um serviço de hospedagem atores desse tipo. Em alguns casos, você talvez queira criar um tipo de base que destina unicamente compartilhando funcionalidade com subtipos e nunca será usado para criar uma instância de concretos atores. Nesses casos, você deve usar o `abstract` palavra-chave para indicar que você nunca criará um ator com base nesse tipo.


## <a name="next-steps"></a>Próximas etapas

- Veja [como a estrutura de atores confiável utiliza a plataforma de serviço tecidos](service-fabric-reliable-actors-platform.md) para fornecer confiabilidade, escalabilidade e estado consistente.
- Saiba mais sobre o [ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
