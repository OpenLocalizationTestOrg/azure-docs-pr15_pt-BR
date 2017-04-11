<properties
 pageTitle="Guia Desenvolvedor - mensagens | Microsoft Azure"
 description="Azure guia do desenvolvedor IoT Hub - dispositivo na nuvem e mensagens de nuvem para dispositivo"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="send-and-receive-messages-with-iot-hub"></a>Enviar e receber mensagens com IoT Hub

## <a name="overview"></a>Visão geral

Hub de IoT fornece os seguintes primitivos mensagens para se comunicar com um dispositivo:

- [Dispositivo à nuvem] [ lnk-d2c] de um dispositivo ao aplicativo back-end.
- [Nuvem para dispositivo] [ lnk-c2d] de um aplicativo back-end (*serviço* ou *nuvem*).

Propriedades de núcleo da funcionalidade de mensagens do IoT Hub são a confiabilidade e durabilidade de mensagens. Essas propriedades permitem resistência a conectividade intermitente no lado do dispositivo e carregar picos no processamento do lado de nuvem de eventos. Hub de IoT implementa *pelo menos uma vez* garantias de entrega para mensagens dispositivo na nuvem e nuvem para dispositivo.

Hub de IoT suporta vários [protocolos voltados para o dispositivo] [ lnk-protocols] (como MQTT, AMQP e HTTP). Para dar suporte a interoperabilidade perfeita entre protocolos, IoT Hub define um [formato de mensagem comum] [ lnk-message-format] que oferecem suporte a todos os protocolos voltados para o dispositivo.

Hub de IoT expõe um [ponto de extremidade compatível com o evento Hub] [ lnk-compatible-endpoint] para habilitar aplicativos de back-end ler as mensagens de dispositivo à nuvem recebidas pelo hub.

### <a name="when-to-use"></a>Quando usar

Sistema de mensagens é dos principais recursos do IoT Hub. Usá-lo sempre que precisar enviar mensagens do seu dispositivo para seu back-end ou enviar mensagens de back-end para um dispositivo.

Para uma comparação dos serviços IoT Hub e Hubs de evento, consulte [comparação de IoT Hub e Hubs de evento][lnk-compare].

## <a name="device-to-cloud-messages"></a>Mensagens de dispositivo à nuvem

Enviar mensagens de dispositivo à nuvem por meio de um ponto de extremidade do dispositivo voltados (**/devices/ {deviceId} / mensagens/eventos**). Seu serviço de back-end recebe mensagens de dispositivo à nuvem por meio de um ponto de extremidade do serviço voltados (**/messages/events**) que seja compatível com [Hubs de evento][lnk-event-hubs]. Portanto, você pode usar o padrão de [integração de Hubs de evento e SDKs] [ lnk-compatible-endpoint] para receber mensagens de dispositivo na nuvem.

Hub de IoT implementa dispositivo à nuvem de mensagens de maneira semelhante ao [Evento Hubs][lnk-event-hubs]. Mensagens de dispositivo à nuvem do IoT Hub são mais Hubs de evento *eventos* que [Barramento de serviço] [ lnk-servicebus] *mensagens*.

Essa implementação tem as seguintes implicações:

* Da mesma forma eventos de Hubs de evento, mensagens de dispositivo à nuvem são durável e mantidas em um hub IoT por até sete dias (consulte [Opções de configuração de dispositivo à nuvem][lnk-d2c-configuration]).
* Mensagens de dispositivo à nuvem são particionadas por um conjunto fixo de partições que é definido no momento da criação (consulte [Opções de configuração de dispositivo à nuvem][lnk-d2c-configuration]).
* Analogamente para Hubs de evento, clientes ler mensagens de dispositivo à nuvem devem tratar partições e ponto de verificação. Consulte [Hubs de evento - consumindo eventos][lnk-event-hubs-consuming-events].
* Como eventos de Hubs de evento, mensagens de dispositivo à nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes otimizar envia. Lotes podem ter no máximo 256 KB e no máximo 500 mensagens.

No entanto, há algumas importantes diferenças entre opção IoT Hub de mensagens de dispositivo na nuvem e Hubs de evento:

* Como explicado em [controlar o acesso às IoT Hub] [ lnk-devguide-security] seção, IoT Hub permite por dispositivo autenticação e controle de acesso.
* Hub de IoT permite milhões de dispositivos conectados simultaneamente (consulte [cotas e a otimização][lnk-quotas]), enquanto Hubs de evento está limitado a 5000 conexões de AMQP por namespace.
* Hub de IoT não permite aleatório partição usando um **PartitionKey**. Mensagens de dispositivo à nuvem são particionadas com base na sua origem **deviceId**.
* Dimensionamento IoT Hub é um pouco diferente de dimensionamento Hubs de evento. Para obter mais informações, consulte [Dimensionamento IoT Hub][lnk-guidance-scale].

> [AZURE.NOTE] Você não pode substituir IoT Hub para Hubs de evento em todos os cenários. Por exemplo, em alguns cálculos de processamento de evento, ele pode ser necessário reparticionar eventos relacionadas com uma propriedade diferente ou campo antes de analisar os fluxos de dados. Neste cenário, você poderia usar um Hub de evento para desassociar duas partes do fluxo de pipeline de processamento. Para obter mais informações, consulte *partições* na [Visão geral de Hubs de evento do Azure][lnk-eventhub-partitions].

Para obter detalhes sobre como usar o dispositivo para nuvem de mensagens, consulte [IoT Hub APIs e SDKs][lnk-sdks].

> [AZURE.NOTE] Ao usar HTTP para enviar mensagens de dispositivo na nuvem, valores e nomes de propriedade só podem conter caracteres alfanuméricos ASCII, além de ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Tráfego de telemetria não

Muitas vezes, além de pontos de dados de telemetria, dispositivos também enviar mensagens e as solicitações que exigem execução e manipulação da camada de lógica de negócios de aplicativos. Por exemplo, alertas críticos que devem disparar uma ação específica no back-end ou respostas de dispositivo para comandos enviados do back-end.

Para obter mais informações sobre a melhor maneira de processar este tipo de mensagem, consulte o [Tutorial: como processar mensagens de dispositivo à nuvem IoT Hub] [ lnk-d2c-tutorial] tutorial.

### <a name="device-to-cloud-configuration-options"></a>Opções de configuração de dispositivo na nuvem

Um hub IoT expõe as seguintes propriedades para habilitar controlar mensagens de dispositivo na nuvem.

* **Contagem de partição**. Defina esta propriedade na criação para definir o número de partições para inclusão de evento do dispositivo na nuvem.
* **Tempo de retenção**. Esta propriedade especifica o tempo de retenção para mensagens de dispositivo na nuvem. O padrão é um dia, mas ele pode ser aumentado para sete dias.

Além disso, analogamente para Hubs de evento, IoT Hub permite que você gerencie grupos de consumidor em dispositivo-para-nuvem recebem ponto de extremidade.

Você pode modificar todas essas propriedades, seja programaticamente por meio do [provedor de recursos do IoT Hub APIs REST][lnk-resource-provider-apis], ou usando o [portal do Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Antifalsificação propriedades

Para evitar a falsificação em mensagens de dispositivo na nuvem, IoT Hub de dispositivo carimbos de todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Os dois primeiros contêm as **deviceId** e **generationId** do dispositivo de origem, acordo com as [Propriedades de identidade de dispositivo][lnk-device-properties].

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado, com as seguintes propriedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Mensagens de nuvem para dispositivo

Enviar mensagens de nuvem para dispositivo por meio de um ponto de extremidade do serviço voltados (**/messages/devicebound**). Um dispositivo recebe-las por meio de um ponto de extremidade do dispositivo específico (**/devices/ {deviceId} / mensagens/devicebound**).

Cada mensagem de nuvem para dispositivo destina-se a um único dispositivo, definindo a propriedade **to** para **/devices/ {deviceId} / mensagens/devicebound**.

>[AZURE.IMPORTANT] Cada fila de dispositivo contém no máximo 50 mensagens de nuvem para dispositivo. Tentando enviar mais mensagens para os mesmos resultados de dispositivo em um erro.

> [AZURE.NOTE] Quando você envia mensagens de nuvem para dispositivo, valores e nomes de propriedade só podem conter caracteres alfanuméricos ASCII, além de ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Ciclo de vida de mensagem

Para garantir a entrega de mensagens pelo menos uma vez, o Hub de IoT persistir mensagens de nuvem para dispositivo em filas de cada dispositivo. Dispositivos explicitamente devem reconhecer a *conclusão* de IoT Hub para removê-los da fila. Isso garante resiliência contra falhas de dispositivo e de conectividade.

O diagrama a seguir mostra o gráfico de estado de ciclo de vida para uma mensagem de nuvem para dispositivo.

![Ciclo de vida de mensagem de nuvem para dispositivo][img-lifecycle]

Quando o serviço envia uma mensagem, ela é considerada *enfileirados*. Quando um dispositivo deseja *receber* uma mensagem, IoT Hub *bloqueios* a mensagem (define o estado para **invisível**) permitindo que outros threads no mesmo dispositivo para começar a receber outras mensagens. Quando um segmento do dispositivo conclui o processamento de uma mensagem, ele notifica IoT Hub *Concluindo* a mensagem.

Um dispositivo também pode:

- *Rejeitar* a mensagem, o que faz com que o Hub de IoT defini-lo para o estado de **Deadlettered** . Observação: dispositivos que se conectam com MQTT não é possível rejeitar mensagens de C2D.
- A mensagem, o que faz com que o Hub de IoT para colocar a mensagem de volta na fila, com o estado *abandonar* definido como **enfileirados**.

Um segmento pode falhar processar uma mensagem sem notificar IoT Hub. Nesse caso, mensagens automaticamente a transição do estado **invisível** ao estado **enfileirados** após um *tempo limite de visibilidade (ou bloquear)*. O valor padrão dessa expiração é um minuto.

Uma mensagem pode fazer a transição entre o **enfileirados** e **invisíveis** estados para, no máximo, o número de vezes especificado na propriedade de **contagem de entrega máx** em IoT Hub. Após o número de transições, o Hub de IoT define o estado da mensagem para **Deadlettered**. Da mesma forma, o Hub de IoT define o estado de uma mensagem para **Deadlettered** após o tempo de expiração (consulte [tempo de vida][lnk-ttl]).

Para um tutorial em mensagens de nuvem para dispositivo, consulte [Tutorial: como enviar mensagens de nuvem para dispositivo com IoT Hub][lnk-c2d-tutorial]. Para tópicos de referência sobre como diferentes APIs e SDKs expõem a funcionalidade de nuvem para dispositivo, consulte [IoT Hub APIs e SDKs][lnk-sdks].

> [AZURE.NOTE] Normalmente, mensagens de nuvem para dispositivo concluir sempre que a perda da mensagem não afetará a lógica do aplicativo. Por exemplo, o conteúdo da mensagem foi com êxito persistiu no armazenamento local ou uma operação foi executada com êxito. A mensagem também poderia ser Carregando informações temporárias, cuja perda não causam impacto a funcionalidade do aplicativo. Às vezes, tarefas de execução demorada, você pode completar a mensagem de nuvem para dispositivo após persistir a descrição da tarefa no armazenamento local. Em seguida, você pode notificar back-end aplicativo com uma ou mais mensagens de dispositivo à nuvem em vários estágios de progresso da tarefa.

### <a name="message-expiration-time-to-live"></a>Expiração da mensagem (hora Live)

Cada mensagem de nuvem para dispositivo tem uma hora de expiração. Nesse momento é definido pelo serviço (na propriedade **ExpiryTimeUtc** ) ou pelo IoT Hub usando padrão *vida útil* especificado como uma propriedade IoT Hub. Consulte [Opções de configuração de nuvem para dispositivo][lnk-c2d-configuration].

> [AZURE.NOTE] Uma maneira comum para tirar proveito de expiração da mensagem e evitar enviando mensagens para dispositivos desconectados, é definir hora abreviada Live valores. Essa abordagem atinge o mesmo resultado como manter o estado de conexão do dispositivo, enquanto estiver sendo mais eficiente. Quando você solicita confirmações de mensagem, IoT Hub notifica os dispositivos que são capazes de receber mensagens e os dispositivos que não estão online ou falharam.

### <a name="message-feedback"></a>Comentários de mensagem

Quando você envia uma mensagem de nuvem para dispositivo, o serviço pode solicitar a entrega de comentários por mensagem sobre o estado final da mensagem.

- Se você definir a propriedade de **Ack** em **positivos**, o Hub de IoT gera uma mensagem de comentários se e somente se, a mensagem de nuvem para dispositivo alcançar o estado **concluído** .
- Se você definir a propriedade **Ack** para **negativo**, o Hub de IoT gera uma mensagem de comentários, se e somente se, a mensagem de nuvem para dispositivo atinge o estado de **Deadlettered** .
- Se você definir a propriedade **Ack** **completo**, o Hub de IoT gera uma mensagem de comentários em ambos os casos.

> [AZURE.NOTE] Se **Ack** estiver **cheia**, e você não recebe uma mensagem de feedback, significa que a mensagem de comentários expirou. O serviço não é possível saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que ele pode processar os comentários antes que ela expire. O tempo máximo de expiração é dois dias, que permite bastante tempo para obter o serviço em execução novamente se ocorrer uma falha.

Como explicado em [pontos de extremidade][lnk-endpoints], IoT Hub oferece comentários por meio de um ponto de extremidade do serviço voltados (**/messages/servicebound/feedback**) como mensagens. A semântica para receber comentários são as mesmas mensagens de nuvem para dispositivo e ter o mesmo [ciclo de vida de mensagem][lnk-lifecycle]. Sempre que possível, comentários de mensagem é processado em lotes em uma única mensagem, com o seguinte formato:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTime | Carimbo de hora que indica quando a mensagem foi criada. |
| ID de usuário | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz serializado JSON dos registros, cada uma com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Carimbo de hora que indica quando o resultado da mensagem aconteceu. Por exemplo, o dispositivo concluída ou a mensagem expirou. |
| OriginalMessageId | **MessageId** da mensagem de nuvem para dispositivo ao qual pertence esta informação de comentários. |
| StatusCode | Inteiros necessários. Usado em mensagens de comentários geradas pelo IoT Hub. <br/> 0 = sucesso <br/> 1 = mensagem expirou <br/> 2 = contagem de entrega máximo excedida <br/> 3 = mensagem rejeitada |
| Descrição | Valores de cadeia de caracteres para **StatusCode**. |
| DeviceId | **DeviceId** do dispositivo de destino da mensagem de nuvem para dispositivo ao qual pertence este pedaço de comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem de nuvem para dispositivo ao qual pertence este pedaço de comentários. |


>[AZURE.IMPORTANT] O serviço deve especificar **MessageId** para a mensagem de nuvem para dispositivo sejam capazes de correlação seus comentários com a mensagem original.

O exemplo a seguir mostra o corpo de uma mensagem de comentários.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opções de configuração de nuvem para dispositivo

Cada hub IoT expõe as seguintes opções de configuração para mensagens de nuvem para dispositivo.

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL padrão para mensagens de nuvem para dispositivo. | Intervalo de ISO_8601 até 2D (mínimo 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount | Contagem de entrega máximo para filas de nuvem para dispositivo por dispositivo. | 1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601 | Retenção de mensagens de serviço ligadas comentários. | Intervalo de ISO_8601 até 2D (mínimo 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount | Contagem de entrega máximo para fila de comentários. | 1 a 100. Padrão: 100. |

Para obter mais informações, consulte [criar IoT hubs][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Ler mensagens de dispositivo à nuvem

Hub de IoT expõe um ponto de extremidade para os serviços de back-end ler as mensagens de dispositivo à nuvem recebidas pelo seu hub. O ponto de extremidade é evento Hub compatível, que permite que você use qualquer um dos mecanismos de serviço Hubs de evento oferece suporte para ler mensagens.

Quando você usa o [SDK do barramento de serviço do Azure para .NET] [ lnk-servicebus-sdk] ou o [Evento Hubs - evento processador Host][lnk-eventprocessorhost], você pode usar as cadeias de caracteres de conexão do IoT Hub com as permissões corretas. Use **mensagens/eventos** como o nome do Hub de evento.

Quando você usa SDKs (ou integrações de produto) que estejam cientes do IoT Hub, você deve recuperar um ponto de extremidade compatível com o evento Hub e um nome compatível com o evento Hub das configurações da IoT Hub no [portal do Azure][lnk-management-portal]:

1. Na lâmina IoT hub, clique em **sistema de mensagens**.
2. Na seção **configurações de dispositivo na nuvem** , você pode encontrar os valores seguintes: **ponto de extremidade compatível com o evento Hub**, **compatível com o evento Hub nome**e **partições**.

    ![Configurações de dispositivo à nuvem][img-eventhubcompatible]

> [AZURE.NOTE] Se o SDK requer um valor de **nome do host** ou **Namespace** , remova o esquema de **ponto de extremidade compatível com o evento Hub**. Por exemplo, se seu ponto de extremidade compatível com o evento Hub é **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **nome do host** seria **iothub-ns-myiothub-1234.servicebus.windows.net**e o **Namespace** seria **iothub-ns-myiothub-1234**.

Você pode usar qualquer política de segurança de acesso compartilhado que tenha as permissões **ServiceConnect** para se conectar ao Hub do evento especificado.

Se você precisar construir uma cadeia de caracteres de conexão do Hub de evento usando as informações anteriores, use o seguinte padrão:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

A seguir é uma lista de SDK e as integrações que você pode usar com pontos de extremidade compatível com o evento Hub que IoT Hub expõe:

* [Cliente Java Hubs de evento](https://github.com/hdinsight/eventhubs-client)
* [Tempestade Apache spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [origem de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre a troca de mensagens com IoT Hub.

## <a name="message-format"></a>Formato da mensagem

As mensagens do IoT Hub compreendem:

* Um conjunto de *Propriedades do sistema*. Propriedades que IoT Hub interprete ou define. Esse conjunto é predeterminado.
* Um conjunto de *Propriedades do aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acesse, sem precisar desserializar o corpo da mensagem. Hub de IoT nunca modifica essas propriedades.
* Um corpo binário opaco.

Para obter mais informações sobre como a mensagem é codificada em protocolos diferentes, consulte [IoT Hub APIs e SDKs][lnk-sdks].

A tabela a seguir lista o conjunto de propriedades do sistema em mensagens de IoT Hub.

| Propriedade | Descrição |
| -------- | ----------- |
| MessageId | Um identificador definido pelo usuário e para a mensagem, usada para padrões de solicitação-resposta. Formato: Uma diferencia maiusculas de minúsculas cadeia (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência | Um número (exclusivo por dispositivo fila) atribuído pelo IoT Hub para cada mensagem de nuvem para dispositivo. |
| Para | Um destino especificado na [Nuvem para dispositivo] [ lnk-c2d] mensagens. |
| ExpiryTimeUtc | Data e hora de expiração da mensagem. |
| EnqueuedTime | Data e hora que a mensagem foi recebida pelo IoT Hub. |
| CorrelationId | Uma propriedade de cadeia de caracteres em uma mensagem de resposta que geralmente contém o MessageId da solicitação, em padrões de solicitação-resposta. |
| ID de usuário | Uma ID usada para especificar a origem de mensagens. Quando mensagens são geradas pelo IoT Hub, ela é definida como `{iot hub name}`. |
| ACK | Um gerador de mensagem de comentários. Esta propriedade é usada em mensagens de nuvem para dispositivo solicitar IoT Hub para gerar mensagens de comentários como resultado o consumo da mensagem pelo dispositivo. Valores possíveis: **Nenhuma** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: receber uma mensagem de comentários se a mensagem foi concluída, **negativo**: receber uma mensagem de comentários se a mensagem expirou (ou contagem de entrega máximo foi atingida) sem sendo concluída pelo dispositivo ou **completo**: negativos. Para obter mais informações, consulte [comentários de mensagem][lnk-feedback]. |
| ConnectionDeviceId | Uma identificação definida pelo IoT Hub em mensagens de dispositivo na nuvem. Ele contém o **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId | Uma identificação definida pelo IoT Hub em mensagens de dispositivo na nuvem. Ele contém o **generationId** (acordo com as [Propriedades de identidade de dispositivo][lnk-device-properties]) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod | Um método de autenticação definido pelo IoT Hub em mensagens de dispositivo na nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo enviando a mensagem. Para obter mais informações, consulte [dispositivo para nuvem antifalsificação][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocolos de comunicação

Hub de IoT permite que os dispositivos usar [MQTT][lnk-mqtt], MQTT sobre WebSocket, [AMQP][lnk-amqp], AMQP WebSocket e HTTP sobre protocolos para comunicações do lado do dispositivo. A tabela a seguir fornece as recomendações de alto nível para sua escolha do protocolo:

| Protocolo | Quando você deve escolher este protocolo |
| -------- | ------------------------------------ |
| MQTT <br> MQTT sobre WebSocket     | Usar em todos os dispositivos que não exigem para conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) sobre a mesma conexão TLS. |
| AMQP <br> AMQP sobre WebSocket    | Use no campo e nuvem gateways para tirar proveito de conexão multiplexação em dispositivos. |
| HTTP    | Use para dispositivos que não oferecem suporte a outros protocolos. |

Considere os seguintes pontos quando você escolhe o protocolo para comunicações do lado do dispositivo:

* **Padrão de nuvem para dispositivo**. HTTP não tem uma maneira eficiente para implementar o envio de servidor. Assim, quando você estiver usando HTTP, dispositivos poll IoT Hub para mensagens de nuvem para dispositivo. Essa abordagem é ineficaz para o dispositivo e o Hub de IoT. Em diretrizes HTTP atuais, cada dispositivo deve pesquisar mensagens de cada 25 minutos ou mais. Por outro lado, MQTT AMQP suporte e envio do servidor quando receber mensagens de nuvem para dispositivo. Eles permitem coloca imediata de mensagens do IoT Hub no dispositivo. Se a latência de entrega é importante, MQTT ou AMQP são os protocolos melhores usar. Para dispositivos raramente conectados, HTTP funciona bem.
* **Os gateways de campo**. Ao usar MQTT e HTTP, não é possível conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) usando a mesma conexão TLS. Portanto, para [cenários de gateway do campo][lnk-azure-gateway-guidance], esses protocolos são com qualidade inferior, pois eles requerem uma conexão TLS entre o gateway de campo e IoT Hub para cada dispositivo conectado ao campo gateway.
* **Dispositivos de recursos baixos**. As bibliotecas MQTT e HTTP têm um espaço menor que as bibliotecas AMQP. Assim, se o dispositivo tiver recursos (por exemplo, menor que 1 MB de RAM) limitados, esses protocolos podem ser a implementação de protocolo única disponível.
* **Rede transversal**. O protocolo AMQP padrão usa a porta 5671, enquanto MQTT ouve na porta 8883, que pode causar problemas em redes que são fechadas para protocolos não HTTP. MQTT sobre WebSocket, AMQP WebSocket e HTTP sobre estão disponíveis para serem usados neste cenário.
* **Tamanho da carga**. MQTT e AMQP são protocolos binários, que resultam em cargas mais compactas que HTTP.

> [AZURE.NOTE] Ao usar HTTP, cada dispositivo deve pesquisar mensagens de nuvem para dispositivo cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável para pesquisar mais de cada 25 minutos.

## <a name="port-numbers"></a>Números de porta

Dispositivos podem se comunicar com IoT Hub no Azure usando vários protocolos. Normalmente, a opção de protocolo é orientada por requisitos específicos da solução. A tabela a seguir lista as portas de saída que devem estar abertas para um dispositivo sejam capazes de usar um protocolo específico:

| Protocolo | Portas |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT sobre WebSocket | 443    |
| AMQP     | 5671    |
| AMQP sobre WebSocket | 443    |
| HTTP     | 443     |
| LWM2M (gerenciamento de dispositivo) | 5684 |

Quando você tiver criado um hub IoT em uma região Azure, o hub mantém o mesmo endereço IP para a vida útil do hub. No entanto, para manter a qualidade do serviço, se Microsoft move o hub de IoT para uma unidade de escala diferente, em seguida, ele é atribuído um novo endereço IP.

## <a name="notes-on-mqtt-support"></a>Observações sobre o suporte MQTT

Hub de IoT implementa o protocolo de v 3.1.1 MQTT com as seguintes limitações e comportamento específico:

  * **Não há suporte para QoS 2**. Quando um cliente do dispositivo publica uma mensagem com **QoS 2**, o Hub de IoT fecha a conexão de rede. Quando um cliente do dispositivo assinado por um tópico com **QoS 2**, o Hub de IoT concede nível máximo QoS 1 no pacote **SUBACK** .
  * **Não se mantiver reter mensagens**. Se um cliente do dispositivo publica uma mensagem com o sinalizador de RETER definido como 1, IoT Hub adiciona o **x-opt-reter** propriedade application a mensagem. Nesse caso, IoT Hub não se mantiver a mensagem reter, mas em vez disso, passa para o aplicativo de back-end.

Para obter mais informações, consulte [IoT Hub MQTT suporte][lnk-devguide-mqtt].

Como uma consideração final, você deve examinar o [gateway de protocolo do Azure IoT] [ lnk-azure-protocol-gateway] que permite que você implante um gateway de protocolo personalizado de alto desempenho que interfaces diretamente com IoT Hub. O gateway de protocolo Azure IoT permite personalizar o protocolo de dispositivo para acomodar implantações de MQTT brownfield ou outros protocolos personalizados. Essa abordagem exigem, no entanto, que você execute e opera um gateway de protocolo personalizado.

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a enviar e receber mensagens com IoT Hub, você pode estar interessado nos seguintes tópicos de guia desenvolvedor:

- [Carregar arquivos em um dispositivo][lnk-devguide-upload]
- [Gerenciar identidades de dispositivo no IoT Hub][lnk-devguide-identities]
- [Controlar o acesso às IoT Hub][lnk-devguide-security]
- [Usar Gêmeos de dispositivo para sincronizar o estado e configurações][lnk-devguide-device-twins]
- [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado nos seguintes tutoriais do IoT Hub:

- [Introdução ao Azure IoT Hub][lnk-getstarted-tutorial]
- [Como enviar mensagens de nuvem para dispositivo com IoT Hub][lnk-c2d-tutorial]
- [Como processar mensagens de dispositivo à nuvem IoT Hub][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
