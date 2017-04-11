<properties 
    pageTitle="Particionado tópicos e filas | Microsoft Azure"
    description="Descreve como partição tópicos e filas do barramento de serviço, usando vários agentes de mensagens."
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Tópicos e filas particionadas

Barramento de serviço do Azure utiliza vários agentes de mensagens para processar mensagens e vários armazenamentos de mensagens para armazenar as mensagens. Uma fila convencional ou tópico é tratado por um agente de única mensagem e armazenado em um repositório de mensagens. Barramento de serviço também permite filas ou tópicos a serem particionados em vários agentes de mensagens e armazena mensagens. Isso significa que a produtividade geral de uma fila particionada ou tópico já não é limitada pelo desempenho de um agente de única mensagem ou o armazenamento de mensagens. Além disso, uma interrupção temporária de um armazenamento de mensagens não processa uma fila particionada ou tópico indisponível. Tópicos e filas particionadas podem conter todos os barramento de serviço recursos avançados, como o suporte para as transações e sessões.

Para obter mais detalhes sobre elementos internos de barramento de serviço, consulte o tópico de [arquitetura de barramento de serviço][] .

## <a name="how-it-works"></a>Como ele funciona

Cada fila particionada ou tópico consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferentes e manipulado por um agente de mensagem diferente. Quando uma mensagem é enviada para uma fila particionada ou tópico, barramento de serviço atribui a mensagem para um dos fragmentos. A seleção é feita aleatoriamente barramento de serviço ou usando uma chave de partição que o remetente pode especificar.

Quando um cliente quiser receber uma mensagem de uma fila particionada ou de uma assinatura de um tópico particionada, consultas de barramento de serviço fragmentos de todas as mensagens, em seguida, retorna a primeira mensagem obtidos de qualquer um dos armazenamentos de mensagens para o destinatário. Caches de barramento de serviço as outras mensagens e retorna-los quando ele recebe adicionais recebem solicitações. Um cliente de recebimento não está ciente de que as partições; o comportamento de voltados para o cliente de uma fila particionada ou tópico (por exemplo, ler, conclua, adiar, mensagens mortas, pré-busca) é idêntico ao comportamento de uma entidade regular.

Não há nenhum custo adicional ao enviar uma mensagem para ou receber uma mensagem de uma fila particionada ou tópico.

## <a name="enable-partitioning"></a>Habilitar partição

Para usar tópicos e filas particionadas com barramento de serviço do Azure, use o SDK do Azure versão 2.2 ou posterior ou especifique `api-version=2013-10` no seu HTTP solicita.

Você pode criar tópicos e filas do barramento de serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com partição habilitado, barramento de serviço cria 16 partições para cada GB que você especificar. Assim, se você criar uma fila que é 5 GB de tamanho, com 16 partições o tamanho máximo da fila se torna (5 \* 16) = 80 GB. Você pode ver o tamanho máximo da fila particionada ou tópico examinando sua entrada no [portal do Azure][].

Há várias maneiras de criar uma fila particionada ou tópico. Quando você cria a fila ou tópico de seu aplicativo, você pode habilitar a partição da fila ou tópico, respectivamente definindo a propriedade [QueueDescription.EnablePartitioning][] ou [TopicDescription.EnablePartitioning][] para **true**. Essas propriedades devem ser definidas no momento fila ou tópico é criado. Não é possível alterar essas propriedades em um fila existente ou tópico. Por exemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, você pode criar uma fila particionada ou tópico no Visual Studio ou no [portal do Azure][]. Quando você cria uma nova fila ou tópico no portal do, defina a opção **Habilitar partição** na lâmina **Configurações gerais** da fila ou tópico janela **configurações** como **true**. No Visual Studio, clique na caixa de seleção **Habilitar partição** na caixa de diálogo **Nova fila** ou **Novo tópico** .

## <a name="use-of-partition-keys"></a>Uso de chaves de partição

Quando uma mensagem for enfileirados para uma fila particionada ou de tópico, barramento de serviço verifica a presença de uma chave de partição. Se encontrar, ela seleciona o fragmento com base na chave. Se não encontrar uma chave de partição, seleciona o fragmento com base em um algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição

Alguns cenários, como sessões ou transações, exigem mensagens sejam armazenadas em um fragmento específico. Todos esses cenários requerem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas ao mesmo fragmento. Se o fragmento está temporariamente indisponível, barramento de serviço retornará um erro.

Dependendo do cenário, propriedades de mensagem diferente são usadas como uma chave de partição:

**Identificação de sessão**: se uma mensagem tiver a propriedade [BrokeredMessage.SessionId][] definida, barramento de serviço usa esta propriedade como a chave da partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são tratadas pelo mesmo agente mensagem. Isso permite barramento de serviço garantir a ordenação bem como a consistência dos Estados de sessão de mensagem.

**PartitionKey**: se uma mensagem com a propriedade [BrokeredMessage.PartitionKey][] , mas não a propriedade de [BrokeredMessage.SessionId][] definida, barramento de serviço utiliza a propriedade [PartitionKey][] como a chave de partição. Se a mensagem tiver a [identificação de sessão][] e o conjunto de propriedades de [PartitionKey][] , ambas as propriedades devem ser idênticas. Se a propriedade [PartitionKey][] estiver definida para um valor diferente da propriedade de [identificação de sessão][] , barramento de serviço retornará uma exceção **InvalidOperationException** . A propriedade [PartitionKey][] deve ser usada se um remetente envia mensagens de transações cientes não sessão. A chave da partição garante que todas as mensagens que são enviadas dentro de uma transação são tratadas pelo mesmo agente mensagens.

**MessageId**: se a fila ou tópico tem a propriedade de [QueueDescription.RequiresDuplicateDetection][] definida como **true** e as propriedades [BrokeredMessage.SessionId][] ou [BrokeredMessage.PartitionKey][] não estiverem definidas, a propriedade [BrokeredMessage.MessageId][] serve como a chave da partição. (Observe que as bibliotecas Microsoft .NET e AMQP atribuir automaticamente um ID de mensagem se o aplicativo de envio não). Nesse caso, todas as cópias da mesma mensagem são tratadas pelo mesmo agente mensagem. Isso permite barramento de serviço detectar e eliminar mensagens duplicadas. Se a propriedade [QueueDescription.RequiresDuplicateDetection][] não está definida como **true**, barramento de serviço não considera a propriedade [MessageId][] como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usando uma chave de partição

Na ausência de uma chave de partição, barramento de serviço distribui mensagens de maneira alternada para todos os fragmentos do particionada fila ou tópico. Se o fragmento escolhido não estiver disponível, barramento de serviço atribui à mensagem um fragmento diferente. Dessa forma, a operação de envio for bem-sucedida apesar indisponibilidade temporária de um armazenamento de mensagens.

Para dar barramento de serviço bastante tempo em fila a mensagem em um fragmento diferente, o valor de [MessagingFactorySettings.OperationTimeout][] especificado pelo cliente que envia a mensagem deve ser maior que 15 segundos. É recomendável que você defina a propriedade [OperationTimeout][] para o valor padrão de 60 segundos.

Observe que uma chave de partição "fixa" uma mensagem para um fragmento específico. Se o armazenamento de mensagens que detém esse fragmento não estiver disponível, o barramento de serviço retornará um erro. Na ausência de uma chave de partição, barramento de serviço pode escolher um fragmento diferente e a operação de sucesso. Portanto, é recomendável que você não forneça uma chave de partição, a menos que ele é necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: usar transações com entidades particionadas

Mensagens que são enviadas como parte de uma transação devem especificar uma chave de partição. Isso pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]ou [BrokeredMessage.MessageId][]. Todas as mensagens que são enviadas como parte da mesma transação, especifique a mesma chave de partição. Se você tentar enviar uma mensagem sem uma chave de partição dentro de uma transação, barramento de serviço retorna uma exceção **InvalidOperationException** . Se você tentar enviar várias mensagens dentro da mesma transação com chaves de partição diferentes, barramento de serviço retorna uma exceção **InvalidOperationException** . Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se qualquer uma das propriedades que servir como uma chave de partição são definidas, barramento de serviço fixa a mensagem para um fragmento específico. Esse comportamento ocorre se uma transação é usada ou não. É recomendável que você não especificar uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Usando sessões com entidades particionadas

Para enviar uma mensagem de transações para um tópico reconhecimento de sessão ou fila, a mensagem deve ter a propriedade [BrokeredMessage.SessionId][] definida. Se a propriedade [BrokeredMessage.PartitionKey][] for especificada também, ele deve ser idêntico para a propriedade de [identificação de sessão][] . Se forem diferentes, o barramento de serviço retorna uma exceção **InvalidOperationException** .

Ao contrário filas regulares (não-particionada) ou tópicos, não é possível usar uma única transação para enviar várias mensagens para sessões diferentes. Se uma tentativa, barramento de serviço retornará uma exceção **InvalidOperationException **. Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Encaminhamento de mensagens automáticas com entidades particionados

Barramento de serviço do Azure suporta encaminhamento de mensagens automáticas de, para ou entre entidades particionadas. Para habilitar o encaminhamento automático de mensagens, defina a propriedade [QueueDescription.ForwardTo][] na fila de origem ou assinatura. Se a mensagem especifica uma chave de partição ([ID de sessão][], [PartitionKey][]ou [MessageId][]), chave da partição será usado para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes

- **Recursos de consistência alta**: se uma entidade usa recursos como sessões, detecção de duplicidades ou controle explícito de partição chave, então as operações de mensagens sempre são roteadas para fragmentos específicos. Se qualquer um dos fragmentos experiência tráfego de alto ou o armazenamento subjacente está com problemas, essas operações falhar e disponibilidade é reduzida. Em geral, a consistência é ainda muito maior do que entidades não particionado; apenas um subconjunto de tráfego está enfrentando problemas, em vez de todo o tráfego.
- **Gerenciamento**: operações como criar, atualizar e excluir devem ser executadas em todos os fragmentos da entidade. Se fragmentos for não íntegro ele pode resultar em falhas para essas operações. Para a operação Get, informações como contagens de mensagem devem ser agregadas de todos os fragmentos. Se não íntegro fragmentos, o status de disponibilidade de entidade é relatado como limitado.
- **Cenários de mensagem de volume baixo**: tais cenários, especialmente quando usando o protocolo HTTP, você talvez precise executar várias operações de recebimento para obter todas as mensagens. Para receber solicitações, o front-end executa um recebimento em todos os fragmentos e armazena todas as respostas recebidas. Uma solicitação de recebimento subsequentes na mesma conexão seria beneficiar este cache e receber latências será menores. No entanto, se você tiver várias conexões ou usa HTTP, que estabelece uma nova conexão para cada solicitação. Assim, não há nenhuma garantia que ele seria será levado no mesmo nó. Se todas as mensagens existentes são bloqueadas e armazenados em cache no outro front-end, a operação de recebimento retorna **Nulo**. Mensagens eventualmente expirarem e você pode recebê-las novamente. É recomendável HTTP manutenção de funcionamento.
- **Procurar/espiada mensagens**: PeekBatch nem sempre retorna o número de mensagens especificado na [propriedade MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Há dois motivos comuns para isso. Um motivo é que o tamanho agregado da coleção de mensagens excede o tamanho máximo de 256KB. Outro motivo é que, se a fila ou tópico tem a [propriedade de EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) definida como **true**, uma partição pode não ter suficiente mensagens para concluir o número de mensagens solicitado. Em geral, se um aplicativo quiser receber um número específico de mensagens, ele deve chamar [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) repetidamente até que ele obtém o número de mensagens, ou não existem mais mensagens para inspecionar. Para obter mais informações, incluindo exemplos de código, consulte [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) ou [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Mais recentes recursos adicionados

- Adicionar ou remover regra agora é compatível com entidades particionadas. Diferente de entidades não particionada, essas operações não são suportadas em transações. 
- AMQP agora é suportado para enviar e receber mensagens para e de uma entidade particionada.
- AMQP agora tem suporte para as seguintes operações: [Lote enviar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Lote receber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [recebimento pelo número de sequência](https://msdn.microsoft.com/library/azure/hh330765.aspx), [inspecionar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Renovar Lock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Mensagem de cronograma](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Cancelar mensagem agendada](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Adicionar regra](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Remover regra](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Bloqueio de renovação de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Definir o estado de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Estado de sessão de Get](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Inspecionar mensagens da sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)e [Enumerar sessões](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitações de entidades particionado

Atualmente o barramento de serviço impõe as seguintes limitações sobre filas particionadas e tópicos:

-   Tópicos e filas particionadas não têm suporte para envio de mensagens que fazem parte de sessões diferentes em uma única transação.
-   Barramento de serviço atualmente permite até 100 filas particionadas ou tópicos por namespace. Cada tópico ou fila particionada conta em direção a cota de 10.000 entidades por namespace (não se aplica ao nível Premium).

## <a name="next-steps"></a>Próximas etapas

Consulte a discussão de [suporte de AMQP 1.0 para barramento de serviço particionado tópicos e filas][] para saber mais sobre partição entidades mensagens. 

  [Arquitetura de barramento de serviço]: service-bus-architecture.md
  [Portal do Azure]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [Identificação de sessão]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Suporte a AMQP 1.0 tópicos e filas do barramento de serviço particionado]: service-bus-partitioned-queues-and-topics-amqp-overview.md
