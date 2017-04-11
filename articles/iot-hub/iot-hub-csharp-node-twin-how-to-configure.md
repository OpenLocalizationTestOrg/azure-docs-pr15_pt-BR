<properties
    pageTitle="Usar propriedades de twin | Microsoft Azure"
    description="Este tutorial mostra como usar propriedades de twin"
    services="iot-hub"
    documentationCenter=".net"
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

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Tutorial: Usar propriedades desejadas para configurar dispositivos (prévia)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, você terá dois aplicativos de console node:

* **SimulateDeviceConfiguration.js**, um aplicativo de dispositivo simulado que aguarda uma atualização de configuração desejada e reporta o status de um processo de atualização de configuração simulada.
* **SetDesiredConfigurationAndQuery**, um aplicativo de console do .NET devem a ser executado do back-end, que define a configuração desejada em um dispositivo e consultas o processo de atualização de configuração.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos de dispositivo e back-end.

Para concluir este tutorial, você precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

Se você seguiu a [Introdução ao Gêmeos de dispositivo] [ lnk-twin-tutorial] tutorial, você já tem um hub de gerenciamento habilitado do dispositivo e uma identidade de dispositivo chamado **myDeviceId**; e você pode pular para [criar o aplicativo do dispositivo simulado] [ lnk-how-to-configure-createapp] seção.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Criar o aplicativo do dispositivo simulado

Nesta seção, você criar um aplicativo de console Node que se conecta ao seu hub como **myDeviceId**, aguarda uma atualização de configuração desejada e, em seguida, relatórios atualizações sobre o processo de atualização de configuração simulada.

1. Crie uma nova pasta vazia chamada **simulatedeviceconfiguration**. Na pasta **simulatedeviceconfiguration** , crie um novo arquivo de package.json usando o seguinte comando no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No seu prompt de comando na pasta **simulatedeviceconfiguration** , execute o seguinte comando para instalar o pacote do **azure-iot-dispositivo-mqtt** e a **azure-iot-dispositivo**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Usando um editor de texto, crie um novo arquivo de **SimulateDeviceConfiguration.js** na pasta **simulatedeviceconfiguration** .

4. Adicione o seguinte código para o arquivo **SimulateDeviceConfiguration.js** e substitua o espaço reservado de **{cadeia de conexão do dispositivo}** com a cadeia de conexão que você copiou quando você criou a identidade do dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    O objeto de **cliente** expõe todos os métodos necessários para interagir com Gêmeos de dispositivo do dispositivo. O código anterior, depois ele inicializa o objeto de **cliente** , recupera o twin para **myDeviceId**e anexa um manipulador para a atualização em propriedades desejadas. O manipulador verifica se há é uma solicitação de alteração de configuração real comparando os configIds, em seguida, invoca um método que inicia a alteração de configuração.

    Observe que, para simplificar, o código anterior usa um padrão embutido para a configuração inicial. Um aplicativo real provavelmente carregam que a configuração de um armazenamento local.
    
    > [AZURE.IMPORTANT] Eventos de alteração de propriedade desejada sempre são emitidos uma vez na conexão de dispositivo, verifique se há uma alteração real nas propriedades desejadas antes de executar qualquer ação.

5. Adicione os seguintes métodos antes do `client.open()` invocação:

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    O método **initConfigChange** atualiza propriedades relatadas no objeto twin local com a solicitação de atualização de configuração e define o status como **pendente**e atualiza o twin de dispositivo no serviço. Depois de atualizar com êxito o twin, ele simula um processo de execução demorada que termina na execução da **completeConfigChange**. Esse método atualiza propriedades de relatada do twin local definindo o status como **sucesso** e removendo o objeto **pendingConfig** . Em seguida, ele atualiza o twin no serviço.

    Observe que, para salvar a largura de banda, relatados propriedades são atualizadas, especificando somente as propriedades para ser modificada (nomeado **patch** no código acima), em vez de substituir todo o documento.

    > [AZURE.NOTE] Este tutorial não simular a qualquer comportamento atualizações de configuração simultâneas. Alguns processos de atualização de configuração talvez consiga acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros podem ter fila-los e outras pessoas podem rejeitá-las com uma condição de erro. Verifique se a considerar o comportamento desejado para o seu processo de configuração específica e adicionar a lógica apropriada antes de iniciar a alteração de configuração.

6. Execute o aplicativo de dispositivo:

        node SimulateDeviceConfiguration.js

    Você verá a mensagem `retrieved device twin`. Manter o aplicativo em execução.

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você irá criar um aplicativo de console do .NET que atualiza as *propriedades desejadas* no twin associado **myDeviceId** com um novo objeto de configuração de telemetria. Ele consulta os Gêmeos de dispositivo armazenados no hub e mostra a diferença entre as configurações desejadas e relatadas do dispositivo.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico Desktop à solução atual usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **SetDesiredConfigurationAndQuery**.

    ![Novo projeto do Visual c# Windows clássico da área de trabalho][img-createapp]

2. No Solution Explorer, clique com botão direito no projeto **SetDesiredConfigurationAndQuery** e clique em **Gerenciar pacotes do Nuget**.

3. Na janela do **Gerenciador de pacotes do Nuget** , certifique-se de que a **versão de pré-lançamento de incluir** está marcada, procurar **microsoft.azure.devices**, selecione **instalar** para instalar o o versão de *pré-lançamento* do **Microsoft.Azure.Devices** empacotar e aceite os termos de uso. Esse procedimento downloads, instalações e adiciona uma referência para o [SDK do serviço do Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget pacote e suas dependências.

    ![Janela de NuGet Package Manager][img-servicenuget]

4. Adicione o seguinte `using` instruções na parte superior do arquivo **Program.cs** :

        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;

5. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão para o hub de IoT que você criou na seção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **programa** :

        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
            
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired state");

            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }

    O objeto de **registro** expõe todos os métodos necessários para interagir com Gêmeos de dispositivo do serviço. O código anterior, depois que ele inicializa o objeto de **registro** , recupera o twin para **myDeviceId**e atualiza suas propriedades desejadas com um novo objeto de configuração de telemetria.
    Depois disso, cada 10 segundos, ele consulta os gêmeos armazenados no hub e imprime as configurações de telemetria desejado e relatados. Consulte a [linguagem de consulta do IoT Hub] [ lnk-query] para saber como gerar relatórios sofisticados em todos os seus dispositivos.

    > [AZURE.IMPORTANT] Este aplicativo consulta IoT Hub cada 10 segundos para ilustração. Use as consultas para gerar relatórios de voltado para o usuário em vários dispositivos e não para detectar as alterações. Se a sua solução requer em tempo real notificações de eventos de dispositivo usam [mensagens de dispositivo à nuvem][lnk-d2c].

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();

8. Com **SimulateDeviceConfiguration.js** em execução, executar o aplicativo .NET do Visual Studio usando **F5** e você verá a configuração relatada alterar de **sucesso** para **pendente** para o **sucesso** novamente com o novo ativo enviar frequência de cinco minutos em vez de 24 horas.

    > [AZURE.IMPORTANT] Não há um atraso de até um minuto entre a operação de relatório do dispositivo e o resultado da consulta. Isso é permitir a infraestrutura de consulta trabalhar em escala muito alta. Para recuperar exibições consistentes de um único twin usem o método **getDeviceTwin** na classe do **registro** .

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você definir uma configuração desejada como *desejado propriedades* do back-end e escreveu um aplicativo de dispositivo para detectar essa alteração e simular um processo de atualização de várias etapas relatar seu status como *propriedades relatadas* para o twin.

Use os seguintes recursos para saber como:

- Enviar telemetria de dispositivos com a [Introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
- agendar ou executar operações em grandes conjuntos de dispositivos Consulte os [trabalhos de uso para agendar e transmitir operações de dispositivo] [ lnk-schedule-jobs] tutorial.
- controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado por usuário), com os [métodos diretos de uso] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
