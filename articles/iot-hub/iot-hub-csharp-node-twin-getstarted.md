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

No final deste tutorial, você terá um .NET e um aplicativo de console node:

* **AddTagsAndQuery.sln**, um aplicativo .NET devem a ser executado do back-end, que adiciona marcas e consultas Gêmeos de dispositivo.
* **TwinSimulatedDevice.js**, um aplicativo de Node que simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente, relatórios e sua condição de conectividade.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos de dispositivo e back-end.

Para concluir este tutorial, você precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Node versão 0.10.x ou posterior.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criar um aplicativo de console Node que adiciona metadados local para o twin associado ao **myDeviceId**. Ele e consultas os gêmeos armazenados no hub do selecionando os dispositivos localizadas nos EUA e, em seguida, aquelas que uma conexão de celular de relatório.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico Desktop à solução atual usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **AddTagsAndQuery**.

    ![Novo projeto do Visual c# Windows clássico da área de trabalho][img-createapp]

2. No Solution Explorer, clique com botão direito no projeto **AddTagsAndQuery** e clique em **Gerenciar pacotes do Nuget**.

3. Na janela do **Gerenciador de pacotes do Nuget** , certifique-se de que a **versão de pré-lançamento de incluir** está marcada, procurar **microsoft.azure.devices**, selecione **instalar** para instalar o o versão de *pré-lançamento* do **Microsoft.Azure.Devices** empacotar e aceite os termos de uso. Esse procedimento downloads, instalações e adiciona uma referência para o [SDK do serviço do Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget pacote e suas dependências.

    ![Janela de NuGet Package Manager][img-servicenuget]

4. Adicione o seguinte `using` instruções na parte superior do arquivo **Program.cs** :

        using Microsoft.Azure.Devices;

5. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão para o hub de IoT que você criou na seção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **programa** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    A classe **RegistryManager** expõe todos os métodos necessários para interagir com Gêmeos de dispositivo do serviço. O código anterior primeiro inicializa o objeto **registryManager** , e em seguida, recupera o twin para **myDeviceId**e finalmente atualiza suas marcas com as informações de local desejado.

    Após a atualização, ele executa duas consultas: o primeiro seleciona somente os Gêmeos de dispositivo de dispositivos localizados na fábrica **Redmond43** , e a segunda refina a consulta para selecionar apenas os dispositivos que também estão conectados por meio de rede celular.

    Observe que o código anterior, quando ela cria o objeto de **consulta** , especifica um número máximo de documentos devolvidos. O objeto de **consulta** contém uma propriedade booleana **HasMoreResults** que você pode usar para chamar os métodos de **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para os resultados que não estão Gêmeos de dispositivo por exemplo, os resultados de consultas de agregação.

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Executar esse aplicativo e você verá um dispositivo nos resultados da consulta pedindo para todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que usam uma rede celular.

    ![Resultados da consulta na janela][img-addtagapp]

Na próxima seção, você criar um aplicativo de dispositivo que relata as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo de dispositivo

Nesta seção, você criar um Node aplicativo de console que se conecta ao seu hub como **myDeviceId**e, em seguida, atualiza seu twin relatados propriedades contendo as informações que ele está conectado usando uma rede celular.

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

6. Agora que o dispositivo relatado suas informações de conectividade, ela deverá aparecer em ambas as consultas. Execute o aplicativo .NET **AddTagsAndQuery** para executar as consultas novamente. Esse tempo **myDeviceId** deverá aparecer em ambos os resultados de consulta.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um hub IoT novo no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você adicionou o dispositivo metadados como marcas de um aplicativo de back-end e escreveu um aplicativo de dispositivo simulado às informações de conectividade de dispositivo de relatório no twin do dispositivo. Você também aprendeu a essas informações usando o Hub de IoT linguagem de consulta do tipo SQL da consulta.

Use os seguintes recursos para saber como:

- Enviar telemetria de dispositivos com a [Introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
- Configurar dispositivos usando as propriedades desejadas do twin com as [propriedades desejadas para configurar dispositivos de usar] [ lnk-twin-how-to-configure] tutorial,
- controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado por usuário) com os [métodos diretos de uso] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

