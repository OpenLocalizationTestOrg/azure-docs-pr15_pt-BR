<properties
    pageTitle="Use telemetria dinâmica | Microsoft Azure"
    description="Siga este tutorial para aprender a usar telemetria dinâmica com a solução de pré-configurado monitoramento remota."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Usar telemetria dinâmica com a solução pré-configurada monitoramento remota

## <a name="introduction"></a>Introdução

Telemetria dinâmica permite visualizar qualquer telemetria enviada para a solução de pré-configurado monitoramento remota. Os dispositivos simulados implantar com a solução pré-configurado enviam telemetria temperatura e umidade, que você pode visualizar no painel. Se você personalizar os dispositivos simulados existentes, cria novos dispositivos simulados ou conectar dispositivos físicos à solução pré-configurado que você pode enviar outros valores de telemetria como a temperatura externa, RPM ou windspeed. Em seguida, você pode visualizar essa telemetria adicional no painel.

Este tutorial usa um dispositivo simulado simples Node que você pode modificar facilmente para experimentar telemetria dinâmica.

Para concluir este tutorial, você precisará:

- Uma assinatura ativa do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
- [Node] [ lnk-node] versão 0.12.x ou posterior.

Você pode concluir este tutorial em qualquer sistema operacional, como o Windows ou Linux, onde você poderá instalar Node.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurar o dispositivo simulado Node

1. No painel de monitoramento remoto, clique em **+ para adicionar um dispositivo** e adicione um dispositivo personalizado. Tome nota do IoT Hub hostname, id do dispositivo e chave de dispositivo. Você precisa-las posteriormente neste tutorial quando você se prepara o aplicativo de cliente do dispositivo remote_monitoring.js.

2. Certifique-se de que a versão Node 0.12.x ou posterior está instalado no seu computador de desenvolvimento. Executar `node --version` em um prompt de comando ou em um shell para verificar a versão. Para obter informações sobre como usar um gerente de pacote para instalar Node no Linux, consulte [Instalar Node via Gerenciador de pacote][node-linux].

3. Quando você tiver instalado o Node, clonar a versão mais recente dos [sdks do azure-iot] [ lnk-github-repo] repositório à sua máquina de desenvolvimento. Sempre use a ramificação **mestre** para a versão mais recente das bibliotecas e amostras.

4. Da sua cópia local dos [sdks do azure-iot] [ lnk-github-repo] repositório, copiar os dois seguintes arquivos da pasta Amostras/de dispositivo de nó para uma pasta vazia em sua máquina de desenvolvimento:

  - Packages.JSON
  - remote_monitoring.js

5. Abra o arquivo remote_monitoring.js e procure a seguinte definição de variável:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Substitua a cadeia de caracteres de conexão do dispositivo **[cadeia de conexão de dispositivo do IoT Hub]** . Use os valores para seu IoT Hub hostname, id do dispositivo e chave de dispositivo que você fez uma anotação na etapa 1. Uma cadeia de conexão do dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se o seu nome de host do IoT Hub é **contoso** e sua id de dispositivo é **mydevice**, a cadeia de caracteres de conexão é semelhante ao seguinte:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Salve o arquivo. Execute os seguintes comandos em um shell ou o prompt de comando na pasta que contém esses arquivos para instalar os pacotes necessários e execute o aplicativo de exemplo:

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observar telemetria dinâmica em ação

O painel mostra a temperatura e umidade telemetria de dispositivos simuladas existentes:

![Painel padrão][image1]

Se você selecionar dispositivo simulado Node executados na seção anterior, você verá temperatura, umidade e telemetria temperatura externa:

![Adicionar temperatura externa ao painel][image2]

A solução de monitoramento remota automaticamente detecta o tipo de telemetria adicionais temperatura externa e adiciona o gráfico no painel.

## <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

A próxima etapa é substituir a telemetria gerada pelo dispositivo simulado Node com um novo conjunto de valores:

1. Pare o dispositivo simulado Node digitando **Ctrl + C** no prompt de comando ou shell.

2. No arquivo remote_monitoring.js, você pode ver os valores de dados básicos para a temperatura existente, umidade e telemetria temperatura externa. Adicione um valor de dados básicos para **rpm** da seguinte maneira:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. O dispositivo simulado Node usa a função de **generateRandomIncrement** no arquivo remote_monitoring.js para adicionar um incremento aleatório para os valores de dados básicos. Tornar aleatório o valor de **rpm** , adicionando uma linha de código após as randomizations existentes da seguinte maneira:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Adicione o novo valor de rpm a carga JSON que o dispositivo envia a IoT Hub:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Execute o dispositivo simulado Node usando o seguinte comando:

    ```
    node remote_monitoring.js
    ```

6. Observe o novo tipo de telemetria RPM que é exibida no gráfico no painel:

![Adicionar RPM ao painel][image3]

> [AZURE.NOTE] Talvez você precise desabilitar e depois habilitar o dispositivo Node na página **dispositivos** no painel de controle para ver a alteração imediatamente.

## <a name="customize-the-dashboard-display"></a>Personalizar a exibição de painel

A mensagem de **Informações do dispositivo** pode incluir metadados sobre o dispositivo pode enviar a IoT Hub de telemetria. Esses metadados podem especificar os tipos de telemetria que envia o dispositivo. Modificar o valor de **deviceMetaData** no arquivo remote_monitoring.js para incluir uma definição de **telemetria** após a definição de **comandos** . O trecho de código a seguir mostra a definição de **comandos** (certifique-se de adicionar um `,` após a definição de **comandos** ):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] A solução de monitoramento remota usa uma correspondência de maiusculas e minúsculas para comparar a definição de metadados com dados no fluxo de telemetria.

Adicionando uma definição de **telemetria** conforme mostrado no trecho de código anterior não altera o comportamento do painel de controle. No entanto, os metadados também podem incluir um atributo **DisplayName** para personalizar a exibição no painel de controle. Atualize a definição de metadados de **telemetria** conforme mostrado no seguinte trecho:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

A captura de tela a seguir mostra como essa alteração modifica a legenda do gráfico no painel:

![Personalizar a legenda do gráfico][image4]

> [AZURE.NOTE] Talvez você precise desabilitar e depois habilitar o dispositivo Node na página **dispositivos** no painel de controle para ver a alteração imediatamente.

## <a name="filter-the-telemetry-types"></a>Filtrar os tipos de telemetria

Por padrão, o gráfico no painel mostra todas as séries de dados no fluxo de telemetria. Você pode usar os metadados de **Informações do dispositivo** para suprimir a exibição dos tipos de telemetria específico no gráfico. 

Para tornar o gráfico para mostrar apenas telemetria temperatura e umidade, omita **ExternalTemperature** dos metadados **telemetria** **Informações do dispositivo** , da seguinte maneira:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

A **Temperatura ao ar livre** não é mais exibido no gráfico:

![Filtrar a telemetria no painel][image5]

Essa alteração afeta apenas a exibição de gráfico. Os valores de dados **ExternalTemperature** ainda são armazenados e disponibilizados para qualquer processamento de back-end.

> [AZURE.NOTE] Talvez você precise desabilitar e depois habilitar o dispositivo Node na página **dispositivos** no painel de controle para ver a alteração imediatamente.

## <a name="handle-errors"></a>Tratar erros

Para um fluxo de dados exibir no gráfico, seu **tipo** nos metadados de **Informações do dispositivo** deve corresponder ao tipo de dados dos valores de telemetria. Por exemplo, se os metadados Especifica que o **tipo** de dados de umidade é **int** e **double** for encontrada no fluxo de telemetria então a telemetria umidade não exibir no gráfico. No entanto, os valores de **umidade** ainda são armazenados e disponibilizados para qualquer processamento de back-end.

## <a name="next-steps"></a>Próximas etapas

Agora que você viu como usar telemetria dinâmica, você pode saber mais sobre como as soluções pré-configuradas usam informações do dispositivo: [monitoramento de metadados de informações de dispositivo no controle remoto solução pré-configurada][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
