<properties
    pageTitle="Usar um dispositivo real com o SDK do Gateway IoT | Microsoft Azure"
    description="Azure IoT Gateway SDK explicação passo a passo usando um dispositivo Texas instrumentos SensorTag para enviar dados para o Hub de IoT através de um gateway em execução em um módulo de computação de Edison Intel"
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


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Azure IoT Gateway SDK (beta) – envie mensagens de dispositivo na nuvem com um dispositivo real usando o Linux

Este passo a passo da [amostra de energia baixa Bluetooth] [ lnk-ble-samplecode] mostra como usar o [SDK do Azure IoT Gateway] [ lnk-sdk] para encaminhar telemetria de dispositivo à nuvem IoT hub de um dispositivo físico e como rotear comandos de IoT Hub para um dispositivo físico.

Este passo a passo abrange:

* **Arquitetura**: informações arquitetônicas importantes sobre a amostra de energia baixa Bluetooth.

* **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

O passo a passo mostra como criar e executar um IoT Gateway em um módulo de computação de Edison Intel que executa o Linux. O gateway é criado usando o SDK do Gateway IoT. O exemplo usa um dispositivo Texas instrumentos SensorTag Bluetooth baixa energia (var) para coletar dados de temperatura.

Quando você executa o gateway-la:

- Conecta-se em um dispositivo de SensorTag usando o protocolo de energia de baixa Bluetooth (var).
- Se conecta ao IoT Hub usando o protocolo HTTP.
- Encaminha telemetria do dispositivo SensorTag IoT hub.
- Comandos de rotas de IoT Hub ao dispositivo SensorTag.

O gateway contém os seguintes módulos:

- Um *módulo de BELA* que interage com um dispositivo BELA para receber dados de temperatura entre os comandos de dispositivo e enviar para o dispositivo.
- Uma *Nuvem BELA módulo do dispositivo* que traduz as mensagens JSON provenientes da nuvem em instruções BELA para o *módulo BELA*.
- Um *módulo do agente de log* que registra todas as mensagens de gateway.
- Um *módulo de mapeamento de identidade* que traduz entre endereços MAC dos dispositivos BELA e identidades de dispositivos do Azure IoT Hub.
- Um *módulo IoT Hub* que carrega os dados de telemetria a um hub IoT e recebe comandos de dispositivo de um hub IoT.
- Um *módulo de impressora BELA* que interprete telemetria do dispositivo BELA e imprime formatado dados no console para habilitar a solução de problemas e depuração.

### <a name="how-data-flows-through-the-gateway"></a>Como os dados fluem através do Gateway

O diagrama de bloco a seguir ilustra o pipeline de fluxo de dados de carregamento de telemetria:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

As etapas que um item de telemetria leva viajando em um dispositivo BELA IoT hub são:

1. O dispositivo BELA gera uma amostra de temperatura e envia via Bluetooth no módulo BELA no gateway.
2. O módulo BELA recebe a amostra e publica o corretor junto com o endereço de MAC do dispositivo.
3. O módulo de mapeamento de identidade seleciona esta mensagem e usa uma tabela interna para traduzir o endereço de MAC do dispositivo para uma identidade de dispositivo do IoT Hub (um ID de dispositivo e a chave do dispositivo). Em seguida, ele publica uma nova mensagem que contém os dados de exemplo de temperatura, o endereço MAC do dispositivo, a ID do dispositivo e a chave do dispositivo.
4. O módulo do IoT Hub recebe essa mensagem nova (gerada pelo módulo de mapeamento de identidade) e publica IoT Hub.
5. O módulo do agente de log registra todas as mensagens do agente para um arquivo de disco.

O diagrama de bloco a seguir ilustra o pipeline de fluxo de dados de comando de dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. O módulo do IoT Hub periodicamente controla o hub de IoT para novas mensagens de comando.
2. Quando o módulo do IoT Hub recebe uma nova mensagem de comando, ele publica o corretor.
3. O módulo de mapeamento de identidade seleciona a mensagem de comando e usa uma tabela interna para traduzir a ID do dispositivo IoT Hub para um dispositivo de endereço MAC. Em seguida, ele publica uma nova mensagem que inclui o endereço de MAC do dispositivo de destino no mapa de propriedades da mensagem.
4. A nuvem BELA módulo dispositivo seleciona esta mensagem e converte na instrução var adequada para o módulo BELA. Em seguida, ele publica uma nova mensagem.
5. O módulo BELA seleciona esta mensagem e executa a instrução de e/s comunicando-se com o dispositivo BELA.
6. O módulo do agente de log registra todas as mensagens do agente para um arquivo de disco.

## <a name="prepare-your-hardware"></a>Preparar o hardware

Este tutorial supõe que você estiver usando um dispositivo de [Texas instrumentos SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a um quadro Edison Intel.

### <a name="set-up-the-edison-board"></a>Configurar o quadro Edison

Antes de começar, você deverá garantir que você pode conectar seu dispositivo Edison a sua rede sem fio. Para configurar seu dispositivo Edison, é necessário conectá-la a um computador host. Intel fornece guias para os seguintes sistemas operacionais de Introdução:

- [Começar a usar o quadro de desenvolvimento de Edison Intel no Windows de 64 bits][lnk-setup-win64].
- [Começar a usar o quadro de desenvolvimento de Edison Intel no Windows de 32 bits][lnk-setup-win32].
- [Começar a usar o quadro de desenvolvimento de Edison Intel no Mac OS X][lnk-setup-osx].
- [Introdução ao quadro de Edison Intel® no Linux][lnk-setup-linux].

Para configurar seu dispositivo Edison e familiarizar com ele, você deve concluir todas as etapas nestas "Introdução" artigos, exceto para a última etapa, "Escolha IDE", que é desnecessário para o tutorial atual. No final do processo de instalação Edison tiver:

- Atualizado seu Edison com o firmware mais recente.
- Estabelecer uma conexão serial do seu host para o Edison.
- Execute o script **configure_edison** para definir uma senha e habilitar WiFi na sua Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Habilitar a conectividade para o dispositivo de SensorTag de seu quadro de Edison

Antes de executar o exemplo, você precisa verificar se o seu quadro de Edison pode se conectar ao dispositivo SensorTag.

Primeiro você precisa verificar se o seu Edison pode se conectar ao dispositivo SensorTag.

1. Desbloquear bluetooth na Edison e verifique se o número da versão é **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Execute o comando **bluetoothctl** . Agora, você está em um shell interativo bluetooth. 

3. Insira o comando **ligue** para ligar o controlador bluetooth. Você verá a saída semelhante a:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Enquanto ainda estiver no shell interativo bluetooth, insira o comando **Verificar ao** procurar dispositivos bluetooth. Você verá a saída semelhante a:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Verifique o dispositivo de SensorTag descoberto pressionando o botão pequeno (o LED verde deve flash). O Edison deve detectar o dispositivo de SensorTag:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    Neste exemplo, você pode ver que o endereço de MAC do dispositivo SensorTag é **A0:E6:F8:B5:F6:00**.

6. Desative a verificação, digitando o comando **Digitalizar desativado** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Conectar ao seu dispositivo de SensorTag usando seu endereço MAC por meio de **conectar \<endereço MAC >**. Observe que a saída de exemplo abaixo será abreviada:
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Observação: Você pode listar as características de GATT do dispositivo novamente usando o comando **atributos de lista** .

8. Você pode agora Desconecte o dispositivo usando o comando **Desconectar** e depois saia do shell bluetooth usando o comando **Sair** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora você está pronto para executar a amostra de Gateway BELA em seu dispositivo Edison.

## <a name="run-the-ble-gateway-sample"></a>Executar a amostra de Gateway BELA

Para executar a amostra de BELA em seu Edison, você precisa concluir três tarefas:

- Configure dois dispositivos de amostra no seu IoT Hub.
- Crie o SDK do Gateway IoT em seu dispositivo Edison.
- Configurar e executar a amostra de BELA em seu dispositivo Edison.

No momento da gravação, o SDK do Gateway IoT só oferece suporte a gateways que usam módulos BELA no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar o seu IoT Hub dois dispositivos de amostra

- [Criar um hub IoT] [ lnk-create-hub] na sua assinatura do Azure, será necessário o nome do seu hub para concluir este passo a passo. Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- Adicionar um dispositivo chamado **SensorTag_01** para seu hub IoT e anote sua chave de identificação e do dispositivo. Você pode usar o [Gerenciador de dispositivo ou Gerenciador de iothub] [ lnk-explorer-tools] ferramentas para adicionar este dispositivo ao hub IoT você criou na etapa anterior e recuperar sua chave. Mapeie esse dispositivo para o dispositivo de SensorTag quando você configura o gateway.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Criar o SDK do Gateway IoT em seu dispositivo Edison

A versão do **gito** na Edsion não suporta submodules. Para baixar o código-fonte completo para o SDK do Gateway IoT para o Edison você tem duas opções:

- Opção #1: Clonar o [SDK do Azure IoT Gateway] [ lnk-sdk] repositório no seu Edison e depois clonar manualmente o repositório para cada submódulo.
- Opção #2: Clonar o [SDK do Azure IoT Gateway] [ lnk-sdk] repositório em um dispositivo da área de trabalho onde **gito** suporta submodules e copie o repositório completo com submodules em seu Edison.

Se você escolher a opção #2, use os seguintes comandos de **gito** para clonar o SDK do Gateway IoT e todos os seus submodules:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Em seguida, você deve zip todo o repositório local em um único arquivo morto antes de copiá-lo para o Edison. Você pode usar um utilitário como **pscp** que está incluído no **Acabamento** para copiar o arquivo morto para o Edison. Por exemplo:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Quando você tem uma cópia completa do repositório IoT Gateway SDK na sua Edison, você pode criá-la usando o seguinte comando de na pasta que contém o SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurar e executar a amostra de BELA em seu dispositivo Edison

Para inicializar e executar o exemplo, você precisa configurar cada módulo que participa no gateway. Esta configuração é fornecida em um arquivo JSON e você precisa configurar todos os cinco módulos de participantes. Há um arquivo JSON de exemplo fornecido no repositório chamado **gateway_sample.json** que você pode usar como ponto de partida para criar seu próprio arquivo de configuração. Este arquivo está na pasta **amostras/ble_gateway_hl/src** na cópia local do repositório IoT Gateway SDK.

As seções a seguir descrevem como editar esse arquivo de configuração para a amostra de BELA e presumem que o repositório de IoT Gateway SDK está na **/home/root/azure-iot-gateway-sdk /** pasta em seu dispositivo Edison. Se o repositório estiver em outro lugar, você deve ajustar os caminhos adequadamente:

#### <a name="logger-configuration"></a>Configuração do agente de log

Considerando que o repositório de gateway está localizado na pasta **/home/root/azure-iot-gateway-sdk /**, configure o módulo do agente de log da seguinte maneira:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuração de módulo BELA

A configuração de exemplo para o dispositivo de VAR supõe um dispositivo Texas instrumentos SensorTag. Qualquer dispositivo BELA padrão que pode funcionar como uma GATT periférico deve funcionar, mas você precisará atualizar as IDs de característica GATT e dados (para obter instruções de gravação). Adicione o endereço de MAC do seu dispositivo de SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Módulo IoT Hub

Adicione o nome do seu IoT Hub. O valor de sufixo normalmente é **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração de módulo de mapeamento de identidade

Adicione o endereço MAC do seu dispositivo de SensorTag e a Id e a chave do dispositivo **SensorTag_01** que você adicionou a seu IoT Hub:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração de módulo de impressora BELA

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de roteamento

A seguinte configuração garante o seguinte:
- O módulo do **agente** recebe e registrar todas as mensagens.
- O módulo **SensorTag** envia mensagens para o **mapeamento** e de **Impressora BELA** módulos.
- O módulo de **mapeamento** envia mensagens para o módulo **IoTHub** para ser enviado ao seu IoT Hub.
- O módulo **IoTHub** envia mensagens de volta para o módulo de **mapeamento** .
- O módulo de **mapeamento** envia mensagens de volta para o módulo **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Para executar a amostra é executar **ble_gateway_hl** binário passando o caminho para o arquivo de configuração de JSON. Se você usou o arquivo de **gateway_sample.json** , o comando para executar terá esta aparência:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Você talvez precise pressionar o botão pequeno do dispositivo SensorTag para torná-lo descoberto antes de executar a amostra.

Quando você executa a amostra, você pode usar o [Gerenciador de dispositivo ou Gerenciador de iothub] [ lnk-explorer-tools] ferramenta para monitorar as mensagens que o gateway encaminha do dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens de nuvem para dispositivo

O módulo BELA também suporta envio de instruções do Azure IoT Hub no dispositivo. Você pode usar o [Explorador de dispositivo do Azure IoT Hub](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou o [IoT Hub Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) para enviar mensagens JSON que o módulo do gateway BELA passa para o dispositivo BELA. Por exemplo, se você estiver usando o dispositivo Texas instrumentos SensorTag, em seguida, você pode enviar as seguintes mensagens JSON para o dispositivo do IoT Hub.

- Redefinir todos os LEDs e a campainha (desativá-los)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurar e/s como 'remoto'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Ativar o LED vermelho

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Ativar o LED verde

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Ativar a campainha

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

O comportamento padrão para um dispositivo usando o protocolo HTTP para se conectar ao IoT Hub é verificar cada 25 minutos para um novo comando. Portanto, se você enviar vários comandos separados, você precisa esperar 25 minutos do dispositivo para cada comando receive.

> [AZURE.NOTE] O gateway também verifica novos comandos sempre que é iniciado para que você pode forçar para processar um comando interrompendo e iniciando o gateway.

## <a name="next-steps"></a>Próximas etapas

Se você quiser obter uma compreensão mais avançada do SDK do Gateway IoT e experimentar alguns exemplos de código, visite os seguintes tutoriais do desenvolvedor e recursos:

- [Gateway do Azure IoT SDK][lnk-sdk]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
