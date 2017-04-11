<properties
pageTitle="Aprenda a usar o conector de barramento de serviço do Azure em seus aplicativos de lógica | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Conecte ao barramento de serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para fila, envie para tópico, receber de fila e receber de assinatura."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Começar a usar o conector de barramento de serviço do Azure

Conecte ao barramento de serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para fila, envie para tópico, receber de fila e receber de assinatura.

Para usar [qualquer conector](./apis-list.md), primeiro é necessário criar um aplicativo de lógica. Você pode começar pela [criação de um aplicativo de lógica agora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Conectar ao barramento de serviço

Para que seu aplicativo de lógica possa acessar qualquer serviço, primeiro é necessário criar uma conexão ao serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo de lógica e outro serviço.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Use um gatilho de barramento de serviço

Um disparador é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo de lógica. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Usar uma ação de barramento de serviço

Uma ação é uma operação realizada pelo fluxo de trabalho definido em um aplicativo de lógica. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão os detalhes sobre os disparadores, ações e respostas que dá suporte a essa conexão.

### <a name="service-bus-triggers"></a>Gatilhos de barramento de serviço

Barramento de serviço tem os seguintes disparadores:  

|Disparadores | Descrição|
|--- | ---|
|[Quando uma mensagem for recebida em uma fila](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Essa operação aciona um fluxo quando for recebida uma mensagem em uma fila.|
|[Quando uma mensagem for recebida em uma assinatura de tópico](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Essa operação aciona um fluxo de quando uma mensagem for recebida em uma assinatura de tópico.|


### <a name="service-bus-actions"></a>Ações de barramento de serviço

Barramento de serviço tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Enviar mensagem](connectors-create-api-servicebus.md#send-message)|Essa operação envia uma mensagem para uma fila ou tópico.|
### <a name="action-and-trigger-details"></a>Detalhes de ação e disparadores

Aqui estão os detalhes das ações e disparadores para este conector, juntamente com suas respostas.



#### <a name="send-message"></a>Enviar mensagem

|Nome da propriedade| Nome para exibição|Descrição|
| ---|---|---|
|ContentData *|Conteúdo|Conteúdo da mensagem.|
|ContentType|Tipo de conteúdo|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|Propriedades|Pares de chave-valor para cada orientado a propriedade.|
|entityName *|Nome de fila/tópico|Nome da fila ou tópico.|

Esses parâmetros avançados também estão disponíveis:

|Nome da propriedade| Nome para exibição|Descrição|
| ---|---|---|
|MessageId|Id da mensagem|Um valor definido pelo usuário que barramento de serviço pode usar para identificar mensagens duplicadas se habilitada.|
|Para|Para|Endereço para envio.|
|ReplyTo|Responder a|Endereço da fila para responder.|
|ReplyToSessionId|Responder a identificação da sessão|Identificador da sessão para responder.|
|Rótulo|Rótulo|Rótulo de específicos do aplicativo.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Data e hora, em UTC, quando a mensagem será adicionada à fila.|
|Identificação de sessão|Id da sessão|Identificador da sessão.|
|CorrelationId|Id de correlação|Identificador de correlação.|
|TimeToLive|Tempo de vida|A duração, em escalas, que uma mensagem é válida. Inicia a duração de quando a mensagem é enviada ao barramento de serviço.|



Um * indica que uma propriedade é necessária.


#### <a name="when-a-message-is-received-in-a-queue"></a>Quando uma mensagem for recebida em uma fila

|Nome da propriedade| Nome para exibição|Descrição|
| ---|---|---|
|Nome_da_fila *|Nome da fila|Nome da fila.|


Um * indica que uma propriedade é necessária.


##### <a name="output-details"></a>Detalhes de saída

ServiceBusMessage: Esse objeto tem o conteúdo e propriedades de uma mensagem de barramento de serviço.


| Nome da propriedade | Tipo de dados | Descrição |
|---|---|---|
|ContentData|cadeia de caracteres|Conteúdo da mensagem.|
|ContentType|cadeia de caracteres|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|objeto|Pares de chave-valor para cada orientado a propriedade.|
|MessageId|cadeia de caracteres|Um valor definido pelo usuário que barramento de serviço pode usar para identificar mensagens duplicadas se habilitada.|
|Para|cadeia de caracteres|Envie para o endereço.|
|ReplyTo|cadeia de caracteres|Endereço da fila para responder.|
|ReplyToSessionId|cadeia de caracteres|Identificador da sessão para responder.|
|Rótulo|cadeia de caracteres|Rótulo de específicos do aplicativo.|
|ScheduledEnqueueTimeUtc|cadeia de caracteres|Data e hora, em UTC, quando a mensagem será adicionada à fila.|
|Identificação de sessão|cadeia de caracteres|Identificador da sessão.|
|CorrelationId|cadeia de caracteres|Identificador de correlação.|
|TimeToLive|cadeia de caracteres|A duração, em escalas, que uma mensagem é válida. Inicia a duração de quando a mensagem é enviada ao barramento de serviço.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Quando uma mensagem for recebida em uma assinatura de tópico

|Nome da propriedade| Nome para exibição|Descrição|
| ---|---|---|
|topicName *|Nome do tópico|Nome do tópico.|
|nome de inscrição *|Nome de assinatura do tópico|Nome da assinatura do tópico.|


Um * indica que uma propriedade é necessária.


##### <a name="output-details"></a>Detalhes de saída

ServiceBusMessage: Esse objeto tem o conteúdo e propriedades de uma mensagem de barramento de serviço.


| Nome da propriedade | Tipo de dados | Descrição |
|---|---|---|
|ContentData|cadeia de caracteres|Conteúdo da mensagem.|
|ContentType|cadeia de caracteres|Tipo de conteúdo do conteúdo da mensagem.|
|Propriedades|objeto|Pares de chave-valor para cada orientado a propriedade.|
|MessageId|cadeia de caracteres|Um valor definido pelo usuário que barramento de serviço pode usar para identificar mensagens duplicadas se habilitada.|
|Para|cadeia de caracteres|Envie para o endereço.|
|ReplyTo|cadeia de caracteres|Endereço da fila para responder.|
|ReplyToSessionId|cadeia de caracteres|Identificador da sessão para responder.|
|Rótulo|cadeia de caracteres|Rótulo de específicos do aplicativo.|
|ScheduledEnqueueTimeUtc|cadeia de caracteres|Data e hora, em UTC, quando a mensagem será adicionada à fila.|
|Identificação de sessão|cadeia de caracteres|Identificador da sessão.|
|CorrelationId|cadeia de caracteres|Identificador de correlação.|
|TimeToLive|cadeia de caracteres|A duração, em escalas, que uma mensagem é válida. Inicia a duração de quando a mensagem é enviada ao barramento de serviço.|



### <a name="http-responses"></a>Respostas HTTP

Ações e disparadores anterior podem retornar um ou mais dos seguintes códigos de status HTTP:

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido.|
|padrão|Falha na operação.|

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
