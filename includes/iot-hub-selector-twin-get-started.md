> [AZURE.SELECTOR]
- [Node](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introdução

Gêmeos de dispositivo são documentos JSON que armazenam informações de estado de dispositivo (metadados, configurações e condições). Hub de IoT persistir twin um dispositivo para cada dispositivo que você se conecta ao IoT Hub.

Use Gêmeos de dispositivo para:

* Armazenar metadados de dispositivo do seu back-end.
* Relatar informações do estado atual como recursos disponíveis e condições (por exemplo, o método conectividade usado) de seu aplicativo do dispositivo.
* Sincronize o estado de fluxos de trabalho de execução longa (como atualizações de firmware e configuração) entre o aplicativo do dispositivo e back-end.
* Consulte seu dispositivo metadados, configuração ou estado.

> [AZURE.NOTE] Gêmeos de dispositivo são projetados para a sincronização e para consultar condições e configurações do dispositivo. Mensagens de [dispositivo à nuvem] [ lnk-d2c] para sequências de eventos de marca (como fluxos de telemetria de dados temporais) e [métodos de nuvem para dispositivo] [ lnk-methods] para controle interativo dispositivos, como ativar um ventilador de um aplicativo controlado por usuário.

Gêmeos de dispositivo são armazenados em um hub IoT e contenham:

* *marcas*, dispositivo metadados acessíveis somente pelo back-end;
* *propriedades desejadas*, JSON objetos pode ser modificados pela back-end e observáveis pelo aplicativo do dispositivo; e
* *propriedades relatadas*, JSON objetos pode ser modificados pelo aplicativo do dispositivo e legíveis por trás final. Marcas e propriedades não podem conter matrizes, mas objetos podem ser aninhados.

![][img-twin]

Além disso, o back-end do aplicativo pode consultar Gêmeos de dispositivo com base em todos os dados acima.
Consulte [compreender Gêmeos de dispositivo] [ lnk-twins] para obter mais informações sobre Gêmeos de dispositivo e para a [linguagem de consulta do IoT Hub] [ lnk-query] referência para consultar.

> [AZURE.NOTE] No momento, Gêmeos de dispositivo são acessíveis somente a partir de dispositivos que se conectam a IoT Hub usando o protocolo MQTT. Consulte o [suporte de MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.

Este tutorial mostra como para:

- Crie um aplicativo de back-end que adiciona *marcas* twin um dispositivo e um dispositivo simulado que relatórios de seu canal de conectividade como um *relatados propriedade* o twin do dispositivo.
- Consultar dispositivos de seu aplicativo de back-end usando filtros sobre marcas e propriedades que criou anteriormente.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md