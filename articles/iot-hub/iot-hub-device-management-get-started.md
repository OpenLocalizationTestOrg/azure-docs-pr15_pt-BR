<properties
 pageTitle="Introdução ao gerenciamento de dispositivo | Microsoft Azure"
 description="Este tutorial mostra como começar com o gerenciamento de dispositivo no Hub do Azure IoT"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>Tutorial: Introdução ao gerenciamento de dispositivo (prévia)

## <a name="introduction"></a>Introdução
Aplicativos em nuvem IoT podem usar primitivos no Azure IoT Hub, ou seja o twin do dispositivo e métodos diretos, para iniciar remotamente e monitorar ações de gerenciamento de dispositivo em dispositivos.  Este artigo fornece orientação e o código para como um aplicativo de nuvem IoT e um dispositivo funcionam juntos para iniciar e monitorar a reinicialização do dispositivo remoto usando IoT Hub.

Para iniciar e monitorar ações de gerenciamento de dispositivo em seus dispositivos de um aplicativo baseado em nuvem back-end remotamente, use primitivos Azure IoT Hub como [twin dispositivo] [ lnk-devtwin] e [nuvem para dispositivo (C2D) métodos][lnk-c2dmethod]. Este tutorial mostra como um aplicativo de back-end e um dispositivo podem trabalhar juntos para permitir que você iniciar e monitorar a reinicialização do dispositivo remoto do IoT Hub.

Use um método de C2D para iniciar ações de gerenciamento de dispositivo (como reinicialização, redefinição de fábrica e atualização de firmware) de um aplicativo de back-end na nuvem. O dispositivo é responsável por:

- Manipulando a solicitação de método enviada a partir do IoT Hub.
- Iniciando a ação específica do dispositivo correspondente no dispositivo.
- Fornecer atualizações de status por meio do twin dispositivo relatado propriedades IoT Hub.

Você pode usar um aplicativo de back-end na nuvem para executar consultas de twin dispositivo para relatar o progresso de suas ações de gerenciamento de dispositivo.

Este tutorial mostra como para:

- Use o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no seu hub IoT.
- Crie um dispositivo simulado que tem um método direto que permite a reinicialização que pode ser chamada pela nuvem.
- Crie um aplicativo de console que chama o método de direta de reinicialização do dispositivo simulado por meio de seu hub IoT.

No final deste tutorial, você tem dois aplicativos de console node:

**dmpatterns_getstarted_device.js**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente, recebe um método direto reinicialização, simula uma reinicialização física e o horário para a última reinicialização de relatórios.

**dmpatterns_getstarted_service.js**, que chama um método direto no dispositivo simulado, exibe a resposta e exibe o twin dispositivo atualizado relatado propriedades.

Para concluir este tutorial, você precisa do seguinte:

Node versão 0.12.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node para este tutorial em Windows ou Linux.

Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console Node que responde a um método direto chamado na nuvem, que aciona a reinicialização do dispositivo simulado e usa o twin dispositivo relatado propriedades para habilitar dispositivo twin consultas identificar dispositivos e quando eles reiniciado pela última vez.

1. Crie uma nova pasta vazia chamada **manageddevice**.  Na pasta **manageddevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **manageddevice** , execute o seguinte comando para instalar o **azure-iot-device@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **dmpatterns_getstarted_device.js** na pasta **manageddevice** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e usá-lo para criar um cliente do dispositivo.  Substitua a cadeia de conexão com a cadeia de caracteres de conexão do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para implementar o método direto no dispositivo

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Abra a conexão para o seu hub IoT e iniciar o ouvinte método direto:

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Salve e feche o arquivo **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota do dispositivo usando um método direto 

Nesta seção, você criar um aplicativo de console Node que inicia uma reinicialização remota em um dispositivo usando um método direto e usa consultas de twin do dispositivo para localizar a última vez reinicialização para esse dispositivo.

1. Crie uma nova pasta vazia chamada **triggerrebootondevice**.  Na pasta **triggerrebootondevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **triggerrebootondevice** , execute o seguinte comando para instalar o **azure-iothub@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. Usando um editor de texto, crie um novo arquivo de **dmpatterns_getstarted_service.js** na pasta **triggerrebootondevice** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de espaço reservado:

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Adicione a seguinte função para chamar o método de dispositivo para reiniciar o dispositivo de destino:

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adicione a seguinte função para consultar o dispositivo e obter a última vez reinicialização:

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Adicione o seguinte código para chamar as funções que acionará o método direto reinicialização e a consulta pela última vez reinicialização:

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Salve e feche o arquivo **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **manageddevice** , execute o seguinte comando para começar a escutar o método direto reinicialização.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. No prompt de comando-na pasta **triggerrebootondevice** , execute o seguinte comando para acionar a reinicialização remota e a consulta para o twin dispositivo localizar a última hora de reinicializar.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Você verá reagir ao método direto imprimindo a mensagem

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender ações de gerenciamento de dispositivo

Suas soluções IoT podem expandir o conjunto de padrões de gerenciamento de dispositivo definido ou habilitar padrões personalizados por meio do uso a twin do dispositivo e primitivos de método de C2D. Outras ações de gerenciamento de dispositivo exemplos de redefinição de fábrica, atualização firmware, atualização de software, gerenciamento de energia, gerenciamento de conectividade e de rede e criptografia de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção de dispositivo

Normalmente, você configurar dispositivos para executar ações em um horário minimiza interrupções e tempo de inatividade.  Janelas de manutenção de dispositivo são um padrão comumente usado para definir a hora quando um dispositivo deve atualizar sua configuração. As soluções de back-end podem usar as propriedades desejadas de twin o dispositivo para definir e ativar uma política em seu dispositivo que permite que uma janela de manutenção. Quando um dispositivo recebe a política de janela de manutenção, ele pode usar a propriedade relatada do twin dispositivo para informar o status da política. O aplicativo de back-end pode usar consultas de twin dispositivo ateste conformidade de dispositivos e cada política.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um método direto para acionar uma reinicialização remota em um dispositivo, usado o twin dispositivo relatado propriedades informar a última vez reinicialização do dispositivo e consultado para o twin dispositivo descobrir a última vez reinicialização do dispositivo da nuvem.

Para continuar a introdução ao IoT Hub e padrões de gerenciamento de dispositivo como remoto sobre a atualização do firmware air, consulte:

[Tutorial: Como fazer uma atualização de firmware][lnk-fwupdate]

Para saber como estender sua IoT chamadas de método de solução e cronograma em vários dispositivos, consulte a [agenda e trabalhos de transmissão] [ lnk-tutorial-jobs] tutorial.

Para continuar a introdução ao IoT Hub, consulte [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx