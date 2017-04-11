<properties 
    pageTitle="Barramento de serviço par namespaces | Microsoft Azure"
    description="Detalhes de implementação de namespace pares e custo"
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

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Detalhes de implementação de namespace em par e implicações de custo

O método de [PairNamespaceAsync][] , usando uma instância de [SendAvailabilityPairedNamespaceOptions][] , executa tarefas visíveis em seu nome. Porque há considerações de custo ao usar o recurso, é útil entender essas tarefas para que você espera o comportamento quando ele acontece. A API relaciona o seguinte comportamento automático em seu nome:

-   Criação de filas de registro posterior.
-   Criação de um objeto de [MessageSender][] que se comunica com filas ou tópicos.
-   Quando uma entidade mensagens fica indisponível, o envia ping mensagens à entidade em uma tentativa de detectar quando essa entidade fica disponível novamente.
-   Opcionalmente, cria um conjunto de "bombas de mensagem" que mover mensagens de filas de acúmulo para as filas principais.
-   Coordenadas fechamento/falha das instâncias [MessagingFactory][] primárias e secundárias.

Em um alto nível, o recurso funciona da seguinte maneira: quando a entidade primária é íntegra, nenhuma alteração de comportamento ocorre. Quando a duração de [FailoverInterval][] ter decorrido e entidade principal vê bem-sucedido não envia após um não transitório [MessagingException][] ou uma [TimeoutException][], ocorre o seguinte comportamento:

1.  Envie operações à entidade principal são desabilitadas e o sistema ping entidade principal até que o ping pode ser entregues com êxito.

2.  Uma fila de registro posterior aleatório está selecionada.

3.  Objetos [BrokeredMessage][] são roteados para fila de registro posterior escolhido.

1.  Se uma operação de envio para fila de registro posterior escolhido falhar, será puxada fila da rotação e uma nova fila está selecionada. Saiba todos os remetentes na instância [MessagingFactory][] da falha.

As figuras a seguir descrevem a sequência. Primeiro, o remetente envia mensagens.

![Namespaces pares][0]

Após a falha de enviar para fila principal, o remetente começa enviando mensagens para uma fila de registro posterior escolhido aleatoriamente. Simultaneamente, ele inicia uma tarefa de ping.

![Namespaces pares][1]

Neste ponto, as mensagens ainda estão na fila secundária e ainda não foram entregues à fila principal. Depois que a fila principal estiver eficaz, novamente, pelo menos um processo deve estar executando o sifão. O sifão oferece as mensagens de todas as diversas filas de acúmulo para as entidades de destino adequado (filas e tópicos).

![Namespaces pares][2]

O restante deste tópico aborda os detalhes específicos de como funcionam essas partes.

## <a name="creation-of-backlog-queues"></a>Criação de filas de acúmulo

O objeto [SendAvailabilityPairedNamespaceOptions][] passado para o método de [PairNamespaceAsync][] indica o número de filas de registro acumulado que você deseja usar. Cada fila de registro posterior, em seguida, é criada com as seguintes propriedades explicitamente conjunto (todos os outros valores são definidos para os padrões de [QueueDescription][] ):

| Caminho                                   | [namespace primária] / x-servicebus-transferência / [índice], onde [índice] é um valor em [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan                                                                                    |
| LockDuration                           | 1 minuto                                                                                             |
| EnableDeadLetteringOnMessageExpiration | verdadeiro                                                                                                 |
| EnableBatchedOperations                | verdadeiro                                                                                                 |

Por exemplo, a primeira fila de registro posterior criado para namespace **contoso** é nomeado `contoso/x-servicebus-transfer/0`.

Ao criar filas, o código primeiro verifica para ver se existe uma fila. Se a fila não existir, a fila é criada. O código não limpar filas de acúmulo "extra". Especificamente, se o aplicativo com o namespace primária **contoso** solicitar cinco registro posterior filas mas uma fila de registro posterior com o caminho `contoso/x-servicebus-transfer/7` existir, essa fila de registro posterior extra ainda está presente, mas não é usada. O sistema permite explicitamente filas de acúmulo extra existir que não deve ser usadas. Como o proprietário de namespace, você é responsável pelo limpando qualquer filas de registro não utilizadas/indesejados posterior. O motivo para essa decisão é que barramento de serviço não é possível saber quais fins existem para todas as filas no seu namespace. Além disso, se uma fila existe com o nome fornecido, mas não atende a assumida [QueueDescription][], em seguida, seu motivos são seus próprios para alterar o comportamento padrão. Não há garantias são feitas para modificações às filas acúmulo pelo seu código. Certifique-se de teste totalmente suas alterações.

## <a name="custom-messagesender"></a>MessageSender personalizado

Ao enviar, todas as mensagens vão por meio de um objeto de [MessageSender][] interno que normalmente se comporta quando tudo funciona e redireciona para as filas de acúmulo quando coisas "quebra". Ao receber uma falha de não transitório, um timer é iniciado. Após um período de [período de tempo][] que consiste o valor da propriedade [FailoverInterval][] durante o qual nenhum bem-sucedida são enviadas, o failover é envolvido. Neste ponto, acontece o seguinte para cada entidade:

- Uma tarefa de ping executa cada [PingPrimaryInterval][] para verificar se a entidade está disponível. Quando essa tarefa é bem sucedida, todo o código do cliente que usa a entidade imediatamente começa a enviar novas mensagens ao namespace principal.

- Solicitações futuras para enviar para que a mesma entidade de quaisquer outros remetentes resultará na [BrokeredMessage][] está sendo enviado a ser modificada para ficar em fila de registro posterior. A modificação remove algumas propriedades do objeto [BrokeredMessage][] e armazena-los em outro lugar. As seguintes propriedades são desmarcadas e adicionadas em um novo alias, permitindo que o barramento de serviço e o SDK processar mensagens uniformemente:

| Nome de propriedade antigo       | Novo nome de propriedade |
|-------------------------|-------------------|
| Identificação de sessão               | x-ms-identificação de sessão    |
| TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | x-ms-caminho         |

O caminho de destino original também é armazenado dentro da mensagem como uma propriedade chamada x-ms-path. Este design permite mensagens para muitas entidades a coexistência em uma fila de registro único posterior. As propriedades são convertidas volta pelo sifão.

O objeto [MessageSender][] personalizado pode encontrar problemas ao mensagens abordagem o limite de 256 KB e failover está ativada. O objeto [MessageSender][] personalizado armazena mensagens para todos os tópicos e filas juntos nas filas de registro posterior. Esse objeto combina mensagens de muitos primárias juntos dentro das filas de registro posterior. Para tratar de balanceamento entre muitos clientes que não souber uns aos outros, o SDK seleciona aleatoriamente uma fila de registro posterior para cada [QueueClient][] ou [TopicClient][] você criar no código.

## <a name="pings"></a>Ping

Uma mensagem de ping é um vazio [BrokeredMessage][] com sua propriedade [ContentType][] definida para aplicativo/vnd.ms-servicebus-ping e um valor de [TimeToLive][] de 1 segundo. Este ping tem uma característica especial no carregador de serviço: servidor nunca oferece um ping quando qualquer chamador solicita um [BrokeredMessage][]. Portanto, você nunca tenha que aprender a receber e ignorar essas mensagens. Cada entidade (fila exclusiva ou tópico) por instância de [MessagingFactory][] por cliente será ser feito o ping quando elas são consideradas indisponível. Por padrão, isso acontece uma vez por minuto. Mensagens de ping são consideradas regulares mensagens de barramento de serviço e podem resultar em encargos para mensagens e largura de banda. Assim que os clientes detectam que o sistema estiver disponível, as mensagens parar.

## <a name="the-syphon"></a>O sifão

Pelo menos um programa executável no aplicativo a deve estar executando ativamente o sifão. O sifão executa um longo votação receber que dura 15 minutos. Quando todas as entidades estão disponíveis e você tiver 10 filas de acúmulo, o aplicativo que hospeda a sifão chama a operação de recebimento 40 vezes por hora, 960 horas por dia e tempos de 28800 em 30 dias. Quando o sifão ativamente é mover as mensagens do registro posterior à fila principal, cada mensagem experiências os encargos seguintes (padrão para o tamanho da mensagem e a largura de banda serão cobrados encargos em todos os estágios):

1.  Envie para o registro acumulado.

2.  Receba do registro acumulado.

3.  Envie para o principal.

4.  Receba do principal.

## <a name="closefault-behavior"></a>Fechar/falhas comportamento

Em um aplicativo que hospeda a sifão, uma vez as falhas de [MessagingFactory][] primárias ou secundárias ou for fechado sem seu parceiro também está sendo com defeito/fechada e o sifão detecta nesse estado, o Age sifão. Se a outras [MessagingFactory][] não estiver fechado em 5 segundos, o sifão será falhas a ainda aberta [MessagingFactory][].

## <a name="next-steps"></a>Próximas etapas

Consulte [padrões de mensagens assíncronas e alta disponibilidade][] para uma discussão detalhada de mensagens assíncronas do barramento de serviço. 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [Período de tempo]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Padrões de mensagens assíncronas e alta disponibilidade]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
