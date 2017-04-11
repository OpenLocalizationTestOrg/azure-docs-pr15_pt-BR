<properties
    pageTitle="Como usar tópicos de barramento de serviço (Ruby) | Microsoft Azure"
    description="Saiba como usar tópicos de barramento de serviço e assinaturas no Azure. Exemplos de código destinam-se a aplicativos Ruby."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Como usar o serviço de barramento tópicos/assinaturas

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar assinaturas de aplicativos Ruby e tópicos de barramento de serviço. Os cenários cobertos incluem a **criação de tópicos e assinaturas, criando filtros de assinatura, enviando mensagens de** um tópico, **receber mensagens de uma assinatura**e **Excluindo tópicos e assinaturas**. Para obter mais informações sobre os tópicos e assinaturas, consulte a seção [Próximas etapas](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos de barramento de serviço e assinaturas

Tópicos de barramento de serviço e assinaturas suportam a uma *publicação/assinatura* modelo de comunicação de mensagens. Ao usar tópicos e assinaturas, componentes de um aplicativo distribuído não comunicarem diretamente; em vez disso, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com filas de barramento de serviço, onde cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem um formulário de **um-para-muitos** de comunicação, usando um padrão publicar/assinar. É possível registrar várias assinaturas a um tópico. Quando uma mensagem é enviada para um tópico, ele é então disponibilizado para cada assinatura para processar de maneira independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, você pode registrar regras de filtro para um tópico numa base por assinatura, que permite filtrar/restringir quais mensagens a um tópico são recebidas pelo quais assinaturas de tópico.

Tópicos de barramento de serviço e assinaturas permitem que você dimensionar para processar um grande número de mensagens em um grande número de usuários e aplicativos.

## <a name="create-a-namespace"></a>Criar um namespace

Para começar a usar filas de barramento de serviço no Azure, você deve primeiro criar um namespace. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo. Você deve criar o espaço para nome por meio da interface de linha de comando porque o [portal do Azure][] não criar namespace com uma conexão do ACS.

Para criar um namespace:

1. Abra uma janela de console do Powershell do Azure.

2. Digite o seguinte comando para criar um namespace. Forneça seu próprio valor namespace e especifique o mesmo região como seu aplicativo.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Criar Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obtenha padrão credenciais de gerenciamento do namespace

Para realizar operações de gerenciamento, como criar uma fila no novo namespace, você deve obter as credenciais de gerenciamento do namespace.

O cmdlet do PowerShell que você executou para criar o namespace barramento de serviço exibe a chave que você pode usar para gerenciar namespace. Copie o valor de **DefaultKey** . Você usará esse valor em seu código posteriormente neste tutorial.

![Copiar chave](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> Você também pode encontrar esta tecla se você faça logon no [portal do Azure][] e navegar até as informações de conexão para o seu espaço para nome.

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby

Para obter instruções, consulte [criar um aplicativo Ruby no Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para barramento de serviço de uso

Para usar barramento de serviço, baixar e usar o pacote Ruby Azure, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-rubygems-to-obtain-the-package"></a>Use RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "azure de instalação de gem" na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Usando o editor de texto favorito, adicione o seguinte à parte superior do arquivo Ruby no qual você pretende usar o armazenamento:

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão de barramento de serviço

O módulo Azure lê as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_acesso\_chave** para informações necessárias para se conectar ao seu espaço para nome. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações de namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina o valor de espaço para nome para o valor que você criou em vez de toda a URL. Por exemplo, use **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## <a name="create-a-topic"></a>Criar um tópico

O objeto **Azure::ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **Azure::ServiceBusService** . Para criar um tópico, use o **criar\_topic()** método. O exemplo a seguir cria um tópico ou imprime os erros se houver algum.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Você também pode passar um objeto de **Azure::ServiceBus::Topic** opções adicionais, que permitem substituir as configurações de tópico padrão como o tempo de mensagem Live ou o tamanho máximo da fila. O exemplo a seguir mostra definindo o tamanho máximo da fila para 5GB e a hora ao vivo para 1 minuto:

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar assinaturas

Assinaturas de tópico também são criadas com o objeto **Azure::ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da assinatura.

Assinaturas são persistentes e continuarão a existir até qualquer ou o tópico estão associados com, são excluídos. Se seu aplicativo contém lógica para criar uma assinatura, deve primeiro verificar se a assinatura já existe usando o método getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

O filtro de **MatchAll** é o padrão que é usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro de **MatchAll** é usado, publicadas para o tópico de todas as mensagens são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "todas as mensagens" e usa o filtro de **MatchAll** padrão.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode definir filtros que permitem especificar quais mensagens enviadas para um tópico deverão ser mostrada dentro de uma assinatura específica.

O tipo mais flexível de filtro compatível com assinaturas é o **Azure::ServiceBus::SqlFilter**, que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Você pode adicionar filtros para uma assinatura usando o **criar\_rule()** método do objeto **Azure::ServiceBusService** . Este método permite que você adicione novos filtros uma assinatura existente.

Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, você deve primeiro remover o filtro padrão ou o **MatchAll** substituirá quaisquer outros filtros que você pode especificar. Você pode remover a regra padrão usando o **Excluir\_rule()** método no objeto **Azure::ServiceBusService** .

O exemplo a seguir cria uma assinatura denominada "mensagens de alta" com um **Azure::ServiceBus::SqlFilter** que seleciona somente as mensagens que têm um personalizado **mensagem\_número** propriedade maior que 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Da mesma forma, o exemplo a seguir cria uma assinatura denominada "mensagens de baixa" com um **Azure::ServiceBus::SqlFilter** que seleciona somente as mensagens que têm uma **message_number** propriedade menor ou igual a 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Quando uma mensagem é enviada agora para "tópico de teste", ele é sempre ser entregue a receptores inscrito para a assinatura de tópico "todas as mensagens" e entregue seletivamente para receptores assinados as assinaturas de tópico "mensagens de alta" e "mensagens de baixa" (dependendo o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, o aplicativo deve usar o **Enviar\_tópico\_message()** método no objeto **Azure::ServiceBusService** . As mensagens enviadas para os tópicos de barramento de serviço são instâncias dos objetos **Azure::ServiceBus::BrokeredMessage** . Objetos de **Azure::ServiceBus::BrokeredMessage** têm um conjunto de propriedades padrão (como o **rótulo** e **tempo\_para\_live**), um dicionário que é usado para armazenar propriedades específicas do aplicativo personalizado e um corpo de dados de cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem, passando um valor de cadeia de caracteres para o **Enviar\_tópico\_message()** método e qualquer obrigatório propriedades padrão serão preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco testar as mensagens "tópico de teste". Observe que o valor da propriedade personalizada de **message_number** de cada mensagem varia na iteração do loop (Isso determina qual assinatura recebe):

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Tamanho este tópico é definido em tempo de criação, com um limite de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura usando o **receber\_assinatura\_message()** método no objeto **Azure::ServiceBusService** . Por padrão, as mensagens são read(peak) e bloqueada sem excluí-lo da assinatura. Você pode ler e excluir a mensagem da assinatura, definindo a **espiada\_lock** opção como **false**.

O comportamento padrão torna a leitura e a exclusão de uma operação de dois estágios, que também possibilita dar suporte a aplicativos que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando **Excluir\_assinatura\_message()** método e fornecendo a mensagem a ser excluída como um parâmetro. O **Excluir\_assinatura\_message()** método será marcar a mensagem como sendo consumida e removê-lo da assinatura.

Se o **: espiada\_lock** parâmetro for definido como **false**, ler e excluir a mensagem se torna o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como mensagens podem ser recebidas e processadas usando **receber\_assinatura\_message()**. O exemplo primeiro recebe e exclui uma mensagem da assinatura "mensagens de baixa" usando **: espiada\_lock** definida como **false**, ele recebe outra mensagem das "alto-mensagens" e, em seguida, exclui a mensagem usando **Excluir\_assinatura\_message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor é possível processar a mensagem por algum motivo, ele pode chamar a **Desbloquear\_assinatura\_message()** método no objeto **Azure::ServiceBusService** . Isso faz com que o barramento de serviço desbloquear a mensagem na assinatura e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processar a mensagem mas antes do **Excluir\_assinatura\_message()** método é chamado, depois a mensagem for entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**; ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Essa lógica geralmente é obtida usando o **mensagem\_id** propriedade da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Tópicos assinaturas são persistentes e devem ser explicitamente excluídas por meio do [portal do Azure][] ou programaticamente. O exemplo a seguir demonstra como excluir o tópico chamado "teste de tópico".

```
azure_service_bus_service.delete_topic("test-topic")
```

Excluir um tópico também exclui qualquer assinaturas que são registradas com o tópico. Assinaturas também podem ser excluídas independentemente. O código a seguir demonstra como excluir a assinatura denominada "mensagens de alta" do tópico "tópico de teste":

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de tópicos do barramento de serviço, siga estes links para saber mais.

- Consulte [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
- Referência de API do [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visite o repositório de [SDK do Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.
 
[Portal do Azure]: https://portal.azure.com
