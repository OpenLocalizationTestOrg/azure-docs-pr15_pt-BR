<properties
   pageTitle="Monitorar e diagnosticar serviços criados com o Azure serviço tecidos localmente | Microsoft Azure"
   description="Saiba como monitorar e diagnosticar seus serviços escritos usando tecidos de serviço do Microsoft Azure em uma máquina de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de máquina local


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Monitoramento, detectar, diagnosticar e solução de problemas permitem serviços continuar com o mínimo de interrupção para a experiência do usuário. Monitoramento e diagnóstico é importante em um ambiente de produção implantado real. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funciona quando você move para um ambiente de produção. Serviço tecidos torna mais fácil para os desenvolvedores de serviço implementar o diagnóstico perfeitamente trabalhar nas configurações do computador único local de desenvolvimento e configurações de cluster de produção real.


## <a name="debugging-service-fabric-java-applications"></a>Depuração de aplicativos de serviço tecidos Java

Para aplicativos de Java, [várias estruturas de log](http://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Como `java.util.logging` é a opção padrão com o JRE, ele também é usado para os [exemplos de código github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Discussão a seguir explica como configurar o `java.util.logging` framework. 
 
Usando java.util.logging, você pode redirecionar os logs de aplicativo para memória, fluxos de saída, arquivos de console ou sockets. Para cada uma dessas opções, há manipuladores de padrão já fornecidas no framework. Você pode criar um `app.properties` arquivo para configurar o identificador de arquivo para o seu aplicativo redirecionar todos os logs para um arquivo local. 

O trecho de código a seguir contém um exemplo de configuração: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A pasta apontada pela `app.properties` arquivo deve existir. Após a `app.properties` arquivo é criado, você precisa também modificar seu script de ponto de entrada, `entrypoint.sh` na `<applicationfolder>/<servicePkg>/Code/` pasta para definir a propriedade `java.util.logging.config.file` para `app.propertes` arquivo. A entrada deve parecer com o trecho a seguir:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Essa configuração resulta em logs sendo coletados um modo de rotação em `/tmp/servicefabric/logs/`. A **%u** e **%g** permitem a criação de arquivos mais, com nomes de arquivo mysfapp0.log, mysfapp1.log e assim por diante. Por padrão se nenhum manipulador explicitamente estiver configurado, o manipulador de console é registrado. Um pode exibir os logs de syslog em /var/log/syslog.
 
Para obter mais informações, consulte os [exemplos de código github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Próximas etapas
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico do seu aplicativo em um cluster Azure. Assista a estes artigos que discutam as diferentes opções para as ferramentas e descrevem como você pode configurá-los.
* [Como coletar logs de diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)
