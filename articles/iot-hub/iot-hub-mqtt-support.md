<properties
 pageTitle="Suporte de IoT Hub MQTT | Microsoft Azure"
 description="Descrição do MQTT suporte no nível do hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Suporte de IoT Hub MQTT

Hub de IoT permite que os dispositivos para se comunicar com os pontos de extremidade de dispositivo de IoT Hub usando o [v 3.1.1 MQTT] [ lnk-mqtt-org] protocolo em porta 8883 ou v 3.1.1 MQTT sobre protocolo WebSocket na porta 443. Hub de IoT requer todas as comunicações de dispositivo esteja protegido usando SSL/TLS (portanto, IoT Hub não oferece suporte a conexões não seguras pela porta 1883).

## <a name="connecting-to-iot-hub"></a>Conectando ao IoT Hub

Um dispositivo pode usar o protocolo MQTT para se conectar a um hub de IoT tanto usando as bibliotecas nos [SDKs do Microsoft Azure IoT] [ lnk-device-sdks] ou usando o MQTT protocolo diretamente.

## <a name="using-the-device-client-sdks"></a>Usando o cliente de dispositivo SDKs

[Cliente do dispositivo SDKs] [ lnk-device-sdks] que suportam o protocolo MQTT estão disponíveis para Java, Node, C, c# e Python. Cliente do dispositivo SDKs usar a cadeia de conexão do IoT Hub padrão para estabelecer uma conexão a um hub IoT. Para usar o protocolo MQTT, configure o parâmetro de protocolo do cliente para **MQTT**. Por padrão, o cliente de dispositivo SDKs conectem a um IoT Hub com o **CleanSession** o sinalizador será definido como **0** e use **QoS 1** para troca de mensagens com o hub de IoT.

Quando um dispositivo estiver conectado a um hub IoT, o cliente de dispositivo SDKs fornecem métodos que habilitar o dispositivo para enviar mensagens para e receber mensagens de um hub IoT.

A tabela a seguir contém links para exemplos de código para cada idioma com suporte e especifica o parâmetro para usar para estabelecer uma conexão a IoT Hub usando o protocolo MQTT.

| Idioma                   | Parâmetro de protocolo        |
| -------------------------- | ------------------------- |
| [Node][lnk-sample-node] | Azure-iot-dispositivo-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar um aplicativo de dispositivo de AMQP para MQTT
Se você estiver usando o [cliente do dispositivo SDKs][lnk-device-sdks], trocando usando AMQP para MQTT requer alterando o parâmetro de protocolo na inicialização cliente conforme mencionado acima.

Ao fazer isso, verifique os seguintes itens:

* AMQP retorna erros de várias condições, enquanto MQTT encerra a conexão. Como resultado sua lógica de manipulação de exceção pode exigir algumas alterações.
* MQTT não oferece suporte as operações de *Rejeitar* quando receber [mensagens de C2D][lnk-messaging]. Se precisar de back-end receber uma resposta do aplicativo do dispositivo, considere o uso de [métodos diretos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Usando o protocolo MQTT diretamente

Se um dispositivo não é possível usar o cliente de dispositivo SDKs, ele ainda poderá se conectar a pontos de extremidade do dispositivo público usando o protocolo MQTT. No pacote **Conectar** o dispositivo deve usar os seguintes valores:

- Para o campo **ClientId** , use o **deviceId**. 
- Para o campo de **nome de usuário** , use `{iothubhostname}/{device_id}`, onde {iothubhostname} é o CName completo do hub IoT.

    Por exemplo, se o nome do seu hub IoT é **contoso.azure-devices.net** e o nome do seu dispositivo é **MyDevice01**, o campo de **nome de usuário** completo deve conter `contoso.azure-devices.net/MyDevice01`.

- Para o campo de **senha** , use um token SAS. O formato do token SAS é a mesma protocolos HTTP tanto o AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Para obter mais informações sobre como gerar tokens SAS, consulte a seção de dispositivo usando IoT Hub de [tokens]de segurança[lnk-sas-tokens].
    
    Ao testar, você também pode usar o [Explorador de dispositivo] [ lnk-device-explorer] ferramenta para gerar rapidamente um token SAS que você pode copiar e colar no seu próprio código:
    
    1. Vá para a guia de **gerenciamento** de dispositivo Explorer.
    2. Clique em **SAS Token** (canto superior direito).
    3. Em **SASTokenForm**, selecione seu dispositivo no **DeviceID** lista suspensa. Defina seu **TTL**.
    4. Clique em **Gerar** para criar o token.
    
    O token SAS gerado tem essa estrutura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    A parte desse token a ser usado como o campo de **senha** para se conectar usando MQTT:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para MQTT conectar e desconectar pacotes, IoT Hub emite um evento no canal de **Monitoramento de operações** com informações adicionais que podem ajudar você a solucionar problemas de conectividade.

### <a name="sending-messages-to-iot-hub"></a>Enviando mensagens a IoT Hub

Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens para IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **Nome de tópico**. O `{property_bag}` elemento permite que o dispositivo para enviar mensagens com propriedades adicionais em um formato codificada como url. Por exemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Isso `{property_bag}` elemento usa a mesma codificação como cadeias de caracteres de consulta no protocolo HTTP.

Também pode usar o aplicativo de cliente do dispositivo `devices/{device_id}/messages/events/{property_bag}` como a **será o nome do tópico** para definir *será mensagens* sejam encaminhadas como uma mensagem de telemetria.

Hub de IoT não dá suporte a mensagens de QoS 2. Se um cliente do dispositivo publica uma mensagem com **QoS 2**, o Hub de IoT fecha a conexão de rede.
Hub de IoT não se mantiver reter mensagens. Se um dispositivo envia uma mensagem com o sinalizador de **RETER** definido como 1, IoT Hub adiciona o **x-opt-reter** propriedade application a mensagem. Nesse caso, em vez de manter a mensagem reter, IoT Hub passa para o aplicativo de back-end.

### <a name="receiving-messages"></a>Receber mensagens

Para receber mensagens de IoT Hub, um dispositivo deve assinar usando `devices/{device_id}/messages/devicebound/#` como um **Filtro de tópico**. O curinga de vários nível **#** no filtro tópico só é usado para permitir que o dispositivo receber propriedades adicionais no nome do tópico. Hub de IoT não permite o uso da **#** ou **?** caracteres curinga para filtragem de subtópicos. Como o Hub de IoT não é um corretor de mensagens pub-sub finalidade geral, ele suporta apenas os nomes de tópico documentadas e filtros de tópico.

Por favor, observe que o dispositivo não receberá quaisquer mensagens IoT no Hub do, antes que ela tem inscreveu com êxito seu dispositivo específico ponto de extremidade, representado pela `devices/{device_id}/messages/devicebound/#` filtro de tópico. Depois bem-sucedida assinatura tiver sido estabelecida, o dispositivo começará a receber apenas as mensagens de nuvem para dispositivo que tiverem sido enviadas a ele após o horário da assinatura. Se o dispositivo se conecta com o sinalizador **CleanSession** definido como **0**, a assinatura serão persistentes nas sessões diferentes. Nesse caso, na próxima vez em que ele se conecta com **CleanSession 0** o dispositivo receberão mensagens pendentes que tiverem sido enviadas a ele enquanto ele estava desconectado. Se o dispositivo usa o sinalizador **CleanSession** definido como **1** , embora, não receberá quaisquer mensagens do IoT Hub até que ele se inscreve para seu ponto de extremidade do dispositivo.

Hub de IoT entrega mensagens com o **Nome do tópico** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` se houver quaisquer propriedades de mensagem. `{property_bag}`contém pares de chave/valor codificada como url de propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema de usuário configurável (como **messageId** ou **correlationId**) estão incluídas no conjunto de propriedades. Nomes de propriedade de sistema possuem o prefixo **$**, propriedades de aplicativo usar o nome da propriedade original sem prefixo.

Quando um cliente do dispositivo assinado por um tópico com **QoS 2**, o Hub de IoT concede nível máximo QoS 1 no pacote **SUBACK** . Depois disso, o Hub de IoT fornecerá mensagens ao dispositivo usando QoS 1.

### <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se você precisa personalizar o comportamento de protocolo MQTT lado nuvem, você deve examinar o [gateway de protocolo do Azure IoT] [ lnk-azure-protocol-gateway] que permite que você implante um gateway de protocolo personalizado de alto desempenho que interfaces diretamente com IoT Hub. O gateway de protocolo Azure IoT permite personalizar o protocolo de dispositivo para acomodar implantações de MQTT brownfield ou outros protocolos personalizados. Essa abordagem exigem, no entanto, que você execute e opera um gateway de protocolo personalizado.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte as [Observações em MQTT suporte] [ lnk-mqtt-devguide] no guia do desenvolvedor do Azure IoT Hub.

Para saber mais sobre o protocolo MQTT, consulte a [documentação de MQTT][lnk-mqtt-docs].

Para saber mais sobre o planejamento de sua implantação IoT Hub, consulte:

- [Certificado Azure para IoT catálogo de dispositivo][lnk-devices]
- [Oferece suporte aos protocolos adicionais][lnk-protocols]
- [Comparar com Hubs de evento][lnk-compare]
- [Dimensionamento, HA e DR][lnk-scaling]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
