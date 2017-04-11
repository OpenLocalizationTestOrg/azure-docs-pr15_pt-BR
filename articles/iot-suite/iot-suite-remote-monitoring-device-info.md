<properties
 pageTitle="Metadados de informações de dispositivo da solução de monitoramento remoto | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado solução monitoramento e remotos sua arquitetura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadados de informações de dispositivo no monitoramento remoto pré-configurado solução

O pacote de IoT Azure monitoramento solução pré-configurada remoto demonstra uma abordagem de gerenciamento de metadados de dispositivo. Este artigo descreve a abordagem que esta solução leva para que você possa entender:

- Quais metadados de dispositivo a solução armazena.
- Como a solução gerencia os metadados de dispositivo.

## <a name="context"></a>Contexto

A solução de pré-configurado monitoramento remota usa [Azure IoT Hub] [ lnk-iot-hub] para ativar seus dispositivos enviar dados para a nuvem. Hub de IoT inclui um [registro de identidade do dispositivo] [ lnk-identity-registry] para controlar o acesso IoT Hub. O registro de identidade de dispositivo IoT Hub é separado do computador remoto monitoramento de solução específicas *registro do dispositivo* que armazena metadados de informações do dispositivo. A solução de monitoramento remota usa um [DocumentDB] [ lnk-docdb] banco de dados para implementar o seu registro de dispositivo para armazenar metadados de informações do dispositivo. A [Arquitetura de referência do Microsoft Azure IoT] [ lnk-ref-arch] descreve a função do registro do dispositivo em uma solução IoT típico.

> [AZURE.NOTE] A solução pré-configurada monitoramento remota mantém o registro do dispositivo de identidade em sincronia com o registro do dispositivo. Usam a mesma id de dispositivo para identificar exclusivamente cada dispositivo conectado ao seu hub IoT.

A [visualização de gerenciamento de dispositivo do IoT Hub] [ lnk-dm-preview] adiciona recursos a IoT Hub que são parecidas com os recursos de gerenciamento de informações do dispositivo descritos neste artigo. Atualmente, a solução de monitoramento remota usa apenas os recursos disponibilidade geral (GA) no IoT Hub.

## <a name="device-information-metadata"></a>Metadados de informações de dispositivo

Um documento JSON de metadados do dispositivo informações armazenado do banco de dados de DocumentDB de registro do dispositivo tem a seguinte estrutura:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: O próprio dispositivo grava essas propriedades e o dispositivo é a autoridade para esses dados. Outras propriedades de dispositivo de exemplo incluem fabricante, modelo e número de série. 
- **DeviceID**: A id de dispositivo exclusivo. Esse valor é o mesmo no registro de identidade de dispositivo do IoT Hub.
- **HubEnabledState**: O status do dispositivo no IoT Hub. Esse valor é inicialmente definido como **Nulo** até que o dispositivo se conecta. No portal de solução, um valor **Nulo** é representado como o dispositivo sendo "registrado, mas não está presente".
- **CreatedTime**: A hora em que o dispositivo foi criado.
- **DeviceState**: O estado relatado pelo dispositivo.
- **UpdatedTime**: O tempo que o dispositivo foi atualizada pela última vez por meio do portal de solução.
- **SystemProperties**: O portal de solução grava as propriedades do sistema e o dispositivo não tem conhecimento dessas propriedades. Um exemplo de propriedade de sistema é o **ICCID** se a solução for autorizada com e conectado a um serviço de gerenciamento de dispositivos habilitados para SIM.
- **Comandos**: uma lista dos comandos o dispositivo oferece suporte. O dispositivo fornece essas informações para a solução.
- **CommandHistory**: uma lista dos comandos enviados pela solução de monitoramento remota para o dispositivo e o status desses comandos.
- **IsSimulatedDevice**: um sinalizador que identifica um dispositivo como um dispositivo simulado.
- **ID**: O identificador exclusivo do DocumentDB para este documento de dispositivo.

> [AZURE.NOTE] Informações de dispositivo também podem incluir metadados para descrever a telemetria que o dispositivo envia a IoT Hub. A solução de monitoramento remota usa esses metadados de telemetria para personalizar como o painel exibe [telemetria dinâmica][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Quando você cria primeiro um dispositivo no portal de solução, a solução cria uma entrada em seu registro de dispositivo, conforme mostrado anteriormente. Muitas das informações está inicialmente oculto e o **HubEnabledState** está definido como **Nulo**. Neste ponto, a solução também cria uma entrada para o dispositivo no registro de identidade do dispositivo, que gera as teclas que no dispositivo usa para autenticar com IoT Hub.

Quando um dispositivo primeiro se conecta à solução, ele envia uma mensagem de informações do dispositivo. Essa mensagem de informações do dispositivo inclui propriedades de dispositivo como o fabricante do dispositivo, número de modelo e número de série. Uma mensagem de informações do dispositivo também inclui uma lista dos comandos que o dispositivo oferece suporte, incluindo informações sobre os parâmetros de comando. Quando a solução recebe essa mensagem, ele atualiza os metadados de informações de dispositivo no registro do dispositivo.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Exiba e edite informações de dispositivo no portal de solução

Lista de dispositivos no portal de solução exibe as seguintes propriedades de dispositivo como colunas: **Status**, **DeviceId**, **fabricante**, **Modelo**, **número de série**, **Firmware**, **plataforma**, **processador**e **RAM instalado**. As propriedades do dispositivo **Latitude** e **Longitude** unidade o local no mapa do Bing no painel. 

![Lista de dispositivos][img-device-list]

Se você clicar em **Editar** no painel de **Detalhes de dispositivo** no portal de solução, você pode editar a todas essas propriedades. Editar estas propriedades atualiza o registro do dispositivo do banco de dados de DocumentDB. No entanto, se um dispositivo envia uma mensagem de informações do dispositivo atualizado, ele substitui quaisquer alterações feitas no portal de solução. Você não pode editar o **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**e propriedades de **UpdatedTime** no portal de solução porque somente o dispositivo tem autoridade sobre essas propriedades.

![Edição de dispositivo][img-device-edit]

Você pode usar o portal de solução para remover um dispositivo da sua solução. Quando você remove um dispositivo, a solução remove os metadados de informações de dispositivo do registro do dispositivo de solução e a entrada de dispositivo no registro de identidade de dispositivo do IoT Hub. Antes de poder remover um dispositivo, você deve desabilitá-lo.

![Remover dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Processamento de mensagem de informações do dispositivo

Mensagens de informações de dispositivo enviadas por um dispositivo são diferentes de mensagens de telemetria. Mensagens de informações de dispositivo incluem informações como propriedades do dispositivo, os comandos que um dispositivo pode responder e qualquer histórico de comando. Hub de IoT em si não tem conhecimento dos metadados contido em uma mensagem de informações do dispositivo e processa a mensagem da mesma forma que ele processa qualquer mensagem dispositivo na nuvem. Na solução de monitoramento remota, uma [Análise de fluxo de Azure] [ lnk-stream-analytics] trabalho (ASA) lê as mensagens de IoT Hub. A análise de fluxo de **DeviceInfo** trabalho filtros para mensagens que contêm **"ObjectType": "DeviceInfo"** e os encaminha para a instância de host **EventProcessorHost** que é executado em um trabalho de web. Lógica da instância **EventProcessorHost** usa a identificação de dispositivo para localizar o registro DocumentDB para o dispositivo específico e atualize o registro. O registro de registro do dispositivo agora inclui informações como propriedades do dispositivo, comandos e histórico de comando.

> [AZURE.NOTE] Uma mensagem de informações do dispositivo é uma mensagem padrão do dispositivo na nuvem. A solução distingue entre mensagens de informações do dispositivo e telemetria usando consultas ASA.

## <a name="example-device-information-records"></a>Exemplo de registros de informações de dispositivo

A solução pré-configurada monitoramento remota usa dois tipos de registros de informações de dispositivo: registros para os dispositivos simulados implantados com a solução e registros para os dispositivos personalizados que você se conectar à solução.

### <a name="simulated-device"></a>Dispositivo simulado

O exemplo a seguir mostra o registro de informações de dispositivo JSON para um dispositivo simulado. Este registro tem um valor definido para **UpdatedTime**, que indica que o dispositivo tenha enviado uma mensagem de **DeviceInfo** IoT hub. O registro inclui algumas propriedades comuns do dispositivo, define seis comandos simulados dispositivos de suporte e tem o sinalizador **IsSimulatedDevice** definido como **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Dispositivo personalizado

O exemplo a seguir mostra o registro de informações de dispositivo JSON para um dispositivo personalizado e tem o sinalizador **IsSimulatedDevice** definido como **0**. Você pode ver que esse dispositivo personalizado oferece suporte a dois comandos e que o portal de solução enviou um comando **SetTemperature (DefinirTemperatura)** no dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

A seguir mostra a mensagem JSON **DeviceInfo** o dispositivo enviado para atualizar os metadados de informações do dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Agora você terminar de como você pode personalizar as soluções pré-configuradas de aprendizagem, você pode explorar alguns dos outros recursos e recursos das soluções Suite IoT pré-configurado:

- [Visão geral da solução previsão manutenção pré-configurado][lnk-predictive-overview]
- [Perguntas frequentes para IoT pacote][lnk-faq]
- [Segurança IoT desde o início][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
