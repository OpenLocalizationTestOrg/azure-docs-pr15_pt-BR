<properties
 pageTitle="Monitoramento de operações de IoT Hub"
 description="Uma visão geral das operações de Azure IoT Hub monitoramento, permitindo que você monitorar o status das operações em seu hub IoT em tempo real"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introdução ao monitoramento de operações

Monitoramento de operações de IoT Hub permite monitorar o status das operações em seu hub IoT em tempo real. Hub de IoT controla eventos em várias categorias de operações, e você pode optar por usar para o envio de eventos de uma ou mais categorias para um ponto de extremidade do seu hub IoT para processamento. Você pode monitorar os dados de erros ou configurar o processamento mais complexo com base em padrões de dados.

Hub de IoT monitora cinco categorias de eventos:

- Operações de identidade de dispositivo
- Telemetria do dispositivo
- Comandos de nuvem para dispositivo
- Conexões
- Carregamentos de arquivo

## <a name="how-to-enable-operations-monitoring"></a>Como habilitar o monitoramento de operações

1. Crie um hub IoT. Você pode encontrar instruções sobre como criar um hub IoT na [Introdução] [ lnk-get-started] guia.

2. Abra a lâmina de seu hub IoT. A partir daí, clique em **monitoramento de operações**.

    ![][1]

3. Selecione as categorias de monitoramento que você quer que você monitorar e clique em **Salvar**. Os eventos estão disponíveis para leitura do ponto de extremidade compatível com o evento Hub listada em **configurações de monitoramento**. O ponto de extremidade do IoT Hub é chamado `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Categorias de eventos e como usá-las

Cada é monitoramento faixas de categoria de operações um tipo diferente de interação com IoT Hub e cada categoria de monitoramento tem um esquema que define como eventos dessa categoria são estruturados.

### <a name="device-identity-operations"></a>Operações de identidade de dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do hub seu IoT. Essa categoria de rastreamento é útil para cenários de provisionamento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo rastreia erros que ocorrem no hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem ao enviar eventos de telemetria (como a otimização) e recebendo eventos de telemetria (como leitor não autorizado). Observe que essa categoria não pode detectar erros causados pelo código em execução no próprio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandos de nuvem para dispositivo

A categoria comandos de nuvem para dispositivo rastreia erros que ocorrem no hub IoT e estão relacionados para o pipeline de comando do dispositivo. Essa categoria inclui erros que ocorrem quando enviar comandos (como remetente não autorizada), recebendo comandos (como contagem de entrega excedida) e receber comentários de comando (como comentários expirado). Esta categoria não detectar erros em um dispositivo que manipula incorretamente um comando, se o comando foi entregue com êxito.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Conexões

A categoria de conexões rastreia erros que ocorrem quando dispositivos conectar ou desconectar um hub IoT. Essa categoria de rastreamento é útil para identificar tentativas de conexão não autorizado e para controlar quando uma conexão é perdida para dispositivos nas áreas de conectividade ruim.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Carregamentos de arquivo

A categoria de carregamento de arquivo rastreia erros que ocorrem no hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui erros que ocorrem com o URI SAS (como quando ele expirar antes de um dispositivo notifica o hub de um carregamento concluído), falhas carregamentos relatados pelo dispositivo e quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do IoT Hub. Observe que essa categoria não pode detectar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo para armazenamento.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Próximas etapas

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md