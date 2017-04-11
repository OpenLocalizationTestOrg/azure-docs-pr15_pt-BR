<properties
   pageTitle="Introdução ao serviço tecidos confiável atores | Microsoft Azure"
   description="Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço baseado em ator simples usando o serviço tecidos confiável atores."
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
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introdução ao atores confiável

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-actors-get-started.md)
- [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica as Noções básicas do Azure serviço tecidos confiável atores e orienta você por meio de criação, depuração e implantar um aplicativo de ator confiável simple no Visual Studio.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que você tenha o ambiente de desenvolvimento do serviço tecidos configurar em sua máquina.
Se você precisar configurá-lo, consulte as instruções detalhadas sobre [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar com atores confiável, você só precisa compreender alguns conceitos básicos:

 * **Serviço de ator**. Atores confiáveis são incluídos em serviços confiável que pode ser implantado na infraestrutura de serviço tecidos. Instâncias de ator estão ativadas em uma instância de serviço nomeado.
 
 * **Registro de ator**. Como com serviços confiável, um serviço de ator confiável deve ser registrado com o tempo de execução do serviço tecidos. Além disso, o tipo de ator precisa ser registrado com o tempo de execução do ator.
 
 * **Interface de ator**. A interface do ator é usada para definir uma interface pública acentuada de um ator. Na terminologia modelo ator confiável, a interface de ator define os tipos de mensagens que o ator pode compreender e processo. A interface do ator é usada por outros atores e aplicativos cliente "Enviar" (de forma assíncrona) mensagens para o ator. Atores confiáveis podem implementar várias interfaces.
 
 * **ActorProxy classe**. A classe ActorProxy é usada por aplicativos de cliente para chamar os métodos expostos por meio da interface de ator. A classe ActorProxy fornece duas funcionalidades importantes:
    * Resolução de nomes: é capaz de localizar o ator no cluster (localizar o nó do cluster onde ele é hospedado).
    * Manipulação de falha: ele pode repetir chamadas de método e resolver novamente o local do ator após, por exemplo, uma falha que requer o ator de ser realocada para outro nó no cluster.

As seguintes regras que pertencem aos interfaces ator são vale a pena mencionar:

- Métodos de interface do ator não podem ser sobrecarregados.
- Interface de ator métodos não pode ter check-out, ref ou parâmetros opcionais.
- Interfaces genéricas não são suportadas.

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio
Depois que você tiver instalado as ferramentas de estrutura de serviço para Visual Studio, você pode criar novos tipos de projeto. Os novos tipos de projeto estão sob a categoria de **nuvem** da caixa de diálogo **Novo projeto** .


![Ferramentas de estrutura de serviço para Visual Studio - novo projeto][1]

Na caixa de diálogo seguinte, você pode escolher o tipo de projeto que você deseja criar.

![Modelos de projeto de estrutura de serviço][5]

Para o projeto HelloWorld, vamos usar o serviço do serviço tecidos confiável atores.

Após ter criado a solução, você verá a seguinte estrutura:

![Estrutura de projeto de estrutura de serviço][2]

## <a name="reliable-actors-basic-building-blocks"></a>Blocos de construção básicos atores confiáveis

Uma solução confiável atores típica é composta por três projetos:

* **O projeto de aplicativo (MyActorApplication)**. Este é o projeto que pacotes de todos os serviços juntos para implantação. Ele contém os scripts *ApplicationManifest.xml* e PowerShell para gerenciar o aplicativo.

* **O projeto de interface (MyActor.Interfaces)**. Este é o projeto que contém a definição de interface para o ator. No projeto MyActor.Interfaces, você pode definir as interfaces que serão usadas pelos atores na solução. Suas interfaces ator podem ser definidos em qualquer projeto com qualquer nome, mas a interface define o contrato de ator que é compartilhado pela implementação de ator e os clientes chamando ator, então, geralmente faz sentido defini-lo em um assembly que é separado da implementação de ator e pode ser compartilhado por vários outros projetos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **O projeto de serviço do ator (MyActor)**. Este é o projeto usado para definir o serviço de estrutura de serviço que vai hospedar o ator. Ele contém a implementação do ator. Uma implementação de ator é uma classe que deriva do tipo base `Actor` e implementa as interfaces que são definidos no projeto MyActor.Interfaces. Uma classe de ator também deve implementar um construtor que aceita um `ActorService` instância e um `ActorId` e passa para a base `Actor` classe. Isso permite inclusão de dependência de construtor de dependências de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

O serviço de ator deve ser registrado com um tipo de serviço no runtime tecidos de serviço. Em ordem para o serviço de ator para executar suas instâncias de ator, seu tipo de ator também deve ser registrado com o serviço de ator. O `ActorRuntime` método de registro executa esse trabalho para atores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se você inicia a partir de um novo projeto no Visual Studio e você tiver apenas uma definição de ator, o registro é incluído por padrão no código que o Visual Studio gera. Se você definir outros atores no serviço, você precisa adicionar o registro de ator usando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] O tempo de execução do serviço tecidos atores emite alguns [contadores de desempenho e eventos relacionam a métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis de diagnóstico e monitoramento do desempenho.


## <a name="debugging"></a>Depuração

As ferramentas de estrutura de serviço para Visual Studio oferecem suporte a depuração em sua máquina local. Você pode iniciar uma sessão de depuração pressionando a tecla F5. O Visual Studio cria (se necessário) pacotes. Também implanta o aplicativo no local cluster tecidos de serviço e anexa o depurador.

Durante o processo de implantação, você pode ver o andamento na janela de **saída** .

![Janela de saída de depuração de estrutura de serviço][3]


## <a name="next-steps"></a>Próximas etapas
 - [Como confiável atores usar a plataforma de estrutura de serviço](service-fabric-reliable-actors-platform.md)
 - [Gerenciamento de estado de ator](service-fabric-reliable-actors-state-management.md)
 - [Ator do ciclo de vida e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
 - [Documentação de referência de ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
