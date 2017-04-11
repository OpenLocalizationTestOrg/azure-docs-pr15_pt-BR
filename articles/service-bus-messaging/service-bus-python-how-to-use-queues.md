<properties 
    pageTitle="Como usar filas de barramento de serviço com Python | Microsoft Azure" 
    description="Saiba como usar filas de barramento de serviço do Azure do Python." 
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
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Como usar filas de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como usar filas de barramento de serviço. Os exemplos são gravados em Python e usam o [pacote de barramento de serviço do Azure Python][]. Os cenários cobertos incluem **Criando filas, enviar e receber mensagens**e **excluindo filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Para instalar Python ou o [pacote de barramento de serviço do Azure Python][], consulte o [Guia de instalação do Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Criar uma fila

O objeto **ServiceBusService** permite que você trabalhe com filas. Adicione o seguinte código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente barramento de serviço:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

O código a seguir cria um objeto **ServiceBusService** . Substituir `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com namespace, nome de chave de assinatura (SAS) de acesso compartilhado e valor.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Os valores para o nome da chave SAS e valor podem ser encontrados nas informações de conexão do [Azure portal clássico][] ou no painel de **Propriedades** do Visual Studio ao selecionar namespace barramento de serviço no Server Explorer (conforme mostrado na seção anterior).

```
bus_service.create_queue('taskqueue')
```

**create_queue** também suporta opções adicionais, que permitem substituir as configurações de fila padrão como o tempo de vida (TTL) mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila 5GB e o valor TTL para 1 minuto:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de barramento de serviço, seu aplicativo chama o **Enviar\_fila\_mensagem** método no objeto **ServiceBusService** .

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila nomeada *taskqueue usando* **Enviar\_fila\_mensagem**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Filas de barramento de serviço oferecem suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens mantido em uma fila, mas há um limite no tamanho total das mensagens apresentadas por uma fila. Este tamanho de fila é definido em tempo de criação, com um limite de 5 GB. Para obter mais informações sobre cotas, consulte [cotas de barramento de serviço][].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

As mensagens são recebidas de um fila usando o **receber\_fila\_mensagem** método no objeto **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

As mensagens são excluídas da fila conforme eles são lidos quando o parâmetro **espiada\_lock** estiver definida como **False**. Você pode ler (espiada) e bloquear a mensagem sem excluí-lo da fila definindo o parâmetro **espiada\_lock** como **True**.

O comportamento de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

Se o **espiada\_lock** parâmetro for definido como **True**, o recebimento torna-se uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método **delete** no objeto de **mensagem** . O método **delete** será marcar a mensagem como sendo consumida e removê-lo da fila.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **Desbloquear** no objeto de **mensagem** . Isso fará com que o barramento de serviço desbloquear a mensagem dentro da fila e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes do método **Excluir** é chamado, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é obtido usando a propriedade **MessageId** da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de filas de barramento de serviço, siga estes links para saber mais.

-   Consulte [filas, tópicos e assinaturas][].

[Azure portal clássico]: https://manage.windowsazure.com
[Pacote de barramento de serviço do Python Azure]: https://pypi.python.org/pypi/azure-servicebus  
[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[Cotas de barramento de serviço]: service-bus-quotas.md
 
