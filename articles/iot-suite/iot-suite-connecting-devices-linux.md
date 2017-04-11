<properties
   pageTitle="Conecte um dispositivo usando C no Linux | Microsoft Azure"
   description="Descreve como conectar um dispositivo para a solução de monitoramento remota da Azure IoT pacote pré-configurado usando um aplicativo escrito em C em execução no Linux."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Conectar o dispositivo para a solução de pré-configurado monitoramento remota (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Criar e executar um cliente de amostra C Linux

Os procedimentos a seguir mostram como criar um aplicativo cliente, escrito em C e construído e executado no Ubuntu Linux, que se comunica com a solução pré-configurada monitoramento remota. Para concluir essas etapas, você precisa de um dispositivo executando Ubuntu versão 15.04 ou 15.10. Antes de prosseguir, instale os pacotes de pré-requisito em seu dispositivo Ubuntu usando o seguinte comando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalar as bibliotecas de cliente no seu dispositivo

Bibliotecas de cliente do Azure IoT Hub estão disponíveis como um pacote, que você pode instalar em seu dispositivo Ubuntu usando o comando **get chance** . Conclua as seguintes etapas para instalar o pacote que contém os arquivos IoT Hub de biblioteca e o cabeçalho de cliente em sua máquina Ubuntu:

1. Adicione o repositório de AzureIoT na máquina:

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. Instale o pacote do azure-iot-sdk-c-desenvolvimento

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="add-code-to-specify-the-behavior-of-the-device"></a>Adicionar código para especificar o comportamento do dispositivo

Em sua máquina Ubuntu, crie uma pasta chamada **remoto\_monitoramento**. No **remoto\_monitoramento** pasta criar as quatro arquivos **main.c**, **remoto\_monitoring.c**, **remoto\_monitoring.h**e **CMakeLists.txt**.

As bibliotecas de cliente do IoT Hub serializador usam um modelo para especificar o formato das mensagens que envia o dispositivo IoT Hub e os comandos que ele recebe de IoT Hub.

1. Em um editor de texto, abra o **remoto\_monitoring.c** arquivo. Adicione o seguinte `#include` instruções:

    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Adicionar as seguintes declarações de variáveis após o `#include` instruções. Substitua os valores de espaço reservado [Id do dispositivo] e [dispositivo chave] com valores para seu dispositivo no painel de solução de monitoramento remoto. Use o nome de host do Hub de IoT no painel para substituir a [nome do IoTHub]. Por exemplo, se o seu nome de host do Hub IoT for **contoso.azure-devices.net**, substitua [nome do IoTHub] **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Adicione o seguinte código para definir o modelo que permite que o dispositivo para se comunicar com IoT Hub. Este modelo especifica que o dispositivo envia temperatura, temperatura externa, umidade e uma id de dispositivo como telemetria. O dispositivo também envia metadados sobre o dispositivo para IoT Hub, incluindo uma lista de comandos que o dispositivo oferece suporte. Este dispositivo responde os comandos **SetTemperature (DefinirTemperatura)** e **SetHumidity**:

    ```
    // Define the Model
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

### <a name="add-code-to-implement-the-behavior-of-the-device"></a>Adicionar código para implementar o comportamento do dispositivo

Adicione as funções para executar quando o dispositivo recebe um comando de hub e o código para enviar telemetria simulada para o hub.

1. Adicione as seguintes funções que são executados quando o dispositivo recebe os comandos **SetTemperature (DefinirTemperatura)** e **SetHumidity** definidos no modelo:

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

2. Adicione a seguinte função que envia uma mensagem para IoT Hub:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Adicione a seguinte função que conecta a biblioteca de serialização no SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Adicione a seguinte função para conectar ao IoT Hub, enviar e receber mensagens e desconectar do hub. Observe como o dispositivo envia metadados sobre si, incluindo os comandos que ele oferece suporte, a IoT Hub quando ele se conecta. Esses metadados habilita a solução para atualizar o status do dispositivo para **execução** no painel:

    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (serializer_init(NULL) != SERIALIZER_OK)
        {
          printf("Failed on serializer_init\r\n");
        }
        else
        {
          IOTHUB_CLIENT_CONFIG config;
          IOTHUB_CLIENT_HANDLE iotHubClientHandle;

          config.deviceId = deviceId;
          config.deviceKey = deviceKey;
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
          iotHubClientHandle = IoTHubClient_Create(&config);
          if (iotHubClientHandle == NULL)
          {
            (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
            if (thermostat == NULL)
            {
              (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
            }
            else
            {
              STRING_HANDLE commandsMetadata;

              if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
              {
                printf("unable to IoTHubClient_SetMessageCallback\r\n");
              }
              else
              {

                /* send the device info upon startup so that the cloud app knows
                   what commands are available and the fact that the device is up */
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = false;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = true;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                commandsMetadata = STRING_new();
                if (commandsMetadata == NULL)
                {
                  (void)printf("Failed on creating string for commands metadata\r\n");
                }
                else
                {
                  /* Serialize the commands metadata as a JSON string before sending */
                  if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                  {
                    (void)printf("Failed serializing commands metadata\r\n");
                  }
                  else
                  {
                    unsigned char* buffer;
                    size_t bufferSize;
                    thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                    /* Here is the actual send of the Device Info */
                    if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                    {
                      (void)printf("Failed serializing\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize);
                    }

                  }

                  STRING_delete(commandsMetadata);
                }

                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }
              }

              DESTROY_MODEL_INSTANCE(thermostat);
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
        platform_deinit();
      }
    }
    ```
    
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

### <a name="add-code-to-invoke-the-remotemonitoringrun-function"></a>Adicionar código para chamar a função remote_monitoring_run

Em um editor de texto, abra o arquivo **remote_monitoring.h** . Adicione o seguinte código:

```
void remote_monitoring_run(void);
```

Em um editor de texto, abra o arquivo **main.c** . Adicione o seguinte código:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="use-cmake-to-build-the-client-application"></a>Usar CMake para criar o aplicativo cliente

As etapas a seguir descrevem como usar *CMake* para criar seu aplicativo cliente.

1. Em um editor de texto, abra o arquivo **CMakeLists.txt** na pasta **remote_monitoring** .

2. Adicione as instruções a seguir para definir como criar seu aplicativo cliente:

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. Na pasta **remote_monitoring** , crie uma pasta para armazenar os arquivos *tornar* CMake gera e então executar os comandos **cmake** e **Verifique** da seguinte maneira:

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. Executar o aplicativo cliente e envie telemetria IoT Hub:

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

