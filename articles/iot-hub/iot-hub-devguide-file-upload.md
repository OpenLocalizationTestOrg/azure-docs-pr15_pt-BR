<properties
 pageTitle="Guia do desenvolvedor - carregamento de arquivo | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - upload de arquivos em um dispositivo IoT hub"
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

# <a name="upload-files-from-a-device"></a>Carregar arquivos em um dispositivo

## <a name="overview"></a>Visão geral

Como detalhado no de [pontos de extremidade do IoT Hub] [ lnk-endpoints] artigo, dispositivos podem iniciar carregamentos de arquivo enviando uma notificação por meio de um ponto de extremidade voltados para o dispositivo (**/devices/ {deviceId} / arquivos**).  Quando um dispositivo notifica IoT Hub de um carregamento concluído, o Hub de IoT gera notificações de carregamento de arquivo que você pode receber por meio de um ponto de extremidade do serviço voltados (**/messages/servicebound/filenotifications**) como mensagens.

Em vez de mensagens de corretagem pelo IoT Hub em si, IoT Hub em vez disso atua como um distribuidor para uma conta de armazenamento do Azure associada. Um dispositivo solicita um token de armazenamento do IoT Hub específico para o arquivo no dispositivo que deseja carregar. O dispositivo usa o URI SAS para carregar o arquivo para o armazenamento e quando o carregamento for concluído o dispositivo envia uma notificação de conclusão para IoT Hub. Hub de IoT verifica se o arquivo foi carregado e depois adiciona uma notificação de carregamento de arquivo ao novo serviço voltados ao arquivo notificação mensagens ponto de extremidade.

Antes de carregar um arquivo IoT hub de um dispositivo, você deve configurar o seu hub associando [Um armazenamento do Azure] [ lnk-associate-storage] conta-lo.

Seu dispositivo pode então [inicializar um carregamento] [ lnk-initialize] e [Notificar IoT hub] [ lnk-notify] quando o carregamento for concluído. Opcionalmente, quando um dispositivo notifica IoT Hub que o upload é concluído, o serviço pode gerar uma [mensagem de notificação][lnk-service-notification].

### <a name="when-to-use"></a>Quando usar

Use este recurso IoT Hub quando você precisa fazer o upload de um arquivo em um dispositivo para seu serviço de back-end em vez de enviar uma mensagem por meio do IoT Hub.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta de armazenamento do Azure IoT Hub

Para usar a funcionalidade de carregamento de arquivo, você deve primeiro vincular uma conta de armazenamento do Azure ao IoT Hub. Você pode fazer isso por meio do [portal do Azure][lnk-management-portal], ou programaticamente por meio do [provedor de recursos do IoT Hub APIs REST][lnk-resource-provider-apis]. Depois de ter associado a uma conta de armazenamento do Azure com seu IoT Hub, o serviço retorna um URI SAS para um dispositivo quando o dispositivo inicia uma solicitação de carregamento de arquivo.

> [AZURE.NOTE] O [Azure IoT Hub SDKs] [ lnk-sdks] tratar automaticamente recuperando o URI SAS, carregar o arquivo e notificando IoT Hub de um carregamento concluído.

## <a name="initialize-a-file-upload"></a>Inicializar um carregamento de arquivo

Hub de IoT tem um ponto de extremidade especificamente para dispositivos solicitar um SAS URI para armazenamento para carregar um arquivo. O dispositivo inicia o processo de carregamento de arquivo enviando uma POSTAGEM para o hub de IoT no `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o corpo JSON a seguir:

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Hub de IoT retorna o seguinte, que usa o dispositivo para carregar o arquivo:

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Obsoleto: inicializar um carregamento de arquivo com um GET

> [AZURE.NOTE] Esta seção descreve funcionalidades preteridas para como receber um URI SAS de IoT Hub. Use o método de POSTAGEM descrito acima.

Hub de IoT tem dois pontos de extremidade do restante para dar suporte a carregamento de arquivo, um para obter o SAS URI para armazenamento e outro para notificar hub IoT de um carregamento concluído. O dispositivo inicia o processo de carregamento de arquivo enviando um GET para o hub de IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O hub retorna um URI SAS específicas para o arquivo a ser carregado e uma ID de correlação a ser usado quando o carregamento é concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar IoT Hub carregamento de um arquivo concluído

O dispositivo é responsável por carregar o arquivo para armazenamento usando o SDK do armazenamento do Azure. Após a conclusão do carregamento, o dispositivo envia uma POSTAGEM para o hub de IoT no `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o corpo JSON a seguir:

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é um representando booliana estando ou não o arquivo foi carregado com êxito. O código de status para `statusCode` é o status do upload do arquivo para armazenamento e o `statusDescription` corresponde à `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como carregar arquivos em um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de carregamento de arquivo

Quando um dispositivo carrega um arquivo e notifica IoT Hub de conclusão do carregamento, o serviço opcionalmente gera uma mensagem de notificação que contém o local de armazenamento e o nome do arquivo.

Como explicado em [pontos de extremidade][lnk-endpoints], IoT Hub fornece notificações de carregamento de arquivo por meio de um ponto de extremidade do serviço voltados (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de recebimento para notificações de carregamento de arquivo são as mesmas mensagens de nuvem para dispositivo e tem a mesma [mensagem de ciclo de vida][lnk-lifecycle]. Cada mensagem recuperada do ponto de extremidade de notificação de carregamento de arquivo é um registro JSON com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Carimbo de hora que indica quando a notificação foi criada. |
| DeviceId | **DeviceId** do dispositivo que carregado o arquivo. |
| BlobUri | URI do arquivo carregado. |
| BlobName | Nome do arquivo carregado. |
| LastUpdatedTime | Carimbo de hora que indica quando o última atualização do arquivo. |
| BlobSizeInBytes | Tamanho do arquivo carregado. |

**Exemplo**. Este é um corpo de exemplo de uma mensagem de notificação de carregamento de arquivo.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de carregamento de arquivo

Cada hub IoT expõe as seguintes opções de configuração para notificações de carregamento de arquivo:

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controla se as notificações de carregamento de arquivo são gravadas o ponto de extremidade de notificações de arquivo. | Bool. Padrão: True. |
| **fileNotifications.ttlAsIso8601** | TTL padrão para as notificações de carregamento de arquivo. | ISO_8601 intervalo até 48 H (mínimo 1 minuto). Padrão: 1 hora. |
| **fileNotifications.lockDuration** | Bloquear duração para a fila de notificações de carregamento de arquivo. | 5 a 300 segundos (mínimos 5 segundos). Padrão: 60 segundos. |
| **fileNotifications.maxDeliveryCount** | Fila de notificação de carregamento de contagem de entrega máximo do arquivo. | 1 a 100. Padrão: 100. |

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como carregar arquivos de dispositivos usando IoT Hub, você pode estar interessado nos seguintes tópicos de guia desenvolvedor:

- [Gerenciar identidades de dispositivo no IoT Hub][lnk-devguide-identities]
- [Controlar o acesso às IoT Hub][lnk-devguide-security]
- [Usar Gêmeos de dispositivo para sincronizar o estado e configurações][lnk-devguide-device-twins]
- [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado no tutorial IoT Hub seguinte:

- [Como carregar arquivos de dispositivos na nuvem com IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
