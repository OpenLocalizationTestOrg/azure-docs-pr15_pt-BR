<properties
 pageTitle="Guia do desenvolvedor - pontos de extremidade do IoT Hub | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - informações de referência sobre pontos de extremidade do IoT Hub"
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

# <a name="reference---iot-hub-endpoints"></a>Referência - pontos de extremidade do IoT Hub

## <a name="list-of-iot-hub-endpoints"></a>Lista de pontos de extremidade do IoT Hub

Hub de IoT Azure é um serviço de vários locatário que expõe sua funcionalidade para vários atores. O diagrama a seguir mostra os vários pontos de extremidade que IoT Hub expõe.

![Pontos de extremidade do IoT Hub][img-endpoints]

A seguir há uma descrição dos pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos do IoT Hub expõe um [Gerente de recursos do Azure] [ lnk-arm] interface que permite que os proprietários de assinatura Azure para criar e excluir IoT hubs e atualizar as propriedades de hub IoT. Propriedades IoT Hub regem [políticas de segurança em nível de hub][lnk-accesscontrol], em vez de controle de acesso de nível de dispositivo e opções funcionais para mensagens de nuvem para dispositivo e o dispositivo na nuvem. O provedor de recursos do IoT Hub também permite que você [Exportar identidades de dispositivo][lnk-importexport].
* **Gerenciamento de identidades do dispositivo**. Cada hub IoT expõe um conjunto de pontos de extremidade de HTTP (REST) para gerenciar identidades do dispositivo (criar, recuperar, atualizar e excluir). [Identidades do dispositivo] [ lnk-device-identities] são usados para controle de autenticação e acesso de dispositivo.
* **Gerenciamento de twin do dispositivo**. Cada hub IoT expõe um conjunto de ponto de extremidade do serviço voltados HTTP (REST) consultar e atualizar [Gêmeos de dispositivo] [ lnk-twins] (Atualizar marcas e propriedades).
* **Gerenciamento de trabalhos**. Cada hub IoT expõe um conjunto de ponto de extremidade do serviço voltados HTTP (REST) para consultar e gerenciar [trabalhos][lnk-jobs].
* **Pontos de extremidade do dispositivo**. Para cada dispositivo provisionado no registro de identidade do dispositivo, o Hub de IoT expõe um conjunto de pontos de extremidade que um dispositivo pode usar para enviar e receber mensagens:
    - *Enviar mensagens de dispositivo na nuvem*. Use esse ponto de extremidade para [Enviar mensagens de dispositivo à nuvem][lnk-d2c].
    - *Receber mensagens de nuvem para dispositivo*. Um dispositivo usa esse ponto de extremidade para receber [mensagens de nuvem para dispositivo]de destino[lnk-c2d].
    - *Iniciar carregamentos de arquivo*. Um dispositivo usa esse ponto de extremidade para receber um URI do Azure armazenamento SAS de IoT Hub para [carregar um arquivo][lnk-upload].
    - *Recuperar e propriedades da atualização twin*. Um dispositivo usa esta pontos de extremidade para acessar seu [twin dispositivo][lnk-twins]de propriedades.
    - *Solicitações de métodos diretos de recebimento*. Um dispositivo usa esta pontos de extremidade para escutar [métodos diretos][lnk-methods]do solicitações.

    Esses pontos de extremidade expostos por meio de [v 3.1.1 MQTT][lnk-mqtt], HTTP 1.1 e [1.0 AMQP] [ lnk-amqp] protocolos. Observe que AMQP também está disponível ao longo do [WebSocket] [ lnk-websockets] na porta 443.
    
    Endpoins as gêmeos e métodos estão disponíveis somente usando [v 3.1.1 MQTT][lnk-mqtt].

* **Pontos de extremidade do serviço**. Cada hub IoT expõe um conjunto de pontos de extremidade que back-end seu aplicativo pode usar para se comunicar com seus dispositivos. Esses pontos de extremidade são atualmente expostos usando somente o [AMQP] [ lnk-amqp] protocolo, exceto para o ponto de extremidade de invocação de método exposto por meio de HTTP 1.1.
    - *Receber mensagens de dispositivo na nuvem*. Neste ponto de extremidade é compatível com [Hubs de evento do Azure][lnk-event-hubs]. Um serviço de back-end pode usá-lo para ler todas as [mensagens de dispositivo à nuvem] [ lnk-d2c] enviados por seus dispositivos.
    - *Enviar mensagens de nuvem para dispositivo e receber confirmações de entrega*. Esses pontos de extremidade habilitar seu back-end de aplicativo enviar [mensagens de nuvem para dispositivo]de confiável[lnk-c2d]e receber a entrega correspondente ou confirmações de expiração.
    - *Receber notificações de arquivo*. Neste ponto de extremidade de mensagens permite que você receba notificações de quando seus dispositivos com êxito carreguem um arquivo. 
    - *Invocação de método direto*. Neste ponto de extremidade permite que um serviço de back-end chamar um [método direto] [ lnk-methods] em um dispositivo.

A [IoT Hub APIs e SDKs] [ lnk-sdks] artigo descreve as diversas maneiras de acessar esses pontos de extremidade.

Finalmente, é importante observar que todos os pontos de extremidade do IoT Hub usam [TLS] [ lnk-tls] protocolo e nenhum ponto de extremidade nunca é exposto em canais sem criptografia/desprotegida.

## <a name="field-gateways"></a>Gateways de campo

Em uma solução IoT, um *gateway de campo* fica entre seus dispositivos e seus pontos de extremidade do IoT Hub. Ele está normalmente localizado próximo seus dispositivos. Seus dispositivos se comunicar diretamente com o gateway de campo usando um protocolo compatível com os dispositivos. O gateway de campo se conecta a um ponto de extremidade do IoT Hub usando um protocolo que é suportado pelo IoT Hub. Um gateway de campo pode ser hardware altamente especializado ou um baixa energia computador executando software que realiza o cenário de ponta a ponta para o qual destina-se o gateway.

Você pode usar o [SDK do Azure IoT Gateway] [ lnk-gateway-sdk] implementar um gateway de campo. Este SDK oferece funcionalidade específica como a capacidade para multiplicar a comunicação de vários dispositivos para a mesma conexão IoT Hub.

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste guia de desenvolvedor do IoT Hub incluem:

- [Linguagem de consulta para gêmeos, métodos e trabalhos][lnk-devguide-query]
- [Cotas e otimização][lnk-devguide-quotas]
- [Suporte de IoT Hub MQTT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md