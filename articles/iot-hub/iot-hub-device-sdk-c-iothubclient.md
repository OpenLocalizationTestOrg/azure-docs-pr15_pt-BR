<properties
    pageTitle="Dispositivo Azure IoT SDK para C - IoTHubClient | Microsoft Azure"
    description="Saiba mais sobre como usar a biblioteca de IoTHubClient no dispositivo IoT Azure SDK para C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivo do Microsoft Azure IoT SDK para C – Saiba mais sobre IoTHubClient

O [primeiro artigo](iot-hub-device-sdk-c-intro.md) desta série introduzido no **Microsoft Azure IoT dispositivo SDK para C**. Esse artigo explicado que existem duas camadas de arquiteturais no SDK. Na base é a biblioteca de **IoTHubClient** que gerencia a comunicação com IoT Hub diretamente. Há também a biblioteca de **serializador** que se baseia na parte superior para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais sobre a biblioteca de **IoTHubClient** .

O artigo anterior descrita como usar a biblioteca de **IoTHubClient** para enviar eventos para IoT Hub e receber mensagens. Este artigo estende essa discussão explicando como gerenciar com mais precisão *quando* você envia e recebe dados, apresentando as **APIs de nível inferior**. Explicaremos também como anexar propriedades para eventos (e recuperá-las de mensagens) usando a propriedade manipulação de recursos na biblioteca **IoTHubClient** . Por fim, forneceremos explicação adicional de várias maneiras diferentes para lidar com mensagens recebidas de IoT Hub.

O artigo conclui abordando alguns tópicos diversos, incluindo mais sobre as credenciais do dispositivo e como alterar o comportamento do **IoTHubClient** pelas opções de configuração.

Usaremos as amostras SDK **IoTHubClient** para explicar esses tópicos. Se você deseja acompanhar, consulte o **iothub\_cliente\_amostra\_http** e **iothub\_cliente\_amostra\_amqp** aplicativos que estão incluídos no dispositivo IoT Azure SDK para C. tudo descrito nas seções a seguir é demonstrou nesses exemplos.

Você pode encontrar o **dispositivo IoT Azure SDK para C** no repositório do [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) GitHub e exibir os detalhes da API na [referência da API do C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descrito a operação básica do **IotHubClient** dentro do contexto do **iothub\_cliente\_amostra\_amqp** aplicativo. Por exemplo, ele explicado como inicializar a biblioteca usando este código.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Ela também descrita como enviar eventos usando essa chamada de função.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descrita como receber mensagens registrando uma função de retorno de chamada.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrava como liberar recursos usando código como a seguir.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

No entanto, há funções de companion para cada uma dessas APIs:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Essas funções todos incluem "LL" no nome da API. Além disso, os parâmetros de cada uma dessas funções são idênticos aos seus equivalentes não-LL. No entanto, o comportamento dessas funções é diferente de uma maneira importante.

Quando você chamar **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criar um novo segmento que é executado em segundo plano. Neste segmento envia eventos e recebe mensagens, IoT no Hub do. Sem tal segmento é criado ao trabalhar com a APIs "LL". A criação de segmento de plano de fundo é uma conveniência para o desenvolvedor. Você não precisa se preocupar explicitamente enviar eventos e receber mensagens de IoT Hub – ele acontece automaticamente no plano de fundo. Em contraste, "LL" APIs oferece controle explícito sobre comunicação com IoT Hub, se necessário.

Para compreender isso melhor, vamos examinar um exemplo:

Quando você chamar **IoTHubClient\_SendEventAsync**, que você realmente estiver fazendo é colocar o evento em um buffer. O segmento de plano de fundo criado quando você chamar **IoTHubClient\_CreateFromConnectionString** continuamente monitora esse buffer e envia todos os dados que ela contém a IoT Hub. Isso acontece no plano de fundo ao mesmo tempo que o thread principal está realizando outro trabalho.

Da mesma forma, quando você registra uma função de retorno de chamada para mensagens usando **IoTHubClient\_SetMessageCallback**, você está solicitando o SDK ter o segmento de plano de fundo invocar a função de retorno de chamada quando uma mensagem for recebida, independentemente do thread principal.

"LL" APIs não cria um segmento de plano de fundo. Em vez disso, uma nova API deve ser chamada para enviar e receber dados do IoT Hub explicitamente. Isso é demonstrado no exemplo a seguir.

O **iothub\_cliente\_amostra\_http** aplicativo que está incluído no SDK demonstra as APIs de nível inferior. Nesse exemplo, podemos enviar eventos para IoT Hub com código como o seguinte:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem e a última linha envia o evento. No entanto, como mencionado anteriormente, "enviando" o evento significa que os dados simplesmente são colocados em um buffer. Nada é transmitido na rede quando chamamos **IoTHubClient\_LL\_SendEventAsync**. Em ordem para ingresso realmente os dados a serem IoT Hub, você deve chamar **IoTHubClient\_LL\_DoWork**, como no exemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código (da **iothub\_cliente\_amostra\_http** aplicativo) chama repetidamente **IoTHubClient\_LL\_DoWork**. Sempre **IoTHubClient\_LL\_DoWork** é chamado, ele envia alguns eventos do buffer IoT hub e recupera uma mensagem na fila está sendo enviada ao dispositivo. Último caso significa que se nós registrado uma função de retorno de chamada para mensagens, em seguida, o retorno de chamada é invocado (presumindo que as mensagens são enfileiradas). Nós seria ter registrado como uma função de retorno de chamada com o código como o seguinte:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo que **IoTHubClient\_LL\_DoWork** normalmente é chamado em um loop é que cada vez que é chamado, ele envia *alguns* buffer eventos IoT hub e recupera *a próxima* mensagem na fila do dispositivo. Não é garantia de cada chamada enviar buffer todos os eventos ou recuperar todas as mensagens na fila. Se você quiser enviar todos os eventos no buffer e, em seguida, prossiga com outro processamento que é possível substituir esse loop com código como o seguinte:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Esse código chama **IoTHubClient\_LL\_DoWork** até que todos os eventos no buffer foram enviados para IoT Hub. Observação que isso não também sugerem que todas as mensagens na fila foram recebidas. Parte o motivo para isso é que a verificação de mensagens "todos" não determinante como uma ação. O que acontece se você recuperar "todos" das mensagens, mas então outro é enviado para o dispositivo imediatamente após? Uma melhor maneira de lidar com que está com um tempo limite programado. Por exemplo, a função de retorno de chamada de mensagem foi redefinir um temporizador sempre que ele for chamado. Em seguida, você pode escrever lógica para continuar o processamento se, por exemplo, sem mensagens foram recebidas nos últimos *X* segundos.

Quando você está ingressing terminado eventos e receber mensagens, certifique-se de chamar a função correspondente para limpar os recursos.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um thread de plano de fundo e outro conjunto de APIs que faz a mesma coisa sem o segmento de plano de fundo. Muitos desenvolvedores podem preferir as APIs sem LL, mas as APIs de nível inferior são úteis quando o desenvolvedor quer controle explícito sobre transmissões de rede. Por exemplo, alguns dispositivos coletam dados ao longo do tempo e eventos de ingresso apenas intervalos especificados (por exemplo, uma vez hora ou uma vez ao dia). As APIs de nível inferior lhe dão a capacidade para controlar explicitamente ao enviar e receber dados do IoT Hub. Outros simplesmente irá preferem a simplicidade que fornecem as APIs de nível inferior. Tudo acontece o segmento principal em vez de alguns ocorra de trabalho no plano de fundo.

O modelo que você escolher, certifique-se sejam consistentes em quais APIs que você usar. Se você iniciar chamando **IoTHubClient\_LL\_CreateFromConnectionString**, certifique-se de que você só usar as APIs de nível inferior correspondentes para qualquer trabalho de acompanhamento:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

O oposto também é verdadeiro. Se você iniciar com **IoTHubClient\_CreateFromConnectionString**, em seguida, use as APIs sem LL para qualquer processamento adicional.

No dispositivo IoT Azure SDK para C, consulte o **iothub\_cliente\_amostra\_http** aplicativo para um exemplo completo das APIs de nível inferior. O **iothub\_cliente\_amostra\_amqp** aplicativo pode ser referenciado para um exemplo completo dos não - LL APIs.

## <a name="property-handling"></a>Manipulação de propriedade

Até o momento quando descritas enviar dados, podemos já foi consultando o corpo da mensagem. Por exemplo, considere este código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem para IoT Hub com o texto em "Olá". No entanto, IoT Hub também permite propriedades será anexado a cada mensagem. Propriedades são pares nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade à mensagem:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vamos começar chamando **IoTHubMessage\_propriedades** e passando a alça da nossa mensagem. O que voltamos é um **mapa\_tratar** referência que possibilita a começar a adicionar propriedades. O segundo é feito chamando **mapa\_AddOrUpdate**, que utiliza uma referência para um mapa\_ALÇA, o nome da propriedade e o valor da propriedade. Com essa API podemos adicionar tantas propriedades podemos gostar.

Quando o evento é lido da **Hubs de evento**, o destinatário pode enumerar as propriedades e recuperar seus valores correspondentes. Por exemplo, no .NET isso deve ser feito acessando a [coleção de propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, nós estiver anexando propriedades para um evento que enviamos IoT hub. Propriedades também podem ser anexadas mensagens recebidas de IoT Hub. Se quisermos recuperar propriedades de uma mensagem, podemos usar código como o seguinte em nossa função de retorno de chamada de mensagem:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada para **IoTHubMessage\_propriedades** retorna o **mapa\_tratar** referência. Podemos passar essa referência para **mapa\_GetInternals** para obter uma referência a uma matriz de pares nome/valor (bem como uma contagem das propriedades). Nesse momento é uma simples questão de enumerar as propriedades para acessar os valores que queremos.

Você não precisa usar propriedades em seu aplicativo. No entanto, se você precisa defini-las eventos ou recuperá-las de mensagens, a biblioteca de **IoTHubClient** torna mais fácil.

## <a name="message-handling"></a>Manipulação de mensagens

Conforme mencionado anteriormente, quando as mensagens chegam do IoT Hub biblioteca **IoTHubClient** responde chamando uma função de retorno de chamada registrado. Não há um parâmetro de retorno desta função que merece alguma explicação adicional. Aqui está um trecho da função retorno de chamada na **iothub\_cliente\_amostra\_http** exemplo de aplicativo:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que o tipo de retorno é **IOTHUBMESSAGE\_disposição\_resultado** e neste caso particular, podemos retornar **IOTHUBMESSAGE\_ACEITOS**. Há outros valores nós pode retornar a partir dessa função que alterar como a biblioteca de **IoTHubClient** reage ao retorno de chamada de mensagem. Aqui estão as opções.

-   **IOTHUBMESSAGE\_ACEITOS** – a mensagem foi processada com êxito. A biblioteca de **IoTHubClient** não irá chamar a função de retorno de chamada novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_rejeitado** – a mensagem não foi processada e não há nenhum desejo para fazê-lo no futuro. A biblioteca de **IoTHubClient** não deve chamar a função de retorno de chamada novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_ABANDONED** – a mensagem não foi processada com êxito, mas a biblioteca de **IoTHubClient** deve chamar a função de retorno de chamada novamente com a mesma mensagem.

Para os códigos de retorno duas primeiras, a biblioteca de **IoTHubClient** envia uma mensagem a IoT Hub indicando que a mensagem deve ser excluída da fila de dispositivo e não entregues novamente. O resultado é a mesma (a mensagem é excluída da fila de dispositivo), mas a mensagem foi aceitas ou rejeitada ainda estiver gravada.  Essa distinção de gravação é útil para remetentes da mensagem que podem ouvir comentários e descobrir se um dispositivo tem aceitas ou rejeitadas uma mensagem específica.

No último caso uma mensagem também será enviada para IoT Hub, mas indica que a mensagem deve ser entregue em novamente. Normalmente você vai abandonar uma mensagem se você encontrar algum erro, mas quer experimentar processar a mensagem novamente. Em contraste, rejeitar uma mensagem é apropriado quando você encontrar um erro irrecuperável (ou se você simplesmente decidir que não deseja processar a mensagem).

Em qualquer caso, lembre-se dos códigos de retorno diferentes para que você pode extrair o comportamento desejado da biblioteca do **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenciais do dispositivo alternativo

Conforme explicado anteriormente, a primeira coisa a fazer quando trabalhando com a biblioteca de **IoTHubClient** é obter um **IOTHUB\_cliente\_tratar** com uma chamada como as seguintes:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **IoTHubClient\_CreateFromConnectionString** são a cadeia de conexão de nosso dispositivo e um parâmetro que indica o protocolo que usamos para se comunicar com IoT Hub. A cadeia de conexão tem um formato que aparece da seguinte maneira:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Há quatro partes de informação essa cadeia de caracteres: nome do IoT Hub, sufixo IoT Hub, ID do dispositivo e chave de acesso compartilhado. Obter o nome de domínio totalmente qualificado (FQDN) de um hub IoT quando você cria sua instância de hub IoT no portal do Azure — assim você obtém o nome do hub de IoT (a primeira parte do FQDN) e o sufixo do hub IoT (o restante do FQDN). Você obtém a identificação do dispositivo e a tecla de acesso compartilhado, quando você registrar seu dispositivo com IoT Hub (conforme descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** fornece uma maneira de inicializar a biblioteca. Se preferir, você pode criar um novo **IOTHUB\_cliente\_tratar** usando esses parâmetros individuais em vez da cadeia de conexão. Isso é feito com o seguinte código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isto faz a mesma coisa que **IoTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que você usaria **IoTHubClient\_CreateFromConnectionString** em vez desse método mais detalhado de inicialização. Tenha em mente, entretanto, que ao registrar um dispositivo no IoT Hub o que você obtém é uma identificação de dispositivo e a chave de dispositivo (não uma cadeia de conexão). A ferramenta SDK do **Gerenciador de dispositivos** introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) usa bibliotecas no **serviço do Azure IoT SDK** para criar a cadeia de conexão da ID do dispositivo, chave do dispositivo e o nome de host IoT Hub. Para chamar **IoTHubClient\_LL\_criar** pode ser preferível porque poupa a etapa de gerar uma cadeia de conexão. Use o método é conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até aqui tudo descrito sobre o funcionamento da biblioteca **IoTHubClient** reflete seu comportamento padrão. No entanto, há algumas opções que você pode definir para alterar como funciona a biblioteca. Isso é feito utilizando o **IoTHubClient\_LL\_SetOption** API. Considere este exemplo:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Há algumas opções que são comumente usados:

-   **SetBatching** (bool) – se **verdadeiro**, então dados enviados a IoT Hub for enviado em lotes. Se **false**, em seguida, as mensagens são enviadas individualmente. O padrão é **Falso**. Observe que a opção de **SetBatching** aplica-se somente para o protocolo HTTP e não para os protocolos MQTT ou AMQP.

-   **Tempo limite** (não assinado int) – esse valor é representado em milissegundos. Se enviar uma solicitação HTTP ou receber uma resposta demora mais do que desta vez, então a conexão atinge o tempo limite.

A opção de lote é importante. Por padrão, os eventos de ingresses biblioteca individualmente (um único evento é tudo o que você passa para **IoTHubClient\_LL\_SendEventAsync**). Se a opção lote for **verdadeira**, a biblioteca coleta quantos eventos como ele pode de buffer (até o tamanho máximo de mensagem que IoT Hub aceitará).  O lote de evento é enviado à IoT Hub em uma única chamada HTTP (os eventos individuais estão agrupados em uma matriz JSON). Habilitar o processamento em lotes normalmente resulta em ganhos de desempenho grande desde que você está reduzindo ida e volta de rede. Ele também reduz significativamente a largura de banda desde que você está enviando um conjunto de cabeçalhos HTTP com um lote de evento em vez de um conjunto de cabeçalhos para cada evento individual. A menos que você tenha um motivo específico para fazer o contrário, normalmente você desejará ativar em lotes.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreve detalhadamente o comportamento da biblioteca **IoTHubClient** encontrado no **dispositivo IoT Azure SDK para C**. Com essas informações, você deve ter uma boa compreensão dos recursos da biblioteca **IoTHubClient** . O [próximo artigo](iot-hub-device-sdk-c-serializer.md) fornece detalhes semelhantes na biblioteca de **serializador** .

Para saber mais sobre como desenvolver para IoT Hub, consulte os [IoT Hub SDKs][lnk-sdks].

Para explorar os recursos do IoT Hub, consulte:

- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
