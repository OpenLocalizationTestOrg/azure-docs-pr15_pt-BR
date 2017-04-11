<properties 
    pageTitle="Criar aplicativos que usam tópicos de barramento de serviço e assinaturas | Microsoft Azure"
    description="Introdução ao publicar-assinar recursos oferecidos pelo tópicos de barramento de serviço e assinaturas."
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

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Criar aplicativos que usam assinaturas e tópicos de barramento de serviço

Barramento de serviço do Azure suporta um conjunto de tecnologias de middleware baseado em nuvem, orientados a mensagem incluindo enfileiramento confiável e durável publicar/assinar mensagens. Este artigo se baseia nas informações fornecidas em [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md) e oferece uma introdução aos recursos de publicação/assinatura oferecidas por tópicos barramento de serviço.

## <a name="evolving-retail-scenario"></a>Cenário de varejo evolução

Este artigo continua o cenário de varejo usado em [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md). Lembre-se que dados de vendas de individuais ponto de venda (POS) terminais devem ser roteados para um sistema de gerenciamento de estoque que usa esses dados para determinar quando estoque tem a serem reabastecidos. Cada terminal relatórios seus dados de vendas enviando mensagens para a fila de **DataCollectionQueue** , onde eles permanecem até que elas são recebidas pelo sistema de gerenciamento de estoque, como mostrado aqui:

![Barramento de serviço 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Para evoluir neste cenário, um novo requisito foi adicionado ao sistema: o proprietário da loja quer ser capaz de monitorar o armazenamento de desempenho em tempo real.

Para resolver esse requisito, o sistema deve "toque" desativar o fluxo de dados de vendas. Nós ainda quiser cada mensagem enviada por terminais de POS sejam enviadas para o sistema de gerenciamento de estoque como antes, mas podemos outra cópia de cada mensagem que podemos usar para apresentar o modo de exibição do painel de controle para o proprietário da loja.

Em qualquer situação como esta, no qual você exige cada mensagem para ser consumida por vários participantes, você pode usar o serviço barramento *Tópicos*. Tópicos fornecem um padrão de publicação/assinatura em que cada mensagem publicada é disponibilizada para uma ou mais assinaturas registradas com o tópico. Em contraste, com filas cada mensagem for recebida por um único consumidor.

Mensagens são enviadas para um tópico da mesma maneira como eles são enviados para uma fila. No entanto, as mensagens não são recebidas do tópico diretamente; eles são recebidos de assinaturas. Você pode pensar uma assinatura a um tópico como uma fila virtual que recebe cópias das mensagens que são enviadas para esse tópico. Mensagens são recebidas de uma assinatura da mesma maneira como eles são recebidos de uma fila.

Voltar para o cenário de varejo, fila é substituída por um tópico e uma assinatura é adicionada, o que pode usar o componente de sistema de gerenciamento de estoque. O sistema agora aparece da seguinte maneira:

![Barramento de serviço 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

A configuração aqui executa idêntico ao design baseado em fila anterior. Ou seja, as mensagens enviadas para o tópico são roteadas para a assinatura de **estoque** , do qual o **Sistema de gerenciamento de estoque** consome-los.

Para o painel de gerenciamento de suporte, criamos uma segunda assinatura no tópico, como mostrado aqui:

![Barramento de serviço 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Com essa configuração, cada mensagem de terminais de POS é disponibilizada às assinaturas o **painel** e de **estoque** .

## <a name="show-me-the-code"></a>Mostre-me o código

O artigo [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md) descreve como inscrever-se para uma conta do Azure e criar um namespace de serviço. Para usar um namespace barramento de serviço, um aplicativo deve fazer referência ao assembly de barramento de serviço, especificamente Microsoft.ServiceBus.dll. A maneira mais fácil para fazer referência dependências de barramento de serviço é instalar o [pacote do Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)do barramento de serviço. Você também pode encontrar o assembly como parte do SDK do Azure. O download está disponível no [SDK do Azure página de download](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Crie o tópico e assinaturas

Operações de gerenciamento para barramento de serviço entidades (tópicos filas e publicar/assinar) de mensagens são realizadas por meio da classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Credenciais apropriadas são necessárias para criar uma instância de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para um namespace específico. Barramento de serviço usa um modelo de segurança baseada em [Assinatura de acesso compartilhado (SAS)](service-bus-sas-overview.md) . A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um provedor de token de segurança com métodos de fábrica internos retornando alguns provedores de token conhecidos. Usaremos um método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para armazenar as credenciais SAS. A instância do [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é então construída com o endereço base do namespace barramento de serviço e o provedor de token.

A classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornece métodos para criar, enumerar e excluir entidades mensagens. O código que é mostrado aqui mostra como a instância do [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e usada para criar o tópico **DataCollectionTopic** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Observe que há sobrecarga do método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) que permite que você defina propriedades do tópico. Por exemplo, você pode definir o valor padrão para vida (útil TTL) para mensagens enviadas para o tópico. Em seguida, adicione as assinaturas de **estoque** e **painéis** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Enviar mensagens para o tópico

Para operações de tempo de execução em entidades barramento de serviço; Por exemplo, enviar e receber mensagens, um aplicativo deve primeiro criar um objeto de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semelhante à classe [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , a instância de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) é criada do endereço base do namespace de serviço e o provedor de token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

As mensagens enviadas para e recebidas dos tópicos do barramento de serviço, são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Essa classe consiste em um conjunto de propriedades padrão (como [rótulo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é usado para armazenar propriedades de aplicativo e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo passando qualquer objeto serializável (o exemplo a seguir passa em um objeto de **SalesData** que representa os dados de vendas do terminal POS), que usará o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar o objeto. Como alternativa, um objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) pode ser fornecido.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

A maneira mais fácil para enviar mensagens para o tópico é usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente da instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

Semelhante ao uso filas, para receber mensagens de uma assinatura que você pode usar um objeto de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que você criar diretamente do [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) usando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Você pode usar um dos dois diferentes receber modos (**ReceiveAndDelete** e **PeekLock**), conforme discutido em [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md).

Observe que, quando você cria um [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para assinaturas, o parâmetro *entityPath* é do formulário `topicPath/subscriptions/subscriptionName`. Portanto, para criar um [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para a assinatura de **inventário** do tópico **DataCollectionTopic** , *entityPath* deve ser definida como `DataCollectionTopic/subscriptions/Inventory`. O código aparece da seguinte maneira:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtros de assinatura

Até agora, neste cenário todas as mensagens enviadas para o tópico são disponibilizadas para todas as assinaturas registradas. A expressão-chave aqui é "disponibilizada." Enquanto assinaturas de barramento de serviço ver todas as mensagens enviadas para o tópico, você pode copiar apenas um subconjunto dessas mensagens na fila de assinatura virtual. Isso é executado usando *filtros*de assinatura. Quando você cria uma assinatura, você pode fornecer uma expressão de filtro na forma de um predicado de estilo de SQL92 que operam sobre as propriedades da mensagem, as propriedades do sistema (por exemplo, o [rótulo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) e as propriedades do aplicativo, como **StoreName** no exemplo anterior.

Um armazenamento segundo evoluir o cenário para ilustrar isso, é a ser adicionado ao nosso cenário de varejo. Dados de vendas de todos os terminais de POS de ambos os armazenamentos ainda precisam ser roteados para o sistema de gerenciamento de estoque centralizado, mas um gerente da loja usando a ferramenta de dashboard só está interessado no desempenho de armazenamento. Você pode usar assinatura filtragem para fazer isso. Observe que, quando os terminais de POS publicar mensagens, eles defina a propriedade de aplicativo **StoreName** na mensagem. Determinado dois armazenamentos, por exemplo, **Redmond** e **Seattle**, terminais de POS em a Redmond armazenam carimbo de suas mensagens de dados de vendas com um **StoreName** igual a **Redmond**, enquanto os terminais de POS de repositório de Seattle usam um **StoreName** igual a **Seattle**. O gerente da loja do armazenamento Redmond só quer ver os dados de seus terminais de POS. O sistema aparecerá da seguinte maneira:

![Bus4 de serviço](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Para configurar esse roteamento, você cria a assinatura do **painel** da seguinte maneira:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Com esse filtro de assinatura, somente as mensagens que têm a propriedade de **StoreName** definida como **Redmond** serão copiadas na fila virtual para a assinatura de **painel** . Há muito mais a filtragem de assinatura, no entanto. Aplicativos podem ter várias regras de filtro por assinatura além a capacidade de modificar as propriedades de uma mensagem, conforme passa para fila virtual de uma assinatura.

## <a name="summary"></a>Resumo

Todos os motivos para usar fila descrito em [criar aplicativos que usam o serviço barramento filas](service-bus-create-queues.md) também se aplicam a tópicos, especificamente:

- Separação temporal – mensagem produtores e consumidores não precisam estar online ao mesmo tempo.

- Redistribuição de carga – picos de carga são suavizados por tópico permitindo que os aplicativos consumindo ser provisionada para média de carga em vez de carga de pico.

- Balanceamento de carga — semelhante a uma fila, você pode ter vários consumidores concorrência listening em uma assinatura única, com cada mensagem entregue a apenas um dos consumidores, assim, balanceamento de carga.

- Acoplamento – você pode evoluir a rede de mensagens sem afetar os pontos de extremidade existentes; Por exemplo, adicionando assinaturas ou alterando filtra a um tópico para permitir para consumidores de novo.

## <a name="next-steps"></a>Próximas etapas

Consulte [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md) para obter informações sobre como usar filas no cenário de varejo de POS.