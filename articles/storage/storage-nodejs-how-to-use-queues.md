<properties
    pageTitle="Como usar o armazenamento de fila do Node | Microsoft Azure"
    description="Aprenda a usar o serviço de fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Amostras escritas em Node."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Como usar o armazenamento de fila do Node

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de fila do Microsoft Azure. Os exemplos são gravados usando a API Node. Os cenários cobertos incluem **inserindo**, **inspecionar**, **Obtendo**e **Excluindo** mensagens da fila, bem como **criar e excluir filas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo de Node

Crie um aplicativo Node em branco. Para obter instruções sobre como criar um aplicativo de Node, consulte [criar um aplicativo da web Node no serviço de aplicativo do Azure], [criar e implantar um aplicativo Node para um serviço de nuvem do Azure] usando o Windows PowerShell ou [Construir e implantar um aplicativo de web Node no Azure usando Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, é necessário o SDK de armazenamento do Azure para Node. js, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gerenciador de pacote de nó (NPM) para obter o pacote

1.  Usar uma interface de linha como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), navegue até a pasta onde você criou seu aplicativo de amostra.

2.  Na janela de comando, digite **npm instalar o armazenamento do azure** . Saída do comando é semelhante ao seguinte exemplo.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Você pode executar manualmente o comando **ls** para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, você encontrará o pacote de **armazenamento do azure** , que contém as bibliotecas que você precisa para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Configurar uma Conexão de armazenamento do Azure

O módulo azure lerá as variáveis de ambiente AZURE\_armazenamento\_conta e AZURE\_armazenamento\_acesso\_chave ou AZURE\_armazenamento\_conexão\_cadeia de caracteres para informações necessárias para conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, especifique as informações da conta ao chamar **createQueueService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um site do Azure, consulte [Node web app usando o serviço de tabela do Azure].

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O código a seguir cria um objeto **QueueService** , que permite que você trabalhe com filas.

    var queueSvc = azure.createQueueService();

Use o método **createQueueIfNotExists** , que retorna fila especificada se ele já existe ou cria uma nova fila com o nome especificado, se ele ainda não exista.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Se a fila for criada, `result.created` é verdadeira. Se a fila existir, `result.created` for falsa.

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **QueueService**. Operações de filtragem pode incluir log automaticamente repetindo, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Depois de fazer seu pré-processamento na lista Opções de solicitação, o método precisa chamar "próximo" passando um retorno de chamada com a assinatura a seguir:

    function (returnObject, finalCallback, next)

Esse retorno de chamada e, depois de processar a returnObject (a resposta da solicitação de servidor), o retorno de chamada precisa para invocar Avançar se existir para continuar processando outros filtros ou simplesmente invocar finalCallback contrário para terminar a chamada de serviço.

Dois filtros que implementam lógica de repetição são fornecidos com o SDK do Azure para Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguir cria um objeto de **QueueService** que usa o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método de **createMessage** para criar uma nova mensagem e adicioná-la à fila.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Como: Inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando o método **peekMessages** . Por padrão, **peekMessages** exibe uma única mensagem.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

O `result` contém a mensagem.

> [AZURE.NOTE] Usando **peekMessages** quando há que nenhuma mensagem na fila não retornará um erro, porém nenhuma mensagem será retornada.

## <a name="how-to-dequeue-the-next-message"></a>Como: Retirar a próxima mensagem

Processamento de uma mensagem é um processo de dois estágios:

1. Retirar a mensagem.

2. Exclua a mensagem.

Para retirar uma mensagem, use **getMessages**. Isso faz com que as mensagens invisível na fila, portanto, não há outros clientes podem processá-los. Depois que seu aplicativo tiver processado uma mensagem, chame **deleteMessage** para excluí-lo da fila. O exemplo a seguir obtém uma mensagem, em seguida, exclui-lo:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] Por padrão, uma mensagem somente está oculta por 30 segundos, após o qual é visível para os outros clientes. Você pode especificar um valor diferente usando `options.visibilityTimeout` com **getMessages**.

> [AZURE.NOTE]
> Usando **getMessages** quando há que nenhuma mensagem na fila não retornará um erro, porém nenhuma mensagem será retornada.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila usando **updateMessage**. O exemplo a seguir atualiza o texto de uma mensagem:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Mensagens de opções adicionais para enfileiramente

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila:

* `options.numOfMessages`-Recuperar um lote de mensagens (até 32).
* `options.visibilityTimeout`-Defina o tempo limite invisibility maior ou menor.

O exemplo a seguir usa o método **getMessages** para receber mensagens de 15 em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite invisibility para cinco minutos para todas as mensagens retornados por esse método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

O **getQueueMetadata** retorna metadados sobre a fila, incluindo o número aproximado de mensagens aguardando na fila.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Como: Filas de lista

Para recuperar uma lista de filas, use **listQueuesSegmented**. Para recuperar uma lista filtrada por um prefixo específico, use **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Se todas as filas não podem ser retornadas, `result.continuationToken` podem ser usados como o primeiro parâmetro de **listQueuesSegmented** ou o segundo parâmetro da **listQueuesSegmentedWithPrefix** para recuperar mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, chame o método **deleteQueue** no objeto fila.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

Para limpar todas as mensagens de uma fila sem excluí-lo, use **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: trabalhar com assinaturas de acesso compartilhado

Assinaturas de acesso compartilhado (SAS) são uma maneira segura para fornecer acesso granular a filas sem fornecer o seu nome de conta de armazenamento ou teclas. SAS muitas vezes são usados para fornecer acesso limitado ao seu filas, como permitir que um aplicativo móvel enviar mensagens.

Um aplicativo confiável como um serviço baseado em nuvem gera uma SAS usando o **generateSharedAccessSignature** da **QueueService**e fornece-o a um aplicativo de confiança parcial ou não confiável. Por exemplo, um aplicativo móvel. As associações de segurança é gerada usando uma política, que descreve as datas de início e término durante o qual as associações de segurança é válida, bem como o nível de acesso concedido ao proprietário SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o proprietário SAS adicionar mensagens à fila e expira 100 minutos após o horário em que ele é criado.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Observe que as informações do host devem ser fornecidas também, conforme ela é necessária quando o proprietário SAS tenta acessar a fila.

O aplicativo cliente usa as associações de segurança com **QueueServiceWithSAS** para executar operações em relação a fila. O exemplo a seguir se conecta à fila e cria uma mensagem.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Desde que as associações de segurança foi gerada com acesso de adicionar, se foi feita uma tentativa de ler, atualizar ou excluir mensagens, um erro será retornado.

### <a name="access-control-lists"></a>Listas de controle de acesso

Você também pode usar uma lista de controle de acesso (ACL) para definir a política de acesso uma Associações de. Isso é útil se você quiser permitir que vários clientes acessar a fila, mas oferece políticas de acesso diferente para cada cliente.

Uma ACL é implementada usando uma matriz de políticas de acesso, com uma identificação associada a cada diretiva. O exemplo a seguir define duas diretivas; um para 'Usuário1' e outra para 'Usuário2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

O exemplo a seguir obtém a ACL atual para **Minha fila**, em seguida, adiciona as novas diretivas usando **setQueueAcl**. Essa abordagem permite:

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Depois que a ACL tiver sido definida, você poderá criar uma SAS com base na identificação de uma política. O exemplo a seguir cria uma novas associações de segurança para 'Usuário2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

-   Visite o [Blog da equipe de armazenamento do Azure][].
-   Visite o repositório de [SDK de armazenamento do Azure para nó][] no GitHub.

  [Armazenamento do Azure SDK para nó]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Criar um aplicativo da web de Node no serviço de aplicativo do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Node web app usando o serviço de tabela do Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Criar e implantar um aplicativo Node para um serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Construir e implantar um aplicativo de web Node para Azure usando Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
