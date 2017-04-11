<properties 
    pageTitle="Como usar tópicos de barramento de serviço com Python | Microsoft Azure" 
    description="Saiba como usar tópicos de barramento de serviço do Azure e assinaturas do Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar assinaturas e tópicos de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar assinaturas e tópicos de barramento de serviço. Os exemplos são gravados em Python e usam o [pacote de Python Azure][]. Os cenários cobertos incluem **Criando tópicos e assinaturas**, **Criando filtros de assinatura**, **enviando mensagens para um tópico**, **receber mensagens de uma assinatura**e **Excluindo tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Observação:** Se você precisar instalar Python ou o [pacote de Python Azure][], consulte o [Guia de instalação do Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o seguinte na parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente barramento de serviço:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código a seguir cria um objeto **ServiceBusService** . Substituir `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com seu namespace real, assinatura de acesso compartilhado (SAS) chave valor de nome e chave.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Você pode obter os valores para o nome da chave SAS e valor a partir do [portal do Azure][].

```
bus_service.create_topic('mytopic')
```

**criar\_tópico** também suporta opções adicionais, que permitem substituir configurações de tópico padrão como o tempo de live mensagem ou o tamanho máximo de tópicos. O exemplo a seguir define o tamanho máximo de tópico 5 GB e uma hora Live valor (TTL) de um minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

Assinaturas para tópicos também são criadas com o objeto **ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da assinatura.

> [AZURE.NOTE] Assinaturas são persistentes e continuarão a existir até eles tanto o tópico ao qual está inscrito, serão excluídas.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

O filtro de **MatchAll** é o padrão que é usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro de **MatchAll** é usado, publicadas para o tópico de todas as mensagens são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma inscrição chamada 'AllMessages' e usa o filtro de **MatchAll** padrão.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode definir filtros que permitem especificar quais mensagens enviadas para um tópico deverão ser mostrada dentro de uma assinatura de tópico específico.

O tipo mais flexível de filtro compatível com assinaturas é um **SqlFilter**, que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre as expressões que podem ser usadas com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression][] .

Você pode adicionar filtros para uma assinatura usando o **criar\_regra** método do objeto **ServiceBusService** . Este método permite que você adicione novos filtros uma assinatura existente.

> [AZURE.NOTE] Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, você deve primeiro remover o filtro padrão ou o **MatchAll** substituirá quaisquer outros filtros que você pode especificar. Você pode remover a regra padrão usando o **Excluir\_regra** método do objeto **ServiceBusService** .

O exemplo a seguir cria uma inscrição chamada `HighMessages` com um **SqlFilter** que seleciona somente as mensagens que têm uma propriedade personalizada **messagenumber** maior que 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Da mesma forma, o exemplo a seguir cria uma inscrição chamada `LowMessages` com um **SqlFilter** que seleciona somente as mensagens que têm uma **messagenumber** propriedade menor ou igual a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando uma mensagem é enviada para `mytopic` sempre é fornecida para receptores inscrito para a assinatura de tópico **AllMessages** e entregue seletivamente para receptores assinados as assinaturas de tópico **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, o aplicativo deve usar o **Enviar\_tópico\_mensagem** método do objeto **ServiceBusService** .

O exemplo a seguir demonstra como enviar cinco testar as mensagens `mytopic`. Observe que o valor da propriedade **messagenumber** de cada mensagem varia a iteração do loop (Isso determina quais assinaturas recebam-lo):

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Tamanho este tópico é definido em tempo de criação, com um limite de 5 GB. Para obter mais informações sobre cotas, consulte [cotas de barramento de serviço][].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o **receber\_assinatura\_mensagem** método no objeto **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são excluídas da assinatura, como eles são lidos quando o parâmetro **espiada\_lock** estiver definida como **False**. Você pode ler (espiada) e bloquear a mensagem sem excluí-lo da fila definindo o parâmetro **espiada\_lock** como **True**.

O comportamento de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

Se o **espiada\_lock** parâmetro for definido como **True**, o recebimento torna-se uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Após o aplicativo termina de processar a mensagem (ou armazena confiavelmente para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando método **delete** no objeto de **mensagem** . O método **delete** marca a mensagem como sendo consumida e remove-o da assinatura.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **Desbloquear** no objeto de **mensagem** . Isso fará com que o barramento de serviço desbloquear a mensagem na assinatura e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo falhar), e em seguida, serviço desbloqueie a mensagem automaticamente e disponibiliza a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes do método **Excluir** é chamado, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é obtido usando a propriedade **MessageId** da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Tópicos assinaturas são persistentes e devem ser explicitamente excluídas por meio do [portal do Azure][] ou programaticamente. O exemplo a seguir mostra como excluir o tópico chamado `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Excluir um tópico também exclui qualquer assinaturas que são registradas com o tópico. Assinaturas também podem ser excluídas independentemente. O código a seguir mostra como excluir uma inscrição chamada `HighMessages` do `mytopic` tópico:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de tópicos do barramento de serviço, siga estes links para saber mais.

-   Consulte [filas, tópicos e assinaturas][].
-   Referência do [SqlFilter.SqlExpression][].

[Portal do Azure]: https://portal.azure.com
[Pacote de Python Azure]: https://pypi.python.org/pypi/azure  
[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Cotas de barramento de serviço]: service-bus-quotas.md 
