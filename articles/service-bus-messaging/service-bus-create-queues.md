<properties 
    pageTitle="Escrever aplicativos que usa filas de barramento de serviço | Microsoft Azure"
    description="Como escrever um aplicativo baseado em fila simples que usa barramento de serviço."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Criar aplicativos que usam filas de barramento de serviço

Este tópico descreve filas de barramento de serviço e mostra como escrever um aplicativo baseado em fila simples que usa barramento de serviço.

Considere um cenário do mundo de varejo no qual os dados de vendas de individuais Point-of-Sale terminais devem ser direcionados a um sistema de gerenciamento de estoque que usa os dados para determinar quando estoque tem a serem reabastecidos. Esta solução usa barramento de serviço de mensagens para a comunicação entre os terminais e o sistema de gerenciamento de estoque, conforme ilustrado na figura a seguir:

![Imagem de filas de barramento de serviço 1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal relatórios seus dados de vendas enviando mensagens para o **DataCollectionQueue**. Essas mensagens permanecem nessa fila até que eles são recuperados pelo sistema de gerenciamento de estoque. Esse padrão geralmente é chamado de *mensagens assíncronas*, porque o terminal de POS não tem de esperar uma resposta do sistema de gerenciamento de estoque para continuar o processamento.

## <a name="why-queuing"></a>Por que fila?

Antes de olharmos para o código que é necessário configurar esse aplicativo, considere as vantagens de usar uma fila nesse cenário em vez de ter terminais de POS falam diretamente (sincronia) para o sistema de gerenciamento de estoque.

### <a name="temporal-decoupling"></a>Separação temporal

Com o padrão de mensagens assíncrono, produtores e consumidores não precisa estar online ao mesmo tempo. A infraestrutura de mensagens confiável armazena mensagens até que o participante de consumo está pronto para recebê-los. Isso significa que os componentes do aplicativo distribuído podem ser desconectados, ou voluntariamente; Por exemplo, para manutenção ou devido a uma falha de componente, sem afetar todo o sistema. Além disso, o aplicativo de consumo precise apenas estar online durante determinadas horas do dia. Por exemplo, neste cenário de varejo, o sistema de gerenciamento de estoque só precise ficar online após o final do expediente.

### <a name="load-leveling"></a>Redistribuição de carga

Em muitos aplicativos carga do sistema varia ao longo do tempo, enquanto o tempo de processamento necessário para cada unidade de trabalho é geralmente constante. Intermediating produtores de mensagem e consumidores com uma fila significa que o aplicativo de consumo (trabalhador) só precisa ser provisionado para uma média de carga em vez de uma carga de pico de serviço. A profundidade da fila crescerão e como a carga de entrada varia de contratos. Isso diretamente economiza dinheiro em relação à quantidade de infraestrutura necessária para a carga de aplicativo de serviço.

![Imagem de filas de barramento de serviço 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Balanceamento de carga

Conforme a carga aumenta, mais processos de trabalho podem ser adicionados a leitura da fila de trabalho. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, este balanceamento de carga com base em recepção permite para uso ideal dos computadores trabalhador mesmo se os computadores de trabalhador diferem em relação à potência de processamento, conforme eles busca mensagens em seu próprio ritmo máximo. Esse padrão geralmente é chamado de concorrência padrão consumidor.

![Imagem de filas de barramento de serviço 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Acoplamento

Usar o enfileiramento de mensagens para intermediário entre os consumidores e produtores de mensagem fornece um acoplamento intrínseco entre os componentes. Como produtores e consumidores não são conhecimento uns dos outros, um consumidor pode ser atualizado sem ter qualquer efeito sobre o produtor. Além disso, a topologia de mensagens pode evoluir sem afetar os pontos de extremidade existentes. Abordaremos isso mais quando falarmos sobre publicar/assinar.

## <a name="show-me-the-code"></a>Mostre-me o código

A seção a seguir mostra como usar o barramento de serviço para criar esse aplicativo.

### <a name="sign-up-for-an-azure-account"></a>Inscrever-se para uma conta do Azure

Você precisará de uma conta do Azure para começar a trabalhar com barramento de serviço. Se você ainda não tiver um, você pode se inscrever para uma conta gratuita [aqui](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Criar um namespace

Quando você tiver uma assinatura, você pode [criar um novo namespace](service-bus-create-namespace-portal.md). Cada namespace atua como um contêiner de escopo para um conjunto de entidades de barramento de serviço. Dê um nome exclusivo para seu novo namespace em todas as contas de barramento de serviço. 

### <a name="install-the-nuget-package"></a>Instale o pacote do NuGet

Para usar o namespace barramento de serviço, um aplicativo deve fazer referência ao assembly de barramento de serviço, especificamente Microsoft.ServiceBus.dll. Você pode encontrar esse assembly como parte do Microsoft Azure SDK e o download está disponível no [SDK do Azure página de download](https://azure.microsoft.com/downloads/). No entanto, o [pacote de serviço barramento NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil para obter a API do barramento de serviço e configurar o aplicativo com todas as dependências de barramento de serviço.

### <a name="create-the-queue"></a>Criar a fila

Operações de gerenciamento para barramento de serviço entidades (tópicos filas e publicar/assinar) de mensagens são realizadas por meio da classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Barramento de serviço usa um modelo de segurança baseada em [Assinatura de acesso compartilhado (SAS)](service-bus-sas-overview.md) . A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um provedor de token de segurança com métodos de fábrica internos retornando alguns provedores de token conhecidos. Usaremos um método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para armazenar as credenciais SAS. A instância do [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é então construída com o endereço base do namespace barramento de serviço e o provedor de token.

A classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornece métodos para criar, enumerar e excluir entidades mensagens. O código que é mostrado aqui mostra como a instância do [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e usada para criar a fila de **DataCollectionQueue** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Observe que há cargas do método [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) que lhe permitem propriedades da fila para ser ajustado. Por exemplo, você pode definir o valor padrão para vida (útil TTL) para mensagens enviadas para a fila.

### <a name="send-messages-to-the-queue"></a>Enviar mensagens à fila

Para operações de tempo de execução em entidades barramento de serviço; Por exemplo, enviar e receber mensagens, um aplicativo deve primeiro criar um objeto de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Semelhante à classe [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , a instância de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) é criada do endereço base do namespace de serviço e o provedor de token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

As mensagens enviadas para e recebidos de barramento de serviço filas são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Essa classe consiste em um conjunto de propriedades padrão (como [rótulo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é usado para armazenar propriedades de aplicativo e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo passando qualquer objeto serializável (o exemplo a seguir passa em um objeto de **SalesData** que representa os dados de vendas do terminal POS), que usará o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar o objeto. Como alternativa, um objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) pode ser fornecido.

A maneira mais fácil para enviar mensagens para uma determinada fila, no nosso caso o **DataCollectionQueue**, é usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente da instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Receber mensagens de fila

Para receber mensagens da fila, você pode usar um objeto de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que você criar diretamente do [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) usando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Receptores de mensagens podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**. O [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) é definido quando o destinatário da mensagem for criado, como um parâmetro à chamada [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Ao usar o modo de **ReceiveAndDelete** , o recebimento é uma operação de captura de único; Isto é, quando barramento de serviço recebe a solicitação, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que o aplicativo pode suportar não processando uma mensagem se uma falha ocorrer. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Como barramento de serviço marcado a mensagem como sendo consumida, quando a reiniciar o aplicativo e inicia consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes do travamento.

No modo de **PeekLock** , o recebimento torna-se uma operação de dois estágios, que torna possível dar suporte a aplicativos que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe a solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando barramento de serviço vê [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) a chamada, ele marca a mensagem como sendo consumida.

Dois outros resultados são possíveis. Primeiro, se o aplicativo for não é possível processar a mensagem por algum motivo, ele pode chamar a [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) na mensagem recebida (em vez de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso faz com que o barramento de serviço desbloquear a mensagem e disponibilizá-lo a ser recebida novamente, pelo consumidor do mesmo ou por outro consumidor Concluindo. Segundo, há um tempo limite associado com o bloqueio e se o aplicativo não é possível processar a mensagem antes do bloqueio tempo limite expirar (por exemplo, se o aplicativo trava), barramento de serviço será desbloquear a mensagem e disponibilizá-lo a ser recebida novamente (essencialmente uma operação [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) por padrão).

Observe que se o aplicativo travar depois ele processa a mensagem, mas antes de [Concluir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) a solicitação foi emitida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso geralmente é chamado de processamento de *Pelo menos uma vez* . Isso significa que cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, lógica adicional é necessária no aplicativo para detectar duplicatas. Isso pode ser feito com base na propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem. O valor desta propriedade permanece constante entre tentativas de entrega. Isso é chamado de processamento de *Exatamente uma vez* .

O código que é mostrado aqui recebe e processa uma mensagem usando o modo de **PeekLock** , que é o padrão se nenhum valor [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) explicitamente é fornecido.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### <a name="use-the-queue-client"></a>Usar o cliente de fila

Exemplos anteriores nesta seção criada objetos [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) diretamente a partir do [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) para enviar e receber mensagens de fila, respectivamente. Uma abordagem alternativa é usar a classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , que suporta envie e receba operações além dos recursos mais avançados, como sessões.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas, consulte [criar aplicativos que usam assinaturas e tópicos de barramento de serviço](service-bus-create-topics-subscriptions.md) para continuar essa discussão usando os recursos de publicação/assinatura de tópicos do barramento de serviço e assinaturas.