<properties
    pageTitle="Hub de IoT Azure para c# Introdução | Microsoft Azure"
    description="Hub de IoT Azure com c# obtendo iniciado tutorial. Use Azure IoT Hub e c# com o Microsoft Azure IoT SDKs para implementar uma solução de Internet das coisas."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Introdução ao Azure IoT Hub para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, você tem três aplicativos de console do Windows:

* **CreateDeviceIdentity**, que cria uma identidade de dispositivo e a chave de segurança associadas para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages**, que exibe a telemetria enviada por seu dispositivo simulado.
* **SimulatedDevice**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente e envia uma mensagem de telemetria cada segundo usando o protocolo AMQP.

> [AZURE.NOTE] Para obter informações sobre o SDK do vários que você pode usar para criar aplicativos executados em dispositivos e back-end sua solução, consulte [IoT Hub SDKs][lnk-hub-sdks].

Para concluir este tutorial, você precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora você criou seu hub IoT e você tiver a cadeia de caracteres hostname e conexão que você precisa para concluir o restante deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta seção, você criar um aplicativo de console do Windows que cria uma identidade de dispositivo no registro identidade em seu hub IoT. Um dispositivo não pode se conectar a IoT hub, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Para obter mais informações, consulte a seção "Registro de identidade do dispositivo" do [Guia de desenvolvedor do Hub IoT][lnk-devguide-identity]. Quando você executa este aplicativo de console, ele gera uma identificação de dispositivo exclusivo e chave que seu dispositivo pode usar para se identificar quando ele envia mensagens de dispositivo à nuvem para IoT Hub.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico Desktop à solução atual usando o modelo de projeto de **Aplicativo de Console** . Verifique se que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **CreateDeviceIdentity**.

    ![Novo projeto do Visual c# Windows clássico da área de trabalho][10]

2. No Solution Explorer, clique com botão direito no projeto **CreateDeviceIdentity** e clique em **Gerenciar pacotes do Nuget**.

3. Na janela do **Gerenciador de pacotes do Nuget** , selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **instalar** para instalar o pacote de **Microsoft.Azure.Devices** e aceite os termos de uso. Esse procedimento downloads, instalações e adiciona uma referência para o [SDK do serviço do Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget pacote e suas dependências.

    ![Janela de NuGet Package Manager][11]

4. Adicione o seguinte `using` instruções na parte superior do arquivo **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão para o hub de IoT que você criou na seção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **programa** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Este método cria uma identidade de dispositivo com ID **myFirstDevice**. (Se essa ID de dispositivo já existir no registro, o código simplesmente recupera as informações de dispositivo existentes.) O aplicativo, em seguida, exibe a chave primária de identidade. Usar esta tecla no dispositivo simulado para se conectar ao seu hub IoT.

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Executar esse aplicativo e anote a chave do dispositivo.

    ![Chave de dispositivo gerada pelo aplicativo][12]

> [AZURE.NOTE] O registro de identidade IoT Hub armazena apenas identidades do dispositivo para habilitar o acesso seguro ao hub. Ele armazena identificações de dispositivo e teclas para usar como credenciais de segurança e um sinalizador de ativado/desativado que você pode usar para desabilitar o acesso para um dispositivo individual. Se seu aplicativo precisa armazenar outros metadados específicos do dispositivo, ele deve usar uma loja de aplicativos específicos. Para obter mais informações, consulte o [Guia de desenvolvedor do Hub de IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de dispositivo à nuvem

Nesta seção, você criar um aplicativo de console do Windows que lê mensagens de dispositivo à nuvem do IoT Hub. Um hub IoT expõe um [Hubs de evento do Azure][lnk-event-hubs-overview]-ponto de extremidade compatível para que você possa ler mensagens de dispositivo na nuvem. Para manter as coisas simples, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. Para saber como processar mensagens de dispositivo à nuvem em escala, consulte as [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial. Para obter mais informações sobre como processar mensagens de Hubs de evento, consulte a [Introdução ao evento Hubs] [ lnk-eventhubs-tutorial] tutorial. (Este tutorial é aplicável para os pontos de extremidade compatível com o evento de Hub IoT Hub.)

> [AZURE.NOTE] O ponto de extremidade compatível com o evento Hub para ler mensagens de dispositivo à nuvem sempre usa o protocolo AMQP.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico Desktop à solução atual, usando o modelo de projeto de **Aplicativo de Console** . Verifique se que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **ReadDeviceToCloudMessages**.

    ![Novo projeto do Visual c# Windows clássico da área de trabalho][10]

2. No Solution Explorer, clique com botão direito no projeto **ReadDeviceToCloudMessages** e clique em **Gerenciar pacotes do Nuget**.

3. Na janela do **Gerenciador de pacotes do Nuget** , pesquise **WindowsAzure.ServiceBus**, selecione **instalar**e aceite os termos de uso. Esse procedimento downloads, instalações e adiciona uma referência ao [Barramento de serviço do Azure][lnk-servicebus-nuget], com todas as suas dependências. Este pacote permite que o aplicativo se conecte ao ponto de extremidade compatível com o evento Hub em seu hub IoT.

4. Adicione o seguinte `using` instruções na parte superior do arquivo **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão para o hub de IoT que você criou na seção "Criar um hub IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adicione o seguinte método à classe **programa** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Este método usa uma instância de **EventHubReceiver** para receber mensagens de todas as IoT hub dispositivo-para-nuvem recebem partições. Observe como você passar um `DateTime.Now` parâmetro quando você cria o objeto **EventHubReceiver** , para que ele receba apenas as mensagens enviadas após ele ser iniciado. Esse filtro é útil em um ambiente de teste para que você possa ver o conjunto atual de mensagens. Em um ambiente de produção seu código deverá garantir que ele processa todas as mensagens. Para obter mais informações, consulte [como processar mensagens de dispositivo à nuvem IoT Hub] [ lnk-process-d2c-tutorial] tutorial.

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você criar um aplicativo de console do Windows que simula um dispositivo que envia mensagens de dispositivo à nuvem para um hub IoT.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico Desktop à solução atual, usando o modelo de projeto de **Aplicativo de Console** . Verifique se que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **SimulatedDevice**.

    ![Novo projeto do Visual c# Windows clássico da área de trabalho][10]

2. No Solution Explorer, clique com botão direito no projeto **SimulatedDevice** e clique em **Gerenciar pacotes do Nuget**.

3. Na janela do **Gerenciador de pacotes do Nuget** , selecione **Procurar**, pesquise **Microsoft.Azure.Devices.Client**, selecione **instalar** para instalar o pacote de **Microsoft.Azure.Devices.Client** e aceite os termos de uso. Esse procedimento downloads, instalações e adiciona uma referência para o [Azure IoT - pacote dispositivo SDK Nuget] [ lnk-device-nuget] e suas dependências.

4. Adicione o seguinte `using` instrução na parte superior do arquivo **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Adicione os campos a seguir para a classe de **programa** . Substitua os valores de espaço reservado com o nome de host IoT hub que você recuperou na seção "Criar um hub IoT" e a chave do dispositivo recuperados na seção "Criar uma identidade de dispositivo".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Adicione o seguinte método à classe **programa** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Este método envia uma nova mensagem de dispositivo à nuvem cada segundo. A mensagem contém um objeto serializado JSON, com a ID de dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por padrão, o método **Create** cria uma instância de **DeviceClient** que usa o protocolo AMQP para se comunicar com IoT Hub. Para usar o protocolo HTTP, use a substituição do método **Create** que permite especificar o protocolo. Se você usar o protocolo HTTP, você também deve adicionar o pacote de Nuget **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o namespace **System.Net.Http.Formatting** .

Este tutorial apresenta as etapas para criar um cliente do dispositivo IoT Hub. Você também pode usar o [Serviço conectado para Azure IoT Hub] [ lnk-connected-service] extensão do Visual Studio para adicionar o código necessário ao seu aplicativo de cliente do dispositivo.


> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  No Visual Studio, no Solution Explorer, clique com botão direito sua solução e clique em **projetos de inicialização definido**. Selecione **vários projetos de inicialização**e, em seguida, selecione **Iniciar** como a ação para projetos **ReadDeviceToCloudMessages** tanto o **SimulatedDevice** .

    ![Propriedades do projeto de inicialização][41]

2.  Pressione **F5** para iniciar os dois aplicativos em execução. A saída do console do aplicativo **SimulatedDevice** mostra as mensagens de que seu dispositivo simulado envia para seu hub IoT. A saída do console do aplicativo **ReadDeviceToCloudMessages** mostra as mensagens que recebe seu hub IoT.

    ![Saída do console de aplicativos][42]

3. O bloco de **uso** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![Bloco de uso de portal Azure][43]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um hub IoT no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você usou este dispositivo identidade para habilitar o aplicativo do dispositivo simulado enviar mensagens de dispositivo à nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub. 

Para continuar a introdução ao IoT Hub e explorar outros cenários IoT, consulte:

- [Conectar seu dispositivo][lnk-connect-device]
- [Introdução ao gerenciamento de dispositivo][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como estender sua solução IoT e processar mensagens de dispositivo à nuvem em escala, consulte as [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
