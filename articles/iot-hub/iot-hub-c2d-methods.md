<properties
 pageTitle="Usar métodos diretos | Microsoft Azure"
 description="Este tutorial mostra como usar métodos diretos"
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
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Tutorial: Usar métodos diretos

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que permite confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e um aplicativo back-end. Tutoriais anteriores ([Introdução ao IoT Hub] e [Enviar mensagens de nuvem para dispositivo com IoT Hub]) ilustram a dispositivo na nuvem e nuvem para dispositivo mensagens funcionalidade básica de IoT Hub. Hub de IoT também oferece a capacidade de chamar métodos não-duráveis em dispositivos da nuvem. Métodos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada de HTTP em que eles êxito ou falham imediatamente (após um tempo limite especificado pelo usuário) para o usuário informar o status da chamada. [Invocar um método direto em um dispositivo] [ lnk-devguide-methods] descreve métodos em mais detalhes e oferece orientações sobre quando usar métodos versus mensagens de nuvem para dispositivo.

Este tutorial mostra como para:

- Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no seu hub IoT.
- Crie um dispositivo simulado que tem um método direto que pode ser chamado pela nuvem.
- Crie um aplicativo de console que chama um método direto no dispositivo simulado por meio de seu hub IoT.

> [AZURE.NOTE] No momento, métodos diretos são acessíveis somente a partir de dispositivos que se conectam a IoT Hub usando o protocolo MQTT. Consulte o [suporte de MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.

No final deste tutorial, você tem dois aplicativos de console node:

* **CallMethodOnDevice.js**, que chama um método do dispositivo simulado e exibe a resposta.
* **SimulatedDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente e responde ao método chamado na nuvem.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos executados em dispositivos e back-end sua solução.

Para concluir este tutorial, você precisa do seguinte:

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você criar um aplicativo de console Node que responde a um método chamado pela nuvem.

1. Crie uma nova pasta vazia chamada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **simulateddevice** , execute o seguinte comando para instalar o pacote do **azure-iot-dispositivo** Device SDK e o pacote do **azure-iot-dispositivo-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **SimulatedDevice.js** na pasta **simulateddevice** .

4. Adicione o seguinte `require` instruções no início do arquivo **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Adicionar uma variável de **connectionString** e usá-lo para criar um cliente do dispositivo. Substitua **{cadeia de conexão do dispositivo}** com a cadeia de conexão que você gerou na seção *criar uma identidade do dispositivo* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Adicione a seguinte função para implementar o método no dispositivo:

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Abrir a conexão para seu hub IoT e iniciar inicializar o ouvinte método:

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Salve e feche o arquivo **SimulatedDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como repetir de conexão), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Chamar um método em um dispositivo

Nesta seção, você criar um aplicativo de console Node que chama um método do dispositivo simulado e, em seguida, exibe a resposta.

1. Crie uma nova pasta vazia chamada **callmethodondevice**. Na pasta **callmethodondevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **callmethodondevice** , execute o seguinte comando para instalar o pacote do **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Usando um editor de texto, crie um arquivo de **CallMethodOnDevice.js** na pasta **callmethodondevice** .

4. Adicione o seguinte `require` instruções no início do arquivo **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado com a cadeia de conexão para o seu hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Crie o cliente para abrir a conexão para o seu hub IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Adicione a seguinte função para chamar o método de dispositivo e imprimir a resposta de dispositivo no console:

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Salve e feche o arquivo **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando-na pasta **simulateddevice** , execute o seguinte comando para começar a ouvir para chamadas de método de seu IoT Hub:

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. No prompt de comando-na pasta **callmethodondevice** , execute o seguinte comando para começar a monitorar seu hub IoT:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Você verá o dispositivo reagir ao método imprimindo a mensagem e o aplicativo que chamado a exibição de método a resposta do dispositivo:

    ![][9]
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um hub IoT novo no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você usou este dispositivo identidade para habilitar o aplicativo do dispositivo simulado reagir a métodos invocados pela nuvem. Você também criou um aplicativo que chama métodos no dispositivo e exibe a resposta do dispositivo. 

Para continuar a introdução ao IoT Hub e explorar outros cenários IoT, consulte:

- [Introdução ao IoT Hub]
- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Para saber como estender sua IoT chamadas de método de solução e cronograma em vários dispositivos, consulte a [agenda e trabalhos de transmissão] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Enviar mensagens de nuvem para dispositivo com IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
