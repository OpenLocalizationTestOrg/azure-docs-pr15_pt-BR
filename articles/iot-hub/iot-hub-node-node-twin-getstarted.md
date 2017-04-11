<properties
    pageTitle="Introdução ao gêmeos | Microsoft Azure"
    description="Este tutorial mostra como usar gêmeos"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>Tutorial: Introdução ao Gêmeos de dispositivo (prévia)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, você terá dois aplicativos de console node:

* **AddTagsAndQuery.js**, um aplicativo de Node devem a ser executado do back-end, que adiciona marcas e consultas Gêmeos de dispositivo.
* **TwinSimulatedDevice.js**, um aplicativo de Node que simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente, relatórios e sua condição de conectividade.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos de dispositivo e back-end.

Para concluir este tutorial, você precisa do seguinte:

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criar um aplicativo de console Node que adiciona metadados local para o twin associado ao **myDeviceId**. Ele e consultas os gêmeos armazenados no hub do selecionando os dispositivos localizadas nos EUA e, em seguida, aquelas que uma conexão de celular de relatório.

1. Crie uma nova pasta vazia chamada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp** , crie um novo arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **addtagsandqueryapp** , execute o seguinte comando para instalar o pacote do **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **AddTagsAndQuery.js** na pasta **addtagsandqueryapp** .

4. Adicione o seguinte código para o arquivo **AddTagsAndQuery.js** e substitua o espaço reservado de **{cadeia de conexão de serviço}** com a cadeia de conexão que você copiou quando você criou seu hub:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    O objeto de **registro** expõe todos os métodos necessários para interagir com Gêmeos de dispositivo do serviço. O código anterior primeiro inicializa o objeto de **registro** , e em seguida, recupera o twin para **myDeviceId**e finalmente atualiza suas marcas com as informações de local desejado.

    Após a atualização as marcas ele chama a função **queryTwins** .

7. Adicione o seguinte código no final da **AddTagsAndQuery.js** para implementar a função **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    O código anterior executa duas consultas: o primeiro seleciona somente os Gêmeos de dispositivo de dispositivos localizados na fábrica **Redmond43** , e a segunda refina a consulta para selecionar apenas os dispositivos que também estão conectados por meio de rede celular.

    Observe que o código anterior, quando ela cria o objeto de **consulta** , especifica um número máximo de documentos devolvidos. O objeto de **consulta** contém uma propriedade booleana **hasMoreResults** que você pode usar para chamar os métodos de **nextAsTwin** várias vezes para recuperar todos os resultados. Um método chamado **próximo** está disponível para os resultados que não estão Gêmeos de dispositivo por exemplo, os resultados de consultas de agregação.

8. Execute o aplicativo com:

        node AddTagsAndQuery.js

    Você deve ver um dispositivo nos resultados da consulta pedindo para todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que usam uma rede celular.

    ![][1]

Na próxima seção, você criar um aplicativo de dispositivo que relata as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo de dispositivo

Nesta seção, você criar um Node aplicativo de console que se conecta ao seu hub como **myDeviceId**e, em seguida, atualiza seu twin relatados propriedades contendo as informações que ele está conectado usando uma rede celular.

> [AZURE.NOTE] No momento, Gêmeos de dispositivo são acessíveis somente a partir de dispositivos que se conectam a IoT Hub usando o protocolo MQTT. Consulte o [suporte de MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.

1. Crie uma nova pasta vazia chamada **reportconnectivity**. Na pasta **reportconnectivity** , crie um novo arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **reportconnectivity** , execute o seguinte comando para instalar o pacote do **azure-iot-dispositivo-mqtt** e a **azure-iot-dispositivo**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **ReportConnectivity.js** na pasta **reportconnectivity** .

4. Adicione o seguinte código para o arquivo **ReportConnectivity.js** e substitua o espaço reservado de **{cadeia de conexão do dispositivo}** com a cadeia de conexão que você copiou quando você criou a identidade do dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    O objeto de **cliente** expõe todos os métodos que você precisa para interagir com Gêmeos de dispositivo do dispositivo. O código anterior, depois que ele inicializa o objeto de **cliente** , recupera o twin para **myDeviceId** e atualiza sua propriedade relatada com as informações de conectividade.

5. Executar o aplicativo de dispositivo

        node ReportConnectivity.js

    Você verá a mensagem `twin state reported`.

6. Agora que o dispositivo relatado suas informações de conectividade, ela deverá aparecer em ambas as consultas. Voltar na pasta **addtagsandqueryapp** e execute as consultas novamente:

        node AddTagsAndQuery.js

    Esse tempo **myDeviceId** deverá aparecer em ambos os resultados de consulta.

    ![][3]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um hub IoT novo no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você adicionou o dispositivo metadados como marcas de um aplicativo de back-end e escreveu um aplicativo de dispositivo simulado às informações de conectividade de dispositivo de relatório no twin do dispositivo. Você também aprendeu a essas informações usando o Hub de IoT linguagem de consulta do tipo SQL da consulta.

Use os seguintes recursos para saber como:

- Enviar telemetria de dispositivos com a [Introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
- Configurar dispositivos usando as propriedades desejadas do twin com as [propriedades desejadas para configurar dispositivos de usar] [ lnk-twin-how-to-configure] tutorial,
- controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado por usuário), com os [métodos diretos de uso] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md