<properties
    pageTitle="Simular um dispositivo com o SDK do Gateway IoT | Microsoft Azure"
    description="Azure IoT Gateway SDK explicação passo a passo usando o Windows para ilustrar telemetria envio de um dispositivo simulado usando o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>Azure IoT Gateway SDK (beta) – envie mensagens de dispositivo na nuvem com um dispositivo simulado usando o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Antes de começar, faça o seguinte:

- [Configurar o ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Windows.
- [Criar um hub IoT] [ lnk-create-hub] na sua assinatura do Azure, será necessário o nome do seu hub para concluir este passo a passo. Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- Adicionar dois dispositivos ao seu hub IoT e tome nota dos seus ids e chaves de dispositivo. Você pode usar o [Gerenciador de dispositivo ou Gerenciador de iothub] [ lnk-explorer-tools] ferramenta para adicionar seus dispositivos hub do IoT que você criou na etapa anterior e recuperar suas chaves.

Para criar o exemplo:

1. Abra um prompt de comando do **Prompt de comando do desenvolvedor para VS2015** .
2. Navegue até a pasta raiz em sua cópia local do repositório do **azure-iot-gateway-sdk** .
3. Executar o **ferramentas\\build.cmd** script. Este script cria um arquivo de solução do Visual Studio, cria a solução e executa os testes. Você pode encontrar a solução do Visual Studio na pasta **build** na sua cópia local do repositório do **azure-iot-gateway-sdk** .

Para executar o exemplo:

Em um editor de texto, abra o arquivo **amostras\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** em sua cópia local do repositório do **azure-iot-gateway-sdk** . Este arquivo configura os módulos no gateway exemplo:

- O módulo **IoTHub** se conecta ao seu hub IoT. Você deve configurá-lo para enviar dados para o seu hub IoT. Especificamente, defina o valor de **IoTHubName** para o nome do seu hub IoT e defina o valor de **IoTHubSuffix** como **devices.net do azure**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Observe que no momento, apenas "HTTP" compartilhará uma conexão de TCP para todas as mensagens do dispositivo. Se você definir o valor para "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada IoT hub para cada dispositivo.
- O módulo de **mapeamento** mapeia os endereços MAC seus dispositivos simulada para suas identificações de dispositivo IoT Hub. Certifique-se de que os valores de **deviceId** correspondem às ids dos dois dispositivos que você adicionou a seu hub IoT e que os valores de **deviceKey** contêm as chaves de seus dispositivos de duas.
- Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como seus endereços MAC corresponderem no módulo de **mapeamento** .
- O módulo do **agente de log** registra sua atividade de gateway em um arquivo.
- Os valores de **caminho de módulo** mostrados a seguir presumem que você clonar repositório IoT SDK de Gateway para a raiz da sua unidade **c:** . Se você baixou-lo para outro local, você precisa ajustar os valores de **caminho de módulo** adequadamente.
- A matriz de **links** na parte inferior do arquivo JSON conecta os módulos **BLE1** e **BLE2** para o módulo de **mapeamento** e o módulo de **mapeamento** no módulo **IoTHub** . Ele também garante que todas as mensagens são registradas pelo módulo do **agente de log** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

Salve as alterações feitas no arquivo de configuração.

Para executar o exemplo:

1. No prompt de comando, navegue até a pasta raiz da sua cópia local do repositório do **azure-iot-gateway-sdk** .
2. Execute o seguinte comando:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. Você pode usar o [Gerenciador de dispositivo ou Gerenciador de iothub] [ lnk-explorer-tools] ferramenta para monitorar as mensagens que IoT hub recebe do gateway.


## <a name="next-steps"></a>Próximas etapas

Se você quiser obter uma compreensão mais avançada do SDK do Gateway IoT e experimentar alguns exemplos de código, visite os seguintes tutoriais do desenvolvedor e recursos:

- [Enviar mensagens de dispositivo à nuvem do dispositivo real com o SDK do Gateway IoT][lnk-physical-device]
- [Gateway do Azure IoT SDK][lnk-gateway-sdk]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 