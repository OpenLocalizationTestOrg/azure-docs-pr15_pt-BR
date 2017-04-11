<properties
   pageTitle="Introdução ao serviços confiáveis | Microsoft Azure"
   description="Introdução à criação de um aplicativo de estrutura de serviço do Microsoft Azure com os serviços de com e sem monitoração."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Introdução ao serviços confiáveis

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-services-quick-start.md)
- [Java no Linux](service-fabric-reliable-services-quick-start-java.md)

Este artigo explica as Noções básicas do Azure serviço tecidos confiável serviços e orienta você por meio de criar e implantar um aplicativo de serviço confiável simples escrito em Java.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se que você tem o ambiente de desenvolvimento do serviço tecidos configurar em sua máquina.
Se você precisar configurá-lo, vá para [Introdução no Mac](service-fabric-get-started-mac.md) ou [Introdução no Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar serviços confiáveis, você só precisa compreender alguns conceitos básicos:

 - **Tipo de serviço**: esta é sua implementação de serviço. Ele é definido pela classe que você escreve que estende `StatelessService` e qualquer outro código ou dependências usadas aí, juntamente com um nome e um número de versão.

 - **Instância de serviço nomeada**: para executar seu serviço, você criar instâncias nomeadas do seu tipo de serviço, bem como, criar instâncias de objeto de um tipo de classe. Instâncias de serviço são, na verdade, instâncias de objeto de sua classe de serviço que você escreve. 

 - **Host de serviço**: as instâncias de serviço nomeado que você criar precisam executar dentro de um host. Host do serviço é apenas um processo onde instâncias do seu serviço podem executar.

 - **Registro de serviço**: registro reúne tudo. O tipo de serviço deve ser registrado com o tempo de execução de serviço tecidos em um host de serviço para permitir que o serviço tecidos criar instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço de estado

Comece criando um novo aplicativo de serviço tecidos. O SDK de tecidos do serviço para Linux inclui um Yeoman gerador para fornecer a estrutura de um aplicativo de serviço tecidos com um serviço sem estado. Inicie executando o Yeoman seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço de estado confiável**. Para este tutorial, nomeie o aplicativo "HelloWorldApplication" e o serviço "HelloWorld". O resultado incluirá diretórios para o `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementar o serviço

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API do serviço fornece dois pontos de entrada para seu código:

 - Um método de ponto de entrada abertas, chamado `runAsync()`, onde você pode começar a executar qualquer cargas de trabalho, incluindo cargas de trabalho de computação de execução longa.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Um ponto de entrada de comunicação onde você pode conectar sua pilha de comunicação de escolha. Isso é onde você pode começar a receber solicitações de usuários e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Neste tutorial, nos concentraremos na `runAsync()` método de ponto de entrada. Isso é onde você pode começar imediatamente executar o código.

### <a name="runasync"></a>RunAsync

A plataforma chama este método quando uma instância de um serviço é inserida e pronto para executar. O ciclo de abrir/fechar de uma instância do serviço pode ocorrer muitas vezes no decorrer do serviço como um todo. Isso pode acontecer por vários motivos, incluindo:

- O sistema move seu instâncias de serviço de balanceamento de recursos.
- Falhas ocorrem em seu código.
- O aplicativo ou o sistema é atualizado.
- O hardware subjacente sofrer uma interrupção.

Este coordenação é gerenciada pelo serviço tecidos para manter seu serviço altamente disponíveis e corretamente equilibrada.

#### <a name="cancellation"></a>Cancelamento

É fundamental que seu código em `runAsync()` pode interromper a execução quando notificado pelo serviço tecidos. O `CompletableFuture` retornadas de `runAsync()` é cancelada quando tecidos de serviço requer o seu serviço para interromper a execução. O exemplo a seguir demonstra como tratar um evento de cancelamento: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registro de serviço

Tipos de serviço devem ser registrados com o tempo de execução do serviço tecidos. O tipo de serviço está definido no `ServiceManifest.xml` e sua classe de serviço que implementa `StatelessService`. Registro de serviço é executado no ponto de entrada principal de processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Executar o aplicativo

O Yeoman estrutura inclui um script de gradle para criar o aplicativo e bash scripts para implantar e un-implantar o aplicativo. Para executar o aplicativo, primeiro crie o aplicativo com gradle:

```bash
$ gradle
```

Isso produzirá um pacote de aplicativo de serviço tecidos que pode ser implantado usando o serviço tecidos Azure CLI. O script install.sh contém os comandos do Azure necessários para implantar o pacote de aplicativo. Basta executar o script install.sh para implantar:

```bask
$ ./install.sh
```
