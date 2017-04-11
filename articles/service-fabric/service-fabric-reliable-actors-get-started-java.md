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
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introdução ao atores confiável

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-actors-get-started.md)
- [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica as Noções básicas do Azure serviço tecidos confiável atores e orienta você por meio de criar e implantar um aplicativo de ator confiável simples em Java.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se que você tem o ambiente de desenvolvimento do serviço tecidos configurar em sua máquina.
Se você precisar configurá-lo, vá para [Introdução no Mac](service-fabric-get-started-mac.md) ou [Introdução no Linux](service-fabric-get-started-linux.md).

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

## <a name="create-an-actor-service"></a>Criar um serviço de ator
Comece criando um novo aplicativo de serviço tecidos. O SDK de tecidos do serviço para Linux inclui um Yeoman gerador para fornecer a estrutura de um aplicativo de serviço tecidos com um serviço sem estado. Inicie executando o Yeoman seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço de ator confiável**. Para este tutorial, nomeie o aplicativo "HelloWorldActorApplication" e o ator "HelloWorldActor". A estrutura a seguir será criada:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocos de construção básicos atores confiáveis

Os conceitos básicos descritos anteriormente traduzem em blocos de construção básicos de um serviço de ator confiável.

### <a name="actor-interface"></a>Interface de ator

Esta página contém a definição de interface para o ator. Essa interface define o contrato de ator que é compartilhado com a implementação de ator e os clientes chamando o ator, para que ele normalmente faz sentido defini-la em um local que é separado da implementação de ator e pode ser compartilhado por vários outros serviços ou aplicativos cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço de ator 
Esta página contém a implementação do ator e o código de registro do ator. A classe de ator implementa a interface de ator. Isso é onde seu ator faz seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registro de ator

O serviço de ator deve ser registrado com um tipo de serviço no runtime tecidos de serviço. Em ordem para o serviço de ator para executar suas instâncias de ator, seu tipo de ator também deve ser registrado com o serviço de ator. O `ActorRuntime` método de registro executa esse trabalho para atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Cliente de teste

Este é um aplicativo de cliente de teste simples que você pode executar separadamente do aplicativo de serviço tecidos para testar seu serviço de ator. Este é um exemplo de onde o ActorProxy pode ser usado para ativar e se comunicar com instâncias de ator. Não são implantada com o seu serviço.

### <a name="the-application"></a>O aplicativo 

Finalmente, o aplicativo compacta o serviço de ator e quaisquer outros serviços que você pode adicionar no futuro juntos para implantação. Ele contém os proprietários *ApplicationManifest.xml* e local para o pacote de serviço do ator.

## <a name="run-the-application"></a>Executar o aplicativo

O Yeoman estrutura inclui um script de gradle para criar o aplicativo e bash scripts para implantar e un-implantar o aplicativo. Para executar o aplicativo, primeiro crie o aplicativo com gradle:

```bash
$ gradle
```

Isso produzirá um pacote de aplicativo de serviço tecidos que pode ser implantado usando o serviço tecidos Azure CLI. O script install.sh contém os comandos do Azure necessários para implantar o pacote de aplicativo. Basta executar o script install.sh para implantar:

```bask
$ ./install.sh
```
