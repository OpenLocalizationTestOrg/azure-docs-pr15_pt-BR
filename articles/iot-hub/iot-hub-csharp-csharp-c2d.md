<properties
    pageTitle="Enviar mensagens de nuvem para dispositivo com IoT Hub | Microsoft Azure"
    description="Siga este tutorial para saber como enviar mensagens de nuvem para dispositivo usando Azure IoT Hub com c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Tutorial: Como enviar mensagens de nuvem para dispositivo com IoT Hub e .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que ajuda a habilitar confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e encerrar um aplicativo novamente. O tutorial de [Introdução ao IoT Hub] mostra como criar um hub IoT, provisionar uma dispositivo identidade nele e o código de um dispositivo simulado que envia mensagens de dispositivo na nuvem.

Este tutorial cria na [Introdução ao IoT Hub]. Mostra como para:

- Do seu aplicativo nuvem back-end, envie mensagens de nuvem para dispositivo para um único dispositivo por IoT Hub.
- Receba mensagens de nuvem para dispositivo em um dispositivo.
- Da nuvem seu aplicativo back-end, solicitar confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo de IoT Hub.

Você pode encontrar mais informações sobre mensagens de nuvem para dispositivo no [Guia do desenvolvedor do IoT Hub][IoT Hub Developer Guide - C2D].

No final deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Introdução ao IoT Hub], que se conecta ao seu hub IoT e recebe mensagens de nuvem para dispositivo.
* **SendCloudToDevice**, que envia uma mensagem de nuvem para dispositivo para o dispositivo simulado por IoT Hub e então recebe sua confirmação de entrega.

> [AZURE.NOTE] Hub de IoT tem suporte SDK muitas plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) por meio do Azure IoT dispositivo SDKs. Para obter instruções passo a passo sobre como conectar seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Azure IoT Developer Center].

Para concluir este tutorial, você precisará o seguinte:

+ Microsoft Visual Studio 2015

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens do dispositivo simulado

Nesta seção, você irá modificar o aplicativo do dispositivo simulado criado na [Introdução ao IoT Hub] para receber mensagens de nuvem para dispositivo do hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice** , adicione o seguinte método à classe **programa** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    O `ReceiveAsync` método assíncrona retorna a mensagem recebida no momento em que ela for recebida pelo dispositivo. Retorna *null* após um período de tempo limite especificáveis (neste caso, o padrão de um minuto é usado). Quando isso acontece, o código deve continuar aguardando para novas mensagens. Essa é a razão para a `if (receivedMessage == null) continue` linha.

    A chamada para `CompleteAsync()` notifica IoT Hub que a mensagem foi processada com êxito. A mensagem pode ser removida com segurança da fila de dispositivo. Se algo acontecer que impede que o aplicativo de dispositivo Concluindo o processamento da mensagem, IoT Hub oferece-lo novamente. Em seguida, é importante que a lógica de processamento de mensagem no aplicativo do dispositivo ser *idempotentes*, para que está recebendo a mesma mensagem várias vezes produz o mesmo resultado. Um aplicativo pode abandonar também temporariamente uma mensagem, o que resulta no hub IoT reter a mensagem na fila de consumo futura. Ou, o aplicativo pode rejeitar uma mensagem, que remove permanentemente a mensagem da fila. Para obter mais informações sobre o ciclo de vida de mensagem de nuvem para dispositivo, consulte o [Guia de desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Ao usar HTTP em vez de MQTT ou AMQP como um transporte, o `ReceiveAsync` método retorna imediatamente. O padrão com suporte para mensagens de nuvem para dispositivo com HTTP está conectados intermitentemente dispositivos que verifiquem mensagens raramente (menos de cada minutos 25). Emissão mais HTTP recebe resultados no IoT Hub otimização as solicitações. Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTP e IoT Hub limitação, consulte o [Guia de desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

2. Adicione o seguinte método no método **principal** , logo antes do `Console.ReadLine()` linha:

        ReceiveC2dAsync();

> [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como retirada exponencial), conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de nuvem para dispositivo

Nesta seção, você vai escrever um aplicativo de console do Windows que envia mensagens de nuvem para dispositivo ao aplicativo do dispositivo simulado.

1. Na solução atual Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual c# usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **SendCloudToDevice**.

    ![Novo projeto no Visual Studio][20]

2. No Solution Explorer, clique com botão direito a solução e clique em **Gerenciar pacotes NuGet para solução...**. 

    Isso abre a janela **Gerenciar pacotes do NuGet** .

3. Procurar por `Microsoft Azure Devices`, clique em **instalar**e aceite os termos de uso. 

    Isso downloads, instalações e adiciona uma referência para o [Azure IoT - pacote serviço SDK NuGet].

4. Adicione o seguinte `using` instrução na parte superior do arquivo **Program.cs** :

        using Microsoft.Azure.Devices;

5. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão do hub de IoT de [Introdução ao IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **programa** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Este método envia uma nova mensagem de nuvem para dispositivo para o dispositivo com a ID, `myFirstDevice`. Altere este parâmetro da mesma forma, caso você o modificou daquela usada em [Introdução ao IoT Hub].

7. Finalmente, adicione as seguintes linhas para o método **principal** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Em Visual Studio, sua solução de atalho e selecione **Definir inicialização projetos...**. Selecionar **vários projetos de inicialização**e, em seguida, selecione a ação **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice**.

9.  Pressione **F5**. Todos os três aplicativos devem começar. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você verá a mensagem sendo recebida pelo aplicativo simulado.

    ![Mensagem de recebimento de aplicativo][21]

## <a name="receive-delivery-feedback"></a>Receba comentários de entrega
É possível confirmações de entrega (ou expiração) de solicitação de IoT Hub para cada mensagem de nuvem para dispositivo. Isso permite que o back-end de nuvem informar facilmente lógica de repetição ou de remuneração. Para saber mais sobre comentários de nuvem para dispositivo, consulte o [Guia de desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

Nesta seção, você irá modificar o aplicativo **SendCloudToDevice** para solicitar comentários e recebê-las de IoT Hub.

1. No Visual Studio, no projeto **SendCloudToDevice** , adicione o seguinte método à classe **programa** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Observe que o padrão de recebimento é o mesmo usado para receber mensagens de nuvem para dispositivo do aplicativo do dispositivo.

2. Adicione o seguinte método no método **principal** , logo após o `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` linha:

        ReceiveFeedbackAsync();

3. Para solicitar comentários para a entrega da sua mensagem de nuvem para dispositivo, você precisa especificar uma propriedade no método **SendCloudToDeviceMessageAsync** . Adicione a seguinte linha, logo após o `var commandMessage = new Message(...);` linha:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Execute os aplicativos pressionando **F5**. Você deve ver todos os três aplicativos iniciar. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você verá a mensagem sendo recebida pelo aplicativo simulado e após alguns segundos, a mensagem de comentários sendo recebida pelo seu aplicativo **SendCloudToDevice** .

    ![Mensagem de recebimento de aplicativo][22]

> [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como retirada exponencial), conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a enviar e receber mensagens de nuvem para dispositivo. 

Para ver exemplos de soluções de ponta a ponta completas que usam IoT Hub, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com IoT Hub, consulte o [Guia de desenvolvedor do IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - pacote serviço SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guia de desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Central de desenvolvedores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Pacote de IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/