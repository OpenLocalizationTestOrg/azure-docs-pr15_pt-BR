<properties
    pageTitle="Hub de IoT Azure para Node Introdução | Microsoft Azure"
    description="Hub de IoT Azure com Node obtendo iniciado tutorial. Use o Azure IoT Hub e Node com o Microsoft Azure IoT SDKs implementar uma solução de Internet das coisas."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Introdução ao Azure IoT Hub para Node

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, você tem três aplicativos de console node:

* **CreateDeviceIdentity.js**, que cria uma identidade de dispositivo e a chave de segurança associadas para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que exibe a telemetria enviada por seu dispositivo simulado.
* **SimulatedDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQP.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos executados em dispositivos e back-end sua solução.

Para concluir este tutorial, você precisa do seguinte:

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora que você criou seu hub IoT. Você tem o nome de host do IoT Hub e a cadeia de conexão IoT Hub que você precisa para concluir o restante deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta seção, você criar um aplicativo de console Node que cria uma identidade de dispositivo no registro identidade em seu hub IoT. Um dispositivo não pode se conectar a IoT hub, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Consulte a seção de **Registro de identidade de dispositivos** do [Guia de desenvolvedor do Hub IoT] [ lnk-devguide-identity] para obter mais informações. Quando você executa este aplicativo de console, ele gera uma identificação de dispositivo exclusivo e chave que seu dispositivo pode usar para se identificar quando ele envia mensagens de dispositivo à nuvem para IoT Hub.

1. Crie uma nova pasta vazia chamada **createdeviceidentity**. Na pasta **createdeviceidentity** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **createdeviceidentity** , execute o seguinte comando para instalar o pacote de serviço SDK do **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo de **CreateDeviceIdentity.js** na pasta **createdeviceidentity** .

4. Adicione o seguinte `require` instrução no início do arquivo **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código para o arquivo **CreateDeviceIdentity.js** e substitua o valor de espaço reservado com a cadeia de conexão para o hub de IoT que você criou na seção anterior: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o seguinte código para criar uma definição de dispositivo no registro de identidade do dispositivo no seu hub IoT. Esse código cria um dispositivo se a id do dispositivo não existir no registro, caso contrário, retorna a chave do dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Salve e feche o arquivo de **CreateDeviceIdentity.js** .

8. Para executar o aplicativo **createdeviceidentity** , execute o seguinte comando no prompt de comando na pasta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Anote a **id do dispositivo** e a **chave do dispositivo**. Você precisa desses valores mais tarde quando você cria um aplicativo que se conecta ao IoT Hub como um dispositivo.

> [AZURE.NOTE] O registro de identidade IoT Hub armazena apenas identidades do dispositivo para habilitar o acesso seguro ao hub. Ele armazena identificações de dispositivo e teclas para usar como credenciais de segurança e um sinalizador de ativado/desativado que você pode usar para desabilitar o acesso para um dispositivo individual. Se seu aplicativo precisa armazenar outros metadados específicos do dispositivo, ele deve usar uma loja de aplicativos específicos. Consulte o [Guia de desenvolvedor do Hub IoT] [ lnk-devguide-identity] para obter mais informações.

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de dispositivo à nuvem

Nesta seção, você criar um aplicativo de console Node que lê mensagens de dispositivo à nuvem do IoT Hub. Um hub IoT expõe um [Evento Hubs][lnk-event-hubs-overview]-ponto de extremidade compatível para que você possa ler mensagens de dispositivo na nuvem. Para manter as coisas simples, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. As [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial mostra como processar mensagens de dispositivo à nuvem em escala. A [Começar com Hubs de evento] [ lnk-eventhubs-tutorial] tutorial fornece mais informações sobre como processar mensagens de evento Hubs e aplica-se a pontos de extremidade compatível com o evento de Hub IoT Hub.

> [AZURE.NOTE] O ponto de extremidade compatível com o evento Hub para ler mensagens de dispositivo à nuvem sempre usa o protocolo AMQP.

1. Crie uma nova pasta vazia chamada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **readdevicetocloudmessages** , execute o seguinte comando para instalar o pacote do **azure-evento-hubs** :

    ```
    npm install azure-event-hubs --save
    ```

3. Usando um editor de texto, crie um arquivo de **ReadDeviceToCloudMessages.js** na pasta **readdevicetocloudmessages** .

4. Adicione o seguinte `require` instruções no início do arquivo **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado com a cadeia de conexão para o seu hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Adicione duas funções a seguir que imprimir saída para o console:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o seguinte código para criar o **EventHubClient**, abra a conexão para o seu IoT Hub e criar um receptor para cada partição. Este aplicativo usa um filtro quando cria um receptor para que o destinatário lê somente as mensagens enviadas a IoT Hub após o receptor começa a ser executado. Esse filtro é útil em um ambiente de teste para ver apenas o conjunto atual de mensagens. Em um ambiente de produção, seu código deverá garantir que ele processa todas as mensagens - ver [como processar mensagens de dispositivo à nuvem IoT Hub] [ lnk-process-d2c-tutorial] tutorial para obter mais informações:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Salve e feche o arquivo **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você criar um aplicativo de console Node que simula um dispositivo que envia mensagens de dispositivo à nuvem para um hub IoT.

1. Crie uma nova pasta vazia chamada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **simulateddevice** , execute o seguinte comando para instalar o pacote do **azure-iot-dispositivo** Device SDK e o pacote do **azure-iot-dispositivo-amqp** :

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **SimulatedDevice.js** na pasta **simulateddevice** .

4. Adicione o seguinte `require` instruções no início do arquivo **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicionar uma variável de **connectionString** e usá-lo para criar um cliente do dispositivo. Substitua **{youriothostname}** com o nome do hub IoT a seção de *criar um IoT Hub* foi criada. Substitua **{yourdevicekey}** com o valor de chave de dispositivo que você gerou na seção *criar uma identidade do dispositivo* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para exibir a saída do aplicativo:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crie um retorno de chamada e use a função **setInterval** para enviar uma nova mensagem para seu hub IoT cada segundo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Abra a conexão para o seu IoT Hub e começar a enviar mensagens:

    ```
    client.open(connectCallback);
    ```

9. Salve e feche o arquivo **SimulatedDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].


## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando-na pasta **readdevicetocloudmessages** , execute o seguinte comando para começar a monitorar seu hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicativo de cliente de serviço Node IoT Hub para monitorar mensagens do dispositivo na nuvem][7]

2. No prompt de comando-na pasta **simulateddevice** , execute o seguinte comando para começar a enviar dados de telemetria para seu hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![Aplicativo de cliente do Node IoT Hub dispositivo para enviar mensagens de dispositivo à nuvem][8]

3. O bloco de **uso** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![Azure portal uso lado a lado mostrando o número de mensagens enviadas para IoT Hub][43]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um hub IoT novo no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você usou este dispositivo identidade para habilitar o aplicativo do dispositivo simulado enviar mensagens de dispositivo à nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub. 

Para continuar a introdução ao IoT Hub e explorar outros cenários IoT, consulte:

- [Conectar seu dispositivo][lnk-connect-device]
- [Introdução ao gerenciamento de dispositivo][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como estender sua solução IoT e processar mensagens de dispositivo à nuvem em escala, consulte as [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
