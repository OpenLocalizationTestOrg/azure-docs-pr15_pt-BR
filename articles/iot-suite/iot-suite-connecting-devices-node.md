<properties
   pageTitle="Conecte um dispositivo usando Node | Microsoft Azure"
   description="Descreve como conectar um dispositivo para a solução de monitoramento remota da Azure IoT pacote pré-configurado usando um aplicativo escrito em Node."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conectar o dispositivo para a solução de pré-configurado monitoramento remota (node)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Criar e executar a solução de exemplo Node

1. Para clonar o repositório de GitHub do *Microsoft Azure IoT SDKs* e instale o *Microsoft Azure IoT dispositivo SDK para Node* no seu ambiente de desktop, siga o [preparar seu ambiente de desenvolvimento] [ lnk-github-prepare] instruções.

2. Da sua cópia local dos [sdks do azure-iot] [ lnk-github-repo] repositório, copiar os dois seguintes arquivos da pasta Amostras/de dispositivo de nó para uma pasta em seu dispositivo:

  - Packages.JSON
  - remote_monitoring.js

3. Abra o arquivo remote_monitoring.js e procure a seguinte variável:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Substitua a cadeia de caracteres de conexão do dispositivo **[cadeia de conexão de dispositivo do IoT Hub]** . Você pode obter os valores de seu IoT Hub hostname, id do dispositivo e chave de dispositivo no painel solução monitoramento remoto. Uma cadeia de conexão do dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se seu nome de host do IoT Hub é **contoso** e sua id de dispositivo é **mydevice**, a cadeia de caracteres de conexão será semelhante a:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Salve o arquivo. Execute os seguintes comandos no prompt de comando na pasta que contém esses arquivos para instalar os pacotes necessários e execute o aplicativo de exemplo:

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md