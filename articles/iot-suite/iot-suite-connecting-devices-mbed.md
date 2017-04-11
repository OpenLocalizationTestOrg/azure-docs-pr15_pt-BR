<properties
   pageTitle="Conecte um dispositivo usando C em mbed | Microsoft Azure"
   description="Descreve como conectar um dispositivo para a solução de monitoramento remota da Azure IoT pacote pré-configurado usando um aplicativo escrito em C em execução em um dispositivo de mbed."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Conectar o dispositivo para a solução de pré-configurado monitoramento remota (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Criar e executar a solução de exemplo C

As instruções a seguir descrevem as etapas para conectar um [Habilitados para mbed Freescale FRDM-K64F] [ lnk-mbed-home] dispositivo para a solução de monitoramento remoto.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Conectar o dispositivo de mbed à sua máquina de rede e desktop

1. Conecte o dispositivo de mbed à sua rede usando um cabo Ethernet. Esta etapa é necessária porque o aplicativo de amostra requer acesso à internet.

2. Consulte [Introdução ao mbed] [ lnk-mbed-getstarted] para conectar seu dispositivo mbed ao seu computador desktop.

3. Se seu computador desktop estiver executando o Windows, consulte [Configuração de PC] [ lnk-mbed-pcconnect] para configurar o acesso de porta serial para o seu dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Criar um projeto de mbed e importar o código de amostra

1. No navegador da web, vá para o [site de desenvolvedor](https://developer.mbed.org/)do mbed.org. Se você ainda não inscreveu, você verá uma opção para criar uma conta (ele é gratuito). Caso contrário, faça logon com suas credenciais de conta. Clique em **compilador** no canto superior direito da página. Esta ação traz a você para a interface do *espaço de trabalho* .

2. Verifique se a plataforma de hardware que você está usando aparece no canto superior direito da janela, ou clique no ícone no canto direito para selecionar a plataforma de hardware.

3. Clique em **Importar** no menu principal. Clique em **clique aqui** para importar de link de URL ao lado do logotipo do globo mbed.

    ![][6]

4. Na janela pop-up, insira o link para a amostra código https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ e em seguida, clique em **Importar**.

    ![][7]

5. Você pode ver na janela de compilador mbed que também importar este projeto importa várias bibliotecas. Algumas são fornecidas e mantidas pela equipe do Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), enquanto outros estão disponíveis no catálogo de bibliotecas de mbed de bibliotecas de terceiros.

    ![][8]

6. Abra o arquivo remote_monitoring\remote_monitoring.c e localize o seguinte código no arquivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Substituir [Id do dispositivo] e [dispositivo chave], com dados do seu dispositivo para habilitar o programa de exemplo para se conectar ao seu hub IoT. Use o nome de host do Hub IoT para substituir o [nome do IoTHub] e espaços reservados [sufixo IoTHub, ou seja azure-devices.net]. Por exemplo, à se seu nome de host do Hub IoT for **contoso.azure-devices.net**, o **contoso** é o **hubName** e tudo depois que ele é o **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Percorrer o código

Se você estiver interessado em funcionamento do programa, esta seção descreve algumas partes principais de código de exemplo. Se você quiser apenas executar o código, saltar para [criar e executa o programa](#buildandrun).

#### <a name="defining-the-model"></a>Definindo o modelo

Este exemplo usa o [serializador] [ lnk-serializer] biblioteca para definir um modelo que especifica as mensagens que o dispositivo pode enviar a IoT Hub e receber do IoT Hub. Neste exemplo, **Contoso** namespace define um modelo de **termostato** que especifica os dados de telemetria **temperatura**, **ExternalTemperature**e **umidade** junto com metadados como a id do dispositivo, propriedades do dispositivo e os comandos que o dispositivo responde a:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Relacionados a definição do modelo são as definições para os comandos **SetTemperature (DefinirTemperatura)** e **SetHumidity** que o dispositivo responde a:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Conectando o modelo para a biblioteca

As funções **sendMessage** e **IoTHubMessage** são código clichê para envio de telemetria do dispositivo e conectar mensagens do IoT Hub manipuladores de comando.

#### <a name="the-remotemonitoringrun-function"></a>A função remote_monitoring_run

Função **principal** do programa invoca a função de **remote_monitoring_run** quando o aplicativo é iniciado executar o comportamento do dispositivo como um cliente de dispositivo IoT Hub. Esta função **remote_monitoring_run** principalmente consiste em pares aninhados de funções:

- **plataforma\_inicialização** e **plataforma\_deinit** executar operações de inicialização e desligamento específico da plataforma.
- **serializador\_inicialização** e **serializador\_deinit** inicializar e eliminação inicializar a biblioteca de serializador.
- **IoTHubClient\_criar** e **IoTHubClient\_Destroy** criar um identificador de cliente, **iotHubClientHandle**, usando as credenciais do dispositivo para conexão com o seu hub IoT.

Na seção principal da função **remote_monitoring_run** , o programa executa as seguintes operações usando a alça de **iotHubClientHandle** :

- Cria uma instância do modelo termostato Contoso e configura os retornos de chamada de mensagem para os dois comandos.
- Envia informações sobre o dispositivo em si, incluindo os comandos que ele suporta, ao seu hub de IoT usando a biblioteca de serializador. Quando o hub recebe essa mensagem, ele altera o status de dispositivo no painel de **pendente** **em execução**.
- Inicia um loop **enquanto** que envia temperatura, temperatura externa e valores de umidade IoT hub cada segundo.

Para referência, aqui está uma amostra de mensagem de **DeviceInfo** enviada IoT hub na inicialização:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Para referência, aqui está uma amostra de mensagem de **telemetria** enviada para IoT Hub:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Para referência, aqui está um exemplo de **que comando** recebido do IoT Hub:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Criar e executar o programa

1. Clique em **Compilar** para criar o programa. Você pode com segurança ignorar quaisquer avisos, mas se a compilação gera erros, corrigi-los antes de continuar.

2. Se a compilação for bem-sucedida, o site do compilador mbed gera um arquivo. bin com o nome do seu projeto e downloads-lo em sua máquina local. Copie o arquivo. bin no dispositivo. Salvar o arquivo. bin no dispositivo faz com que o dispositivo reiniciar e executar o programa contido no arquivo. bin. Você pode reiniciar o programa manualmente a qualquer momento pressionando o botão de redefinição do dispositivo mbed.

3. Conectar-se ao dispositivo usando um aplicativo de cliente SSH, como acabamento. Você pode determinar a porta serial que usa o seu dispositivo, verificando o Gerenciador de dispositivos do Windows.

    ![][11]

4. No acabamento, clique no tipo de conexão **Serial** . O dispositivo normalmente se conecta ao transmissão 9600, insira 9600 na caixa **velocidade** . Clique em **Abrir**.

5. O programa inicia a execução. Talvez você precise redefinir o quadro (pressione CTRL + Break ou pressione o botão de redefinição da placa) se o programa não iniciar automaticamente quando você se conecta.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
