<properties
 pageTitle="Como fazer uma atualização de firmware | Microsoft Azure"
 description="Este tutorial mostra como fazer uma atualização de firmware"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Tutorial: Como fazer um firmware update (prévia)

## <a name="introduction"></a>Introdução
A [Introdução ao gerenciamento de dispositivo] [ lnk-dm-getstarted] tutorial, você viu como usar o [twin dispositivo] [ lnk-devtwin] e [nuvem para dispositivo (C2D) métodos] [ lnk-c2dmethod] primitivos para reinicializar remotamente um dispositivo. Este tutorial usa os mesmos primitivos IoT Hub e fornece orientação e mostra como fazer uma atualização de firmware simulada de ponta a ponta.  Esse padrão é usado na implementação de atualização do firmware para a amostra de dispositivo Intel Edison.

Este tutorial mostra como para:

- Crie um aplicativo de console que chama o método direto firmwareUpdate do dispositivo simulado por meio de seu hub IoT.
- Crie um dispositivo simulado que implementa um método direto de firmwareUpdate que passa por um processo de vários estágio que aguarda para baixar a imagem do firmware, a imagem de firmware de downloads e finalmente aplica imagem de firmware ésimo.  Em toda a realização de cada estágio o dispositivo utilizando o twin relatados propriedades atualizar progresso.

No final deste tutorial, você tem dois aplicativos de console node:

**dmpatterns_fwupdate_service.js**, que chama um método direto no dispositivo simulado, exibe a resposta e periodicamente (cada 500 ms) exibe o twin dispositivo atualizado relatados propriedades.

**dmpatterns_fwupdate_device.js**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente, recebe um método direto firmwareUpdate, é executado por meio de um processo de vários estado para simular uma atualização de firmware, incluindo: aguardando o download de imagem, baixando a nova imagem e finalmente aplicação da imagem.


Para concluir este tutorial, você precisa do seguinte:

Node versão 0.12.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node para este tutorial em Windows ou Linux.

Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

Siga o artigo [Introdução ao gerenciamento de dispositivos](iot-hub-device-management-get-started.md) para criar o seu hub IoT e obter sua cadeia de conexão.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console Node que responde a um método direto chamado na nuvem, que aciona uma atualização de firmware do dispositivo simulado e usa o twin dispositivo relatado propriedades para habilitar dispositivo twin consultas identificar dispositivos e quando eles reiniciado pela última vez.

1. Crie uma nova pasta vazia chamada **manageddevice**.  Na pasta **manageddevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **manageddevice** , execute o seguinte comando para instalar o **azure-iot-device@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **dmpatterns_fwupdate_device.js** na pasta **manageddevice** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e usá-lo para criar um cliente do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione o seguinte função que será usada para atualizar twin relatados propriedades

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Adicione as seguintes funções que simular a baixar e aplicar da imagem de firmware.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Adicione o seguinte função que irá atualizar o status de atualização de firmware através o twin relatado propriedades aguardando o download.  Normalmente, dispositivos são informados de uma atualização disponível e um administrador o dispositivo para iniciar o download e aplicar a atualização de causas de política definida.  Isso é onde a lógica para habilitar essa política seria executado.  Para simplificar, estamos atrasar para 4 segundos e prosseguir com o download da imagem de firmware. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Adicione o seguinte função que irá atualizar o status de atualização de firmware através o twin relatados propriedades para baixar a imagem de firmware.  Ela segue por simular um download firmware e finalmente atualiza o status de atualização de firmware para informar de um download sucesso ou falha.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Adicione o seguinte função que irá atualizar o status de atualização de firmware através o twin relatados propriedades para aplicação da imagem de firmware.  Ela segue por simular uma aplicação da imagem de firmware e finalmente atualiza o status de atualização de firmware para informar de uma aplicar sucesso ou falha.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Adicione a seguinte functoin que lidar com o método de firmwareUpdate e iniciar o processo de atualização de firmware de vários estágios.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Finalmente, adicione o seguinte código que se conecta ao hub IoT como um dispositivo, 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Disparar uma atualização de firmware remoto do dispositivo usando um método direto 

Nesta seção, você criar um aplicativo de console Node que inicia uma atualização de firmware remoto em um dispositivo usando um método direto e usa consultas de twin do dispositivo para obter o status da atualização do firmware active periodicamente nesse dispositivo.


1. Crie uma nova pasta vazia chamada **triggerfwupdateondevice**.  Na pasta **triggerfwupdateondevice** , crie um arquivo de package.json usando o seguinte comando no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```
    
2. No seu prompt de comando na pasta **triggerfwupdateondevice** , execute o seguinte comando para instalar o pacote de SDK do dispositivo de **iothub do azure** e o **azure-iot-dispositivo-mqtt** pacote:

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Usando um editor de texto, crie um novo arquivo de **dmpatterns_getstarted_service.js** na pasta **triggerfwupdateondevice** .

4. Adicione o seguinte 'exige' instruções no início do arquivo **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de espaço reservado:

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Adicione a seguinte função para localizar e exibir o valor da firmwareUpdate relatado propriedade.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Adicione a seguinte função para chamar o método firmwareUpdate para reiniciar o dispositivo de destino:

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Finalmente, adicione a seguinte função código para iniciar o firmware Atualizar sequência e iniciar periodicamente mostrando o twin relatado propriedades:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Salve e feche o arquivo **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **manageddevice** , execute o seguinte comando para começar a escutar o método direto reinicialização.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. No prompt de comando-na pasta **triggerfwupdateondevice** , execute o seguinte comando para acionar a reinicialização remota e a consulta para o twin dispositivo localizar a última hora de reinicializar.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Você verá reagir ao método direto imprimindo a mensagem

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um método direto para acionar uma atualização de firmware remoto em um dispositivo e periodicamente usado o twin relatados propriedades para entender o andamento do firmware do processo de atualização.  

Para saber como estender sua IoT chamadas de método de solução e cronograma em vários dispositivos, consulte a [agenda e trabalhos de transmissão] [ lnk-tutorial-jobs] tutorial.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
