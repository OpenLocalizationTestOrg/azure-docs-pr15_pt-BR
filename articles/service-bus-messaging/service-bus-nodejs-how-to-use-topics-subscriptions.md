<properties 
    pageTitle="Como usar tópicos de barramento de serviço com Node | Microsoft Azure" 
    description="Saiba como usar tópicos de barramento de serviço e assinaturas no Azure de um aplicativo de Node." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como os tópicos de barramento de serviço de uso e assinaturas

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como usar tópicos de barramento de serviço e assinaturas de aplicativos Node. Os cenários cobertos incluem **criar tópicos e assinaturas**, **Criando filtros de assinatura**, **enviando mensagens de** um tópico, **receber mensagens de uma assinatura**e **Excluindo tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [próximas etapas](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo de Node

Crie um aplicativo Node em branco. Para obter instruções sobre como criar um aplicativo de Node, consulte [criar e implantar um aplicativo de Node para um Site do Azure], [Serviço de nuvem Node][] usando o Windows PowerShell ou o Site da Web com o WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Para usar barramento de serviço, baixe o pacote do Azure Node. Este pacote inclui um conjunto de bibliotecas que se comunicar com os serviços de serviço barramento restante.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gerenciador de pacote de nó (NPM) para obter o pacote

1.  Usar uma interface de linha como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), navegue até a pasta onde você criou seu aplicativo de amostra.

2.  Digite **npm instalar azure** na janela de comando, que deve resultar na saída a seguir:

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  Você pode executar manualmente o comando **ls** para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localize o pacote do **azure** , que contém as bibliotecas que você precisa para acessar os tópicos de barramento de serviço.

### <a name="import-the-module"></a>Importar o módulo

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão de barramento de serviço

O módulo Azure lê as variáveis de ambiente AZURE\_SERVICEBUS\_NAMESPACE e AZURE\_SERVICEBUS\_acesso\_chave para informações necessárias para se conectar ao barramento de serviço. Se essas variáveis de ambiente não estiverem definidas, especifique as informações da conta ao chamar **createServiceBusService**.

Para um exemplo de definindo as variáveis de ambiente em um arquivo de configuração para um serviço de nuvem do Azure, consulte [Serviço de nuvem Node com armazenamento][].

Para obter um exemplo de definir as variáveis de ambiente no [Azure portal clássico][] para um site do Azure, consulte [Aplicação de Web Node com armazenamento][].

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **ServiceBusService** . Adicione-o na parte superior do arquivo **Server. js** , após a instrução para importar o módulo azure:

```
var serviceBusService = azure.createServiceBusService();
```

Chamando **createTopicIfNotExists** no objeto **ServiceBusService** , será retornado o tópico especificado (se existir) ou um novo tópico com o nome especificado será criado. O código a seguir usa **createTopicIfNotExists** para criar ou conectar o tópico chamado 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** também suporta opções adicionais, que permitem substituir configurações de tópico padrão como o tempo de live mensagem ou o tamanho máximo de tópicos. O exemplo a seguir define o tamanho máximo de tópico para 5GB com um tempo de vida de um minuto:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **ServiceBusService**. Operações de filtragem pode incluir log automaticamente repetindo, etc. Filtros são objetos que implementam um método com a assinatura:

```
function handle (requestOptions, next)
```

Depois de executar pré-processamento na lista Opções de solicitação, o método chama `next` passando um retorno de chamada com a assinatura a seguir:

```
function (returnObject, finalCallback, next)
```

Esse retorno de chamada e, após o processamento **returnObject** (a resposta da solicitação de servidor), o retorno de chamada precisa seja invoca Avançar se existir para continuar a outros filtros de processamento ou simplesmente invocar **finalCallback** contrário para terminar a chamada de serviço.

Dois filtros que implementam lógica de repetição são fornecidos com o SDK do Azure para Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguir cria um objeto de **ServiceBusService** que usa o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Criar assinaturas

Assinaturas de tópico também são criadas com o objeto **ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da assinatura.

> [AZURE.NOTE] Assinaturas são persistentes e continuarão a existir até qualquer ou o tópico estão associados com, são excluídos. Se seu aplicativo contém lógica para criar uma assinatura, deve primeiro verificar se a assinatura já existe usando o método **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

O filtro de **MatchAll** é o padrão que é usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro de **MatchAll** é usado, publicadas para o tópico de todas as mensagens são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma inscrição chamada 'AllMessages' e usa o filtro de **MatchAll** padrão.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode criar filtros que permitem a você escopo que as mensagens enviadas para um tópico deverá ser mostrada dentro de uma assinatura de tópico específico.

O tipo mais flexível de filtro compatível com assinaturas é o **SqlFilter**, que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] sintaxe.

Filtros podem ser adicionados a uma assinatura usando o método **createRule** do objeto **ServiceBusService** . Este método permite que você adicione novos filtros uma assinatura existente.

> [AZURE.NOTE] Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, você deve primeiro remover o filtro padrão ou o **MatchAll** substituirá quaisquer outros filtros que você pode especificar. Você pode remover a regra padrão, usando o método **deleteRule** do objeto **ServiceBusService** .

O exemplo a seguir cria uma inscrição chamada `HighMessages` com um **SqlFilter** que seleciona somente as mensagens que têm uma propriedade personalizada **messagenumber** maior que 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Da mesma forma, o exemplo a seguir cria uma inscrição chamada `LowMessages` com um **SqlFilter** que seleciona somente as mensagens que têm uma **messagenumber** propriedade menor ou igual a 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando uma mensagem agora é enviada para `MyTopic`, ele sempre sejam entregues em receptores inscritos na `AllMessages` assinatura do tópico e seletivamente entregue para receptores assinados o `HighMessages` e `LowMessages` assinaturas de tópico (dependendo o conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, seu aplicativo deve usar o método **sendTopicMessage** do objeto **ServiceBusService** .
As mensagens enviadas para os tópicos de barramento de serviço são **BrokeredMessage** objetos.
Objetos de **BrokeredMessage** têm um conjunto de propriedades padrão (como **rótulo** e **TimeToLive**), um dicionário que é usado para armazenar propriedades específicas do aplicativo personalizado e um corpo de dados de cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem, passando um valor de cadeia de caracteres para o **sendTopicMessage** e as propriedades padrão obrigatórias serão preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens para 'MyTopic' de teste. Observe que o valor da propriedade **messagenumber** de cada mensagem varia a iteração do loop (isso determinará quais assinaturas recebam-lo):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Tamanho este tópico é definido em tempo de criação, com um limite de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

Mensagens são recebidas de uma assinatura usando o método **receiveSubscriptionMessage** no objeto **ServiceBusService** . Por padrão, as mensagens são excluídas da assinatura conforme eles são lidos; No entanto, você pode ler (espiada) e bloquear a mensagem sem excluí-lo da assinatura, definindo o parâmetro opcional **isPeekLock** como **true**.

O comportamento padrão de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** é definido como **true**, o recebimento torna-se uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando **deleteMessage** método e fornecendo a mensagem a ser excluída como um parâmetro. O método **deleteMessage** será marcar a mensagem como sendo consumida e removê-lo da assinatura.

O exemplo a seguir demonstra como mensagens podem ser recebidas e processadas usando **receiveSubscriptionMessage**. O exemplo primeiro recebe exclui uma mensagem da assinatura 'LowMessages' e, em seguida, recebe uma mensagem da assinatura 'HighMessages' usando **isPeekLock** definido como true. Em seguida, exclui a mensagem usando o **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **unlockMessage** no objeto **ServiceBusService** . Isso fará com que o barramento de serviço desbloquear a mensagem na assinatura e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, serviço desbloqueie a mensagem automaticamente e disponibiliza a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes do método **deleteMessage** é chamado, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é obtido usando a propriedade **MessageId** da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Tópicos assinaturas são persistentes e devem ser explicitamente excluídas por meio do [Azure portal clássico][] ou programaticamente.
O exemplo a seguir demonstra como excluir o tópico chamado `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Excluir um tópico também excluirá qualquer assinaturas que são registradas com o tópico. Assinaturas também podem ser excluídas independentemente. O exemplo a seguir mostra como excluir uma inscrição chamada `HighMessages` do `MyTopic` tópico:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de tópicos do barramento de serviço, siga estes links para saber mais.

-   Consulte [filas, tópicos e assinaturas][].
-   Referência de API do [SqlFilter][].
-   Visite o repositório de [SDK do Azure para nó][] no GitHub.

  [SDK do Azure para nó]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal clássico]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Serviço de nuvem Node]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Criar e implantar um aplicativo de Node para um Site do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Serviço de nuvem Node com armazenamento]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Aplicativo Web do Node com armazenamento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
