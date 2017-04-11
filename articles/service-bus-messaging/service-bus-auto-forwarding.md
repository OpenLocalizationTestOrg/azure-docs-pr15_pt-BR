<properties 
    pageTitle="Encaminhamento automático barramento de serviço de mensagens entidades | Microsoft Azure"
    description="Como a cadeia de uma assinatura para outra fila ou tópico ou fila."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Encadeamento entidades de barramento de serviço com encaminhamento automático

O recurso de *encaminhamento automático* permite a cadeia de uma assinatura para outra fila ou tópico que faz parte do mesmo namespace ou fila. Quando o encaminhamento automático está habilitado, barramento de serviço automaticamente remove as mensagens que são colocadas na primeira fila ou assinatura (origem) e coloca-os na segunda fila ou tópico (destino). Observe que ainda é possível enviar uma mensagem para a entidade de destino diretamente. Além disso, não é possível cadeia uma subfila, como uma fila de mensagens mortas, para outra fila ou tópico.

## <a name="using-auto-forwarding"></a>Usando o encaminhamento automático

Você pode habilitar encaminhamento automático definindo as propriedades de [SubscriptionDescription.ForwardTo][] ou [QueueDescription.ForwardTo][] nos objetos [QueueDescription][] ou [SubscriptionDescription][] para a fonte, como no exemplo a seguir.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino deve existir no momento que a entidade de origem é criada. Se a entidade de destino não existir, barramento de serviço retornará uma exceção quando for solicitado a criar a entidade de origem.

Você pode usar o encaminhamento automático para dimensionar um tópico individual. Barramento de serviço limita o [número de inscrições sobre um determinado tópico](service-bus-quotas.md) a 2.000. Você pode acomodar assinaturas adicionais, criando tópicos de segundo nível. Observe que mesmo se você não está vinculados pela limitação de barramento de serviço no número de assinaturas, adicionar um segundo nível de tópicos pode melhorar a produtividade geral do seu tópico.

![Cenário de encaminhamento automático][0]

Você também pode usar o encaminhamento automático desassociar remetentes de mensagens de receptores. Por exemplo, considere um sistema de ERP que consiste em três módulos: processamento de pedidos, gerenciamento de estoque e gerenciamento de relações de cliente. Cada um desses módulos gera mensagens que são colocados em um tópico correspondente. Alice e Paulo é representantes de vendas que estão interessados em todas as mensagens relacionadas a seus clientes. Para receber essas mensagens, Alice e Paulo cria uma fila pessoal e uma assinatura em cada um dos tópicos ERP que encaminham automaticamente todas as mensagens para sua fila.

![Cenário de encaminhamento automático][1]

Se Alice prossegue férias, sua fila pessoal, em vez do tópico ERP, preenche para cima. Nesse cenário, porque um representante de vendas não recebeu as mensagens, nenhum dos tópicos ERP nunca alcançar cota.

## <a name="auto-forwarding-considerations"></a>Considerações de encaminhamento automático

Se a entidade de destino acumulada muitas mensagens e exceda a cota ou a entidade de destino é desabilitada, a entidade de origem adiciona as mensagens para a sua [fila de inatividade](service-bus-dead-letter-queues.md) até que há espaço no destino (ou a entidade está habilitada novamente). As mensagens continuará live na fila de inatividade, portanto você explicitamente deve receber e processá-los da fila de inatividade.

Quando encadeamento juntas tópicos individuais para obter um tópico composto com muitas assinaturas, é recomendável que você tenha um número moderado de assinaturas sobre o tópico de primeiro nível e muitas assinaturas sobre os tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 assinaturas, cada um deles encadeadas para um tópico de segundo nível com 200 assinaturas, permite produtividade superior de um tópico de primeiro nível com 200 assinaturas, cada encadeadas para um tópico de segundo nível com 20 assinaturas.

Barramento de serviço cobra uma operação de cada mensagem encaminhada. Por exemplo, enviar uma mensagem para um tópico com 20 assinaturas, cada um deles configurado para encaminhar automaticamente mensagens para outra fila ou tópico, será cobrado como 21 operações se todas as assinaturas de primeiro nível recebem uma cópia da mensagem.

Para criar uma assinatura que esteja encadeada para outra fila ou tópico, o criador da assinatura deve ter **Gerenciar** permissões na origem e a entidade de destino. Somente enviar mensagens para o tópico de origem requer permissões **Enviar** sobre o tópico de origem.

## <a name="next-steps"></a>Próximas etapas

Para obter informações detalhadas sobre o encaminhamento automático, consulte os seguintes tópicos de referência:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Para saber mais sobre as melhorias de desempenho de barramento de serviço, consulte [Partitioned mensagens entidades][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Particionado entidades de mensagens]: service-bus-partitioning.md