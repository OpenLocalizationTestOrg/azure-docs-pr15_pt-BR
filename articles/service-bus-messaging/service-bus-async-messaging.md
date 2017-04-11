<properties 
    pageTitle="Barramento de serviço de mensagens assíncrono | Microsoft Azure"
    description="Descrição do serviço de mensagens assíncrono do barramento de serviço."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronas e alta disponibilidade

Mensagens assíncronas podem ser implementada de várias maneiras diferentes. Com filas, tópicos e assinaturas, barramento de serviço do Azure suporta assincronia por meio de um mecanismo encaminhar e de armazenamento. Em operação (síncrona) normal, você envia mensagens para filas e tópicos e recebe mensagens de filas e assinaturas. Aplicativos que você escreve dependem essas entidades sempre sendo disponíveis. Quando a integridade de entidade alterada, devido a uma variedade de circunstâncias, você precisa de forma a fornecer uma entidade de funcionalidade reduzida que pode atender a maioria das necessidades.

Aplicativos normalmente usam padrões de mensagens assíncronas para habilitar um número de cenários de comunicação. Você pode criar aplicativos na qual a clientes podem enviar mensagens para serviços, mesmo quando o serviço não está sendo executado. Para aplicativos experiência explosões de comunicações, uma fila podem ajudar nivelar a carga, fornecendo um local para comunicações de buffer. Por fim, você pode obter um balanceador de carga simples, mas eficaz para distribuir mensagens em várias máquinas.

Para manter a disponibilidade de qualquer uma dessas entidades, considere a possibilidade de várias maneiras diferentes, em que essas entidades podem aparecer não está disponíveis para um sistema de mensagens durável. Em geral, vemos a entidade ficam indisponíveis aos aplicativos que podemos escrever das seguintes maneiras diferentes:

- Não é possível enviar mensagens.

- Não é possível receber mensagens.

- Não é possível gerenciar entidades (criar, recuperar, atualizar ou excluir entidades).

- Não é possível contatar o serviço.

Para cada uma dessas falhas, diferentes modos de falha existem que permitem que um aplicativo continue realizar o trabalho em algum nível de funcionalidade reduzida. Por exemplo, um sistema que pode enviar mensagens mas não recebê-los ainda pode receber pedidos de clientes, mas não é possível processar pedidos. Este tópico aborda possíveis problemas que podem ocorrer e como esses problemas são reduzidos. Barramento de serviço introduziu um número de reduções que você deve optar pelo e este tópico também descreve as regras que regem o uso dessas reduções opt-in.

## <a name="reliability-in-service-bus"></a>Confiabilidade no carregador de serviço

Há várias maneiras para lidar com problemas de mensagem e entidade e há diretrizes que regem o uso apropriado dessas reduções. Para compreender as diretrizes, você deve primeiro compreender o que pode falhar no carregador de serviço. Devido ao projeto do Azure sistemas, todos esses problemas tendem a ser curta duração. Em um alto nível, as causas diferentes indisponibilidade aparecem da seguinte maneira:

-   A limitação de um sistema externo que depende barramento de serviço. A limitação ocorre de interações com os recursos de computação e de armazenamento.

-   Problema para um sistema quais depende barramento de serviço. Por exemplo, uma determinada parte do armazenamento pode encontrar problemas.

-   Falha do serviço barramento no subsistema único. Nessa situação, um nó de computação teremos em um estado inconsistente e reinicie a mesmo, fazendo com que todas as entidades que ele serve para carregar saldo para outros nós. Por sua vez, isso pode causar um curto período de processamento de mensagem lenta.

-   Falha do barramento de serviço em um data center Azure. Este é uma "falha grave" durante o qual o sistema está inacessível por muitos minutos ou algumas horas.

> [AZURE.NOTE] O termo **armazenamento** pode significam que o armazenamento do Azure e SQL Azure.

Barramento de serviço contém um número de atenuação para esses problemas. As seções a seguir discutem cada problema e seus respectivas reduções.

### <a name="throttling"></a>A limitação

Com barramento de serviço, a otimização permite o gerenciamento de taxas de mensagem cooperativo. Cada nó barramento de serviço individual hospeda muitas entidades. Cada uma dessas entidades exige o sistema em termos de CPU, memória, armazenamento e outros aspectos. Quando qualquer um desses aspectos detecta uso que exceder os limites definidos, barramento de serviço pode negar uma determinada solicitação. O chamador recebe um [ServerBusyException][] e tentativas após 10 segundos.

Como um fator atenuante, o código deve ler o erro e interromper qualquer tentativas da mensagem pelo menos 10 segundos. Desde que o erro pode ocorrer em partes do aplicativo cliente, esperamos que cada parte independentemente executa a lógica de repetição. O código pode reduzir a probabilidade de sendo limitado habilitando partição em fila ou tópico.

### <a name="issue-for-an-azure-dependency"></a>Problema para uma dependência Azure

Outros componentes dentro do Azure ocasionalmente podem ter problemas de serviço. Por exemplo, quando um sistema que usa barramento de serviço está sendo atualizado, sistema temporariamente pode experimentar recursos reduzidos. Para contornar esses tipos de problemas, barramento de serviço regularmente investiga e implementa reduções. Efeitos de lado dessas reduções aparecem. Por exemplo, para lidar com problemas temporárias com armazenamento, barramento de serviço implementa um sistema que permite operações de envio de mensagens trabalhar de forma consistente. Devido à natureza da atenuação, uma mensagem enviada pode levar até 15 minutos para aparecer na fila afetada ou assinatura e prepare-se para uma operação de recebimento. Em geral, a maioria das entidades não terão esse problema. No entanto, dado o número de entidades no carregador de serviço de dentro do Azure, essa atenuação é às vezes, necessário para um pequeno subconjunto dos clientes do barramento de serviço.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha de barramento de serviço em um único subsistema

Com qualquer aplicativo, circunstâncias podem causar um componente interno de barramento de serviço para tornar-se inconsistente. Quando o barramento de serviço detecta isso, ele coleta de dados do aplicativo para ajudar no diagnóstico o que aconteceu. Depois que os dados são coletados, o aplicativo é reiniciado em uma tentativa de retorná-lo para um estado consistente. Esse processo acontece rapidamente, e os resultados em uma entidade apareçam para ficar indisponível por até alguns minutos, embora típico para baixo vezes são muito menores.

Nesses casos, o aplicativo cliente gera uma exceção [System. TimeoutException][] ou [MessagingException][] . Serviço barramento contém uma atenuação para esse problema na forma de lógica de repetição automatizada de cliente. Depois de acabar o período de repetição e a mensagem não foi entregue, você poderá explorar usando outros recursos, como [namespaces pares][]. Namespaces pares tem outras restrições descritos nesse artigo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Falha de barramento de serviço em um data center Azure

O motivo mais provável uma falha em um data center Azure é uma falha na atualização da implantação do barramento de serviço ou um sistema dependente. Como a plataforma amadureceu, a probabilidade desse tipo de falha tem diminuído. Uma falha de data center também pode acontecer por razões que incluem o seguinte:

-   Interrupção elétrica (alimentação e gerar power desaparecem).
-   Conectividade (quebra de internet entre seus clientes e Azure).

Em ambos os casos, um desastre natural ou causado pelo homem causou o problema. Para contornar esse problema e certifique-se de que você ainda pode enviar mensagens, você pode usar [namespaces pares][] para habilitar mensagens sejam enviadas em um segundo local enquanto o local principal é feito Íntegro novamente. Para obter mais informações, consulte [práticas recomendadas para aplicativos proteger contra interrupções de barramento de serviço e desastres][].

## <a name="paired-namespaces"></a>Namespaces pares

O recurso de [namespaces pares][] oferece suporte a cenários em que uma entidade de barramento de serviço ou a implantação em um data center fica indisponível. Enquanto este evento ocorre raramente, sistemas distribuídos ainda devem estar preparados para manipular piores cenários. Normalmente, esse evento acontece porque algum elemento quais depende barramento de serviço está enfrentando um problema temporário. Para manter a disponibilidade de aplicativo durante uma interrupção, usuários de barramento de serviço podem usar dois namespaces separadas, preferência em centros de dados separado para hospedar seus entidades mensagens. O resto desta seção usa a seguinte terminologia:

-   Namespace principal: O namespace com o qual o seu aplicativo interage, para enviar e receber operações.

-   Namespace secundário: namespace que atua como um backup ao namespace principal. Lógica de aplicativo não interagem com esse namespace.

-   Intervalo failover: A quantidade de tempo para aceitar falhas normais antes do aplicativo alterna do namespace primário ao namespace secundário.

Par namespaces *Enviar disponibilidade*de suporte. Envie disponibilidade preserva a capacidade de enviar mensagens. Para usar a disponibilidade de envio, seu aplicativo deve atender aos seguintes requisitos:

1.  Mensagens são recebidas apenas do namespace principal.

2.  As mensagens enviadas para uma determinada fila ou tópico pode chegar fora de ordem.

3.  Se seu aplicativo usa sessões, mensagens dentro de uma sessão podem chegar fora de ordem. Esta é uma quebra de funcionalidade normal de sessões. Isso significa que seu aplicativo usa as sessões logicamente agrupar mensagens. Estado de sessão é mantido apenas no namespace principal.

4.  Mensagens dentro de uma sessão podem chegam fora de ordem. Esta é uma quebra de funcionalidade normal de sessões. Isso significa que seu aplicativo usa as sessões logicamente agrupar mensagens.

5.  Estado de sessão é mantido apenas no namespace principal.

6.  Fila principal pode ficar online e comece a aceitar mensagens antes de fila secundária oferece todas as mensagens na fila principal.

As seções a seguir discutem as APIs, como as APIs são implementadas e mostra o código de exemplo que usam o recurso. Observe que há implicações de cobranças associadas a esse recurso.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>A API MessagingFactory.PairNamespaceAsync

O recurso de namespaces pares inclui o método [PairNamespaceAsync][] na classe [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando a tarefa é concluída, o namespace emparelhamento também está concluído e pronto para agir sobre para qualquer [MessageReceiver][], [QueueClient][], ou [TopicClient][] criados com a instância de [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] é a classe base para os diferentes tipos de emparelhamento que estão disponíveis com um objeto [MessagingFactory][] . Atualmente, a única classe derivada é um nomeado [SendAvailabilityPairedNamespaceOptions][], que implementa os requisitos de disponibilidade de envio. [SendAvailabilityPairedNamespaceOptions][] tem um conjunto de construtores que criam uns dos outros. Observando o construtor com a maioria dos parâmetros, você pode compreender o comportamento dos outros construtores.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Esses parâmetros têm os seguintes significados:

-   *secondaryNamespaceManager*: uma instância de [Gerenciador de namespace][] inicializada para o namespace secundário que o método de [PairNamespaceAsync][] pode usar para configurar o namespace secundário. O Gerenciador de namespace é usado para obter a lista de filas no namespace e certifique-se de que as filas de acúmulo necessários existem. Se essas filas não existir, eles são criados. [Gerenciador de namespace][] requer a capacidade de criar um token com a declaração de **Gerenciar** .

-   *messagingFactory*: A instância de [MessagingFactory][] do namespace secundário. O objeto [MessagingFactory][] é usado para enviar e, se a propriedade [EnableSyphon][] é definida como **true**, receber mensagens de filas de registro posterior.

-   *backlogQueueCount*: O número de filas de acúmulo para criar. Este valor deve ser pelo menos 1. Ao enviar mensagens para o registro acumulado, uma dessas filas aleatoriamente é escolhida. Se você definir o valor para 1, apenas uma fila nunca pode ser usada. Quando isso acontece e fila de registro um posterior gera erros, o cliente não poderá tentar uma fila de registro posterior diferentes e pode falhar ao enviar sua mensagem. É recomendável configurar esse valor para alguns maior valor e padrão o valor 10. Você pode alterar isso para um valor maior ou menor dependendo de quantos dados seu aplicativo envia por dia. Cada fila de registro posterior pode conter até 5 GB de mensagens.

-   *failoverInterval*: A quantidade de tempo durante o qual você aceitará falhas no namespace primária antes de trocar qualquer jurídica ao namespace secundário. Failovers ocorrer em uma base de entidade por entidade. Entidades em um único namespace frequentemente ao vivo em nós diferentes dentro de barramento de serviço. Uma falha em uma entidade não sugerem uma falha em outro. Você pode definir esse valor para [System.TimeSpan.Zero][] failover para o secundário imediatamente após sua falha primeira, não transitório. Falhas que disparam o cronômetro failover são qualquer [MessagingException][] no qual a propriedade [IsTransient][] for falso, ou um [System. TimeoutException][]. Outras exceções, como [UnauthorizedAccessException][] não farão failover, porque indicam que o cliente está configurado incorretamente. Um [ServerBusyException][] não causa failover porque o padrão correto é aguardar 10 segundos, envie a mensagem novamente.

-   *enableSyphon*: indica que esse emparelhamento determinado deve também syphon mensagens do namespace secundário de volta ao namespace principal. Em geral, aplicativos que enviam mensagens devem definir esse valor como **false**; aplicativos que recebem mensagens devem definir esse valor como **true**. O motivo para isso é que com frequência, há menos receptores de mensagens de remetentes de mensagens. Dependendo do número de receptores, você pode optar por ter uma instância de único aplicativo lidar com as tarefas de sifão. Usar vários receptores tem implicações de cobranças para cada fila de registro posterior.

Para usar o código, crie uma instância de [MessagingFactory][] primária, uma instância de [MessagingFactory][] secundária, uma instância de [Gerenciador de namespace][] secundária e uma instância de [SendAvailabilityPairedNamespaceOptions][] . A chamada pode ser tão simple quanto o seguinte:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando a tarefa retornada pelo método [PairNamespaceAsync][] for concluído, tudo está configurado e pronto para uso. Antes que a tarefa é retornada, você pode não ter concluído todo o trabalho de plano de fundo necessário para o emparelhamento para trabalhar à direita. Como resultado, você não deve começar enviando mensagens até que a tarefa retorna. Se alguma falha ocorrer, como credenciais incorretas ou Falha ao criar filas de acúmulo, essas exceções serão lançadas assim que a tarefa for concluída. Depois que a tarefa retorna, verificar se as filas foram encontradas ou criadas examinando a propriedade [BacklogQueueCount][] em sua instância de [SendAvailabilityPairedNamespaceOptions][] . Para o código anterior, a operação aparece da seguinte maneira:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de mensagens assíncronas no carregador de serviço, leia mais detalhes sobre [namespaces pares][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System. TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Práticas recomendadas para Protegendo aplicativos contra interrupções de barramento de serviço e desastres]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [Gerenciador de namespace]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [namespaces pares]: service-bus-paired-namespaces.md