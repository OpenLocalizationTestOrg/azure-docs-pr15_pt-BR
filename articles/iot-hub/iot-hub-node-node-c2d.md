<properties
    pageTitle="Enviar mensagens de nuvem para dispositivo com IoT Hub | Microsoft Azure"
    description="Siga este tutorial para saber como enviar mensagens de nuvem para dispositivo usando Azure IoT Hub com Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Tutorial: Como enviar mensagens de nuvem para dispositivo com IoT Hub e Node

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que ajuda a habilitar confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e encerrar um aplicativo novamente. O tutorial de [Introdução ao IoT Hub] mostra como criar um hub IoT, provisionar uma dispositivo identidade nele e o código de um dispositivo simulado que envia mensagens de dispositivo na nuvem.

Este tutorial cria na [Introdução ao IoT Hub]. Mostra como para:

- Do seu aplicativo nuvem back-end, envie mensagens de nuvem para dispositivo para um único dispositivo por IoT Hub.
- Receba mensagens de nuvem para dispositivo em um dispositivo.
- Da nuvem seu aplicativo back-end, solicitar confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo de IoT Hub.

Você pode encontrar mais informações sobre mensagens de nuvem para dispositivo no [Guia do desenvolvedor do IoT Hub][IoT Hub Developer Guide - C2D].

No final deste tutorial, você deve executar dois aplicativos de console node:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Introdução ao IoT Hub], que se conecta ao seu hub IoT e recebe mensagens de nuvem para dispositivo.
* **SendCloudToDeviceMessage**, que envia uma mensagem de nuvem para dispositivo para o dispositivo simulado por IoT Hub e então recebe sua confirmação de entrega.

> [AZURE.NOTE] Hub de IoT tem suporte SDK muitas plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) por meio do Azure IoT dispositivo SDKs. Para obter instruções passo a passo sobre como conectar seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Azure IoT Developer Center].

Para concluir este tutorial, você precisa do seguinte:

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens do dispositivo simulado

Nesta seção, você modificar o aplicativo de dispositivo simulado criado na [Introdução ao IoT Hub] para receber mensagens de nuvem para dispositivo do hub IoT.

1. Usando um editor de texto, abra o arquivo SimulatedDevice.js.

2. Modificar a função de **connectCallback** para lidar com mensagens enviadas de IoT Hub. Neste exemplo, o dispositivo sempre chama a função **completa** para notificá-IoT Hub que ele tenha processado a mensagem. Sua nova versão da função **connectCallback** tem esta aparência:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Se você usar HTTP em vez de MQTT ou AMQP como transporte, a instância de **DeviceClient** verifica mensagens de Hub de IoT raramente (menos de cada minutos 25). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTP e IoT Hub limitação, consulte o [Guia de desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de nuvem para dispositivo

Nesta seção, você criar um aplicativo de console Node que envia mensagens de nuvem para dispositivo ao aplicativo do dispositivo simulado. É necessário o Id do dispositivo que você adicionou o tutorial de [Introdução ao IoT Hub] de dispositivo. Você também precisa a cadeia de conexão para o seu hub IoT que você pode encontrar no [portal do Azure].

1. Crie uma pasta vazia chamada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **sendcloudtodevicemessage** , execute o seguinte comando para instalar o pacote do **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **SendCloudToDeviceMessage.js** na pasta **sendcloudtodevicemessage** .

4. Adicione o seguinte `require` instruções no início do arquivo **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o seguinte código ao arquivo de **SendCloudToDeviceMessage.js** . Substitua o valor de espaço reservado de cadeia de conexão com a cadeia de conexão para o hub de IoT que você criou o tutorial de [Introdução ao IoT Hub] . Substitua o espaço reservado dispositivo de destino com a id de dispositivo do dispositivo que você adicionou o tutorial de [Introdução ao IoT Hub] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para imprimir os resultados de operação no console:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função para imprimir mensagens de comentários de entrega no console:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e manipular a mensagem de comentários quando o dispositivo reconhece a mensagem de nuvem para dispositivo:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Salve e feche o arquivo de **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando-na pasta **simulateddevice** , execute o seguinte comando para enviar telemetria IoT hub e escutar mensagens de nuvem para dispositivo:

    ```
    node SimulatedDevice.js 
    ```

    ![Executar o aplicativo do dispositivo simulado][img-simulated-device]

2. No prompt de comando-na pasta **sendcloudtodevicemessage** , execute o seguinte comando para enviar uma mensagem de nuvem para dispositivo e aguarde a comentários de confirmação:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Executar o aplicativo para enviar o comando c2d][img-send-command]

    > [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como retirada exponencial), conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a enviar e receber mensagens de nuvem para dispositivo. 

Para ver exemplos de soluções de ponta a ponta completas que usam IoT Hub, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com IoT Hub, consulte o [Guia de desenvolvedor do IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guia de desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Central de desenvolvedores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Pacote de IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/