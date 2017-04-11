<properties
 pageTitle="Como agendar trabalhos | Microsoft Azure"
 description="Este tutorial mostra como agendar trabalhos"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Tutorial: Agendar e transmitir trabalhos (prévia)

## <a name="introduction"></a>Introdução
Hub de IoT Azure é um serviço totalmente gerenciado que permite um back-end de aplicativo criar e controlar trabalhos que agendem e atualizar milhões de dispositivos.  Trabalhos podem ser usados para as seguintes ações:

- Atualizar as propriedades de twin desejado de dispositivo
- Atualizar as marcas de twin de dispositivo
- Chamar métodos de nuvem para dispositivo

Forma conceitual, um trabalho envolve uma destas ações e controla o andamento da execução de um conjunto de dispositivos, que é definido por uma consulta de twin.  Por exemplo, usando um trabalho um back-end do aplicativo pode chamar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta de twin e agendada no futuro.  Aplicativo, em seguida, pode acompanhar o andamento como cada um desses dispositivos receber e executar o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

- Twin do dispositivo e propriedades: [Introdução ao twin] [ lnk-get-started-twin] e [Tutorial: como usar propriedades de twin][lnk-twin-props]
- Métodos de nuvem para dispositivo: [guia de desenvolvedor - métodos diretos] [ lnk-dev-methods] e [Tutorial: métodos de C2D][lnk-c2d-methods]

Este tutorial mostra como para:

- Crie um dispositivo simulado que tem um método direto que permite lockDoor que pode ser chamado pelo aplicativo volta final.
- Crie um aplicativo de console que chama o método direto lockDoor no dispositivo simulado usando um trabalho e atualiza as propriedades de twin desejado usando um trabalho de dispositivo.

No final deste tutorial, você tem dois aplicativos de console node:

**simDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto lockDoor.

**scheduleJobService.js**, que chama um método direto no dispositivo simulado e atualizar as propriedades de disired do twin usando um trabalho.

Para concluir este tutorial, você precisa do seguinte:

Node versão 0.12.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node para este tutorial em Windows ou Linux.

Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console Node que responde a um método direto chamado na nuvem, que aciona a reinicialização do dispositivo simulado e usa o twin dispositivo relatado propriedades para habilitar dispositivo twin consultas identificar dispositivos e quando eles reiniciado pela última vez.

1. Crie uma nova pasta vazia chamada **simDevice**.  Na pasta **simDevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **simDevice** , execute o seguinte comando para instalar o pacote do **azure-iot-dispositivo** Device SDK e o pacote do **azure-iot-dispositivo-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **simDevice.js** na pasta **simDevice** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e usá-lo para criar um cliente do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para lidar com o método lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Adicione o seguinte código para registrar o manipulador para o método lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Salve e feche o arquivo **simDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades de um twin

Nesta seção, você cria um aplicativo de console Node que inicia uma lockDoor remoto em um dispositivo usando um método direto e atualizar as propriedades do twin.

1. Crie uma nova pasta vazia chamada **scheduleJobService**.  Na pasta **scheduleJobService** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **scheduleJobService** , execute o seguinte comando para instalar o pacote de SDK do dispositivo de **iothub do azure** e o **azure-iot-dispositivo-mqtt** pacote:

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. Usando um editor de texto, crie um novo arquivo de **scheduleJobService.js** na pasta **scheduleJobService** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de espaço reservado:

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Adicione a seguinte função que será usada para monitorar a execução do trabalho:

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adicione o seguinte código para agendar o trabalho que chama o método de dispositivo:

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Adicione o seguinte código para agendar o trabalho para atualizar o twin:

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Salve e feche o arquivo **scheduleJobService.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **simDevice** , execute o seguinte comando para começar a escutar o método direto reinicialização.

    ```
    node simDevice.js
    ```

2. No prompt de comando-na pasta **scheduleJobService** , execute o seguinte comando para acionar a reinicialização remota e a consulta para o twin dispositivo localizar a última hora de reinicializar.

    ```
    node scheduleJobService.js
    ```

3. Você verá a saída do dispositivo e aplicativos de back-end.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho agendar um método direto para um dispositivo e a atualização das propriedades do twin dispositivo.

Para continuar a introdução ao IoT Hub e padrões de gerenciamento de dispositivo como remoto sobre a atualização do firmware air, consulte:

[Tutorial: Como fazer uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao IoT Hub, consulte [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx