<properties
    pageTitle="Enviar mensagens de nuvem para dispositivo com IoT Hub | Microsoft Azure"
    description="Siga este tutorial para saber como enviar mensagens de nuvem para dispositivo usando Azure IoT Hub com Java."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Tutorial: Como enviar mensagens de nuvem para dispositivo com IoT Hub e Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que ajuda a habilitar confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e encerrar um aplicativo novamente. O tutorial de [Introdução ao IoT Hub] mostra como criar um hub IoT, provisionar uma dispositivo identidade nele e o código de um dispositivo simulado que envia mensagens de dispositivo na nuvem.

Este tutorial cria na [Introdução ao IoT Hub]. Mostra como para:

- Do seu aplicativo nuvem back-end, envie mensagens de nuvem para dispositivo para um único dispositivo por IoT Hub.
- Receba mensagens de nuvem para dispositivo em um dispositivo.
- Da nuvem seu aplicativo back-end, solicitar confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo de IoT Hub.

Você pode encontrar mais informações sobre mensagens de nuvem para dispositivo no [Guia do desenvolvedor do IoT Hub][IoT Hub Developer Guide - C2D].

No final deste tutorial, você deve executar dois aplicativos de console de Java:

* **dispositivo simulado**, uma versão modificada do aplicativo criado em [Introdução ao IoT Hub], que se conecta ao seu hub IoT e recebe mensagens de nuvem para dispositivo.
* **Enviar mensagens de c2d**, que envia uma mensagem de nuvem para dispositivo para o dispositivo simulado por IoT Hub e então recebe sua confirmação de entrega.

> [AZURE.NOTE] Hub de IoT tem suporte SDK muitas plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) por meio do Azure IoT dispositivo SDKs. Para obter instruções passo a passo sobre como conectar seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Azure IoT Developer Center].

Para concluir este tutorial, você precisa do seguinte:

+ Java SE 8. <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Java para este tutorial em Windows ou Linux.

+ Maven 3.  <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial em Windows ou Linux.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens do dispositivo simulado

Nesta seção, você modificar o aplicativo de dispositivo simulado criado na [Introdução ao IoT Hub] para receber mensagens de nuvem para dispositivo do hub IoT.

1. Usando um editor de texto, abra o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione a seguinte classe **MessageCallback** como uma classe aninhada dentro da classe de **aplicativo** . **Executar** método é invocado quando o dispositivo recebe uma mensagem de IoT Hub. Neste exemplo, o dispositivo sempre notifica o hub que concluiu a mensagem:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificar o método **principal** para criar uma instância de **MessageCallback** e chame o método de **setMessageCallback** antes de abrir o cliente da seguinte maneira:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Se você usar HTTP em vez de MQTT ou AMQP como transporte, a instância de **DeviceClient** verifica mensagens de Hub de IoT raramente (menos de cada minutos 25). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTP e IoT Hub limitação, consulte o [Guia de desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de nuvem para dispositivo

Nesta seção, você criar um aplicativo de console de Java que envia mensagens de nuvem para dispositivo ao aplicativo do dispositivo simulado. É necessário o Id do dispositivo que você adicionou o tutorial de [Introdução ao IoT Hub] de dispositivo. Você também precisa a cadeia de conexão para o seu hub IoT que você pode encontrar no [portal do Azure].

1. Crie um projeto Maven chamado **Enviar mensagens de c2d** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de enviar mensagens de c2d.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta enviar mensagens de c2d e adicione a seguinte dependência para o nó de **dependências** . Adicionar a dependência permite que você use o pacote de **iothub-serviço-cliente java** em seu aplicativo para se comunicar com seu serviço de hub IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o arquivo:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe para a classe de **aplicativo** , substituindo **{yourhubconnectionstring}** e **{yourdeviceid}** pelos valores seu observado anteriormente:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Substitua o método **principal** com o seguinte código que se conecta ao seu hub IoT, envia uma mensagem para seu dispositivo e, em seguida, aguarda uma confirmação de que o dispositivo recebido e processado a mensagem:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como retirada exponencial), conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando-na pasta do dispositivo simulado, execute o seguinte comando para começar a enviar telemetria para seu hub IoT e para escutar mensagens de nuvem para dispositivo enviadas de seu hub:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Executar o aplicativo do dispositivo simulado][img-simulated-device]

2. No prompt de comando-na pasta enviar mensagens de c2d, execute o seguinte comando para enviar uma mensagem de nuvem para dispositivo e aguarde uma confirmação de comentários:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Execute o comando para enviar a mensagem de nuvem para dispositivo][img-send-command]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a enviar e receber mensagens de nuvem para dispositivo. 

Para ver exemplos de soluções de ponta a ponta completas que usam IoT Hub, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com IoT Hub, consulte o [Guia de desenvolvedor do IoT Hub].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introdução ao IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guia de desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Central de desenvolvedores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Pacote de IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/