<properties
    pageTitle="Como usar o armazenamento de fila do Python | Microsoft Azure"
    description="Saiba como usar o serviço de fila do Azure do Python para criar e excluir filas e inserir, obter e excluir mensagens."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Como usar o armazenamento de fila do Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de fila do Azure. Os exemplos são gravados em Python e usam o [Microsoft Azure armazenamento SDK para Python]. Os cenários cobertos incluem **inserindo**, **inspecionar**, **Obtendo**e **Excluindo** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [próximas etapas].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService** . Adicione o seguinte na parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente o armazenamento do Azure:

    from azure.storage.queue import QueueService

O código a seguir cria um objeto de **QueueService** usando a chave de nome e uma conta de conta do armazenamento. Substitua 'Minha conta' e 'mykey' com seu nome de conta e chave.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o **colocar\_mensagem** método para criar uma nova mensagem e adicioná-la à fila.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Como: Inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando a **espiada\_mensagens** método. Por padrão, **espiada\_mensagens** exibe em uma única mensagem.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Como: Retirar mensagens

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar **obter\_mensagens**, você receber a próxima mensagem na fila por padrão. Uma mensagem retornada de **obter\_mensagens** fica invisível para qualquer outro código ler mensagens dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção a mensagem da fila, você também deve chamar **Excluir\_mensagem**. Esse processo de duas etapas de remoção de uma mensagem garante que, quando seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. As chamadas de código **Excluir\_mensagem** logo depois que a mensagem foi processada.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte exemplo de código usa o **obter\_mensagens** método para receber mensagens de 16 em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite invisibility para cinco minutos para cada mensagem.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representa uma tarefa de trabalho, você pode usar esse recurso para atualizar o status da tarefa. O código abaixo usa o **atualizar\_mensagem** método para atualizar uma mensagem. O tempo limite de visibilidade está definido como 0, indicando que a mensagem será exibida imediatamente e o conteúdo é atualizado.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O **obter\_fila\_metadados** método solicita o serviço de fila para retornar metadados sobre a fila e a **approximate_message_count**. O resultado é aproximado só porque mensagens podem ser adicionadas ou removidas após o serviço de fila responde à sua solicitação.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, ligue o **Excluir\_fila** método.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de fila, siga estes links para saber mais.

- [Central de desenvolvedores do Python](/develop/python/)
- [Serviços de armazenamento do Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de armazenamento do Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
