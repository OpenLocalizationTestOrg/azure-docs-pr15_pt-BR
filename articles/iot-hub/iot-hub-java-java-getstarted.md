<properties
    pageTitle="Hub de IoT Azure para Java Introdução | Microsoft Azure"
    description="Hub de IoT Azure com Java obtendo iniciado tutorial. Use o Azure IoT Hub e Java com o SDK de IoT do Microsoft Azure para implementar uma solução de Internet das coisas."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Introdução ao Azure IoT Hub para Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, você tem três aplicativos de console de Java:

* **criar dispositivo-identidade**, que cria um dispositivo identidade e a chave de segurança associadas para se conectar seu dispositivo simulado.
* **ler mensagens de d2c**, que exibe a telemetria enviada por seu dispositivo simulado.
* **dispositivo simulado**, que se conecta ao seu hub IoT com a identidade do dispositivo que criou anteriormente e envia uma mensagem de telemetria cada segundo usando o protocolo AMQP.

> [AZURE.NOTE] O artigo [IoT Hub SDKs] [ lnk-hub-sdks] fornece informações sobre o SDK do vários que você pode usar para criar aplicativos executados em dispositivos e back-end sua solução.

Para concluir este tutorial, você precisa do seguinte:

+ Java SE 8. <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Java para este tutorial em Windows ou Linux.

+ Maven 3.  <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial em Windows ou Linux.

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como uma etapa final, anote o valor de **chave primária** e clique em **sistema de mensagens**. Na lâmina **Messaging** , anote o **nome do evento Hub compatível** e o **ponto de extremidade compatível com o evento Hub**. Você precisará esses três valores quando você cria seu aplicativo de **ler mensagens de d2c** .

![Azure blade portal IoT Hub mensagens][6]

Agora você criou seu hub IoT e você possui o Hub de IoT hostname, cadeia de conexão do IoT Hub, chave primária do Hub IoT, compatível com o evento Hub nome e ponto de extremidade compatível com o evento Hub que você precisa concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta seção, você criar um aplicativo de console de Java que cria uma nova identidade de dispositivo no registro identidade em seu hub IoT. Um dispositivo não pode se conectar a IoT hub, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Consulte a seção de **Registro de identidade de dispositivos** do [Guia de desenvolvedor do Hub IoT] [ lnk-devguide-identity] para obter mais informações. Quando você executa este aplicativo de console, ele gera uma identificação de dispositivo exclusivo e chave que seu dispositivo pode usar para se identificar quando ele envia mensagens de dispositivo à nuvem para IoT Hub.

1. Crie uma nova pasta vazia chamada iot java-get-iniciados. Na pasta iot java-get-iniciados, crie um novo projeto de Maven chamado **criar dispositivo-identidade** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de criar dispositivo-identidade.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta criar de dispositivo de identidade e adicionar a seguinte dependência ao nó **dependências** . Isso permite que você use o sdk do serviço de iothub um pacote em seu aplicativo:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o arquivo:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe para a classe de **aplicativo** , substituindo anteriormente o seu observado **{yourhubconnectionstring}** pelo valor:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modifice a assinatura do método **principal** para incluir as exceções da seguinte maneira:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Adicione o seguinte código como o corpo do método **principal** . Esse código cria um dispositivo chamado *javadevice* no seu registro de identidade IoT Hub se ainda não exista. Ele exibe o id do dispositivo e a chave que você precisa de mais tarde:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Salve e feche o arquivo App.java.

11. Para criar o aplicativo de **criação de dispositivo de identidade** usando Maven, execute o seguinte comando no prompt de comando na pasta criar de dispositivo de identidade:

    ```
    mvn clean package -DskipTests
    ```

12. Para executar o aplicativo de **criação de dispositivo de identidade** usando Maven, execute o seguinte comando no prompt de comando na pasta criar de dispositivo de identidade:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Anote a **id do dispositivo** e a **chave do dispositivo**. Você precisará mais tarde quando você cria um aplicativo que se conecta ao IoT Hub como um dispositivo.

> [AZURE.NOTE] O registro de identidade IoT Hub armazena apenas identidades do dispositivo para habilitar o acesso seguro ao hub. Ele armazena identificações de dispositivo e teclas para usar como credenciais de segurança e um sinalizador de ativado/desativado que você pode usar para desabilitar o acesso para um dispositivo individual. Se seu aplicativo precisa armazenar outros metadados específicos do dispositivo, ele deve usar uma loja de aplicativos específicos. Consulte o [Guia de desenvolvedor do Hub IoT] [ lnk-devguide-identity] para obter mais informações.

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de dispositivo à nuvem

Nesta seção, você criar um aplicativo de console de Java que lê mensagens de dispositivo à nuvem do IoT Hub. Um hub IoT expõe um [Hub de evento][lnk-event-hubs-overview]-ponto de extremidade compatível para que você possa ler mensagens de dispositivo na nuvem. Para manter as coisas simples, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. As [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial mostra como processar mensagens de dispositivo à nuvem em escala. A [Começar com Hubs de evento] [ lnk-eventhubs-tutorial] tutorial fornece mais informações sobre como processar mensagens de evento Hubs e aplica-se a pontos de extremidade compatível com o evento de Hub IoT Hub.

> [AZURE.NOTE] O ponto de extremidade compatível com o evento Hub para ler mensagens de dispositivo à nuvem sempre usa o protocolo AMQP.

1. Na pasta iot java-get-iniciados que você criou na seção *criar uma identidade do dispositivo* , crie um novo projeto de Maven chamado **ler mensagens de d2c** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de ler mensagens de d2c.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta ler mensagens de d2c e adicione a seguinte dependência para o nó de **dependências** . Isso permite que você usar o pacote do cliente eventhubs em seu aplicativo para ler do ponto de extremidade compatível com o evento Hub:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o arquivo:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Adicione as seguintes variáveis de nível de classe para a classe **App** . Substitua **{youriothubkey}**, **{youreventhubcompatibleendpoint}**e **{youreventhubcompatiblename}** com os valores que você anotou anteriormente:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Adicione o seguinte método de **receiveMessages** para a classe **App** . Este método cria uma instância de **EventHubClient** para se conectar ao ponto de extremidade compatível com o evento Hub e cria uma instância de **PartitionReceiver** a leitura de uma partição de Hub de evento assíncrona. Ele loops continuamente e imprime os detalhes da mensagem até que o aplicativo termina.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Este método usa um filtro quando cria o receptor para que o destinatário lê somente as mensagens enviadas a IoT Hub após o receptor começa a ser executado. Isso é útil em um ambiente de teste para que você possa ver o conjunto atual de mensagens. Em um ambiente de produção, seu código deverá garantir que ele processa todas as mensagens - veja [como processar mensagens de dispositivo à nuvem IoT Hub] [ lnk-process-d2c-tutorial] tutorial para obter mais informações.

9. Modifice a assinatura do método **principal** para incluir a exceção da seguinte maneira:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Adicione o seguinte código para o método **principal** na classe **App** . Este código cria as duas instâncias de **EventHubClient** e **PartitionReceiver** e permite que você feche o aplicativo quando terminar de processar mensagens:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Esse código supõe que você criou seu hub IoT na camada F1 (gratuita). Um hub IoT livre tem duas partições denominadas "0" e "1".

11. Salve e feche o arquivo App.java.

12. Para criar o aplicativo de **ler mensagens de d2c** usando Maven, execute o seguinte comando no prompt de comando na pasta ler mensagens de d2c:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você criar um aplicativo de console de Java que simula um dispositivo que envia mensagens de dispositivo à nuvem para um hub IoT.

1. Na pasta iot java-get-iniciados que você criou na seção *criar uma identidade do dispositivo* , crie um novo projeto Maven chamado **dispositivo simulado** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de dispositivo simulada.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta do dispositivo simulado e adicione as seguintes dependências para o nó de **dependências** . Isso permite que você use o pacote de iothub-java-client em seu aplicativo para se comunicar com seu hub IoT e serializar objetos Java como JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o arquivo:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Adicione as seguintes variáveis de nível de classe para a classe de **aplicativo** , substituindo **{youriothubname}** com nome do hub IoT e **{yourdevicekey}** com o valor de chave de dispositivo gerado na seção *criar uma identidade do dispositivo* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Este aplicativo de exemplo usa a variável de **protocolo** quando ele cria um objeto **DeviceClient** . Você pode usar o protocolo HTTP ou AMQP para se comunicar com IoT Hub.

8. Adicione que o seguinte aninhada classe de **TelemetryDataPoint** dentro da classe de **aplicativo** para especificar os dados de telemetria que seu dispositivo envia para seu hub IoT:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Adicione a seguinte classe **EventCallback** aninhada dentro da classe de **aplicativo** para exibir o status de confirmação que o hub de IoT retorna quando ele processa uma mensagem a partir do dispositivo simulado. Este método também notifica o segmento principal no aplicativo quando a mensagem foi processada:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Adicione a seguinte classe **MessageSender** aninhada dentro da classe de **aplicativo** . O método **Executar** nesta classe gera dados de telemetria de amostra para enviar para o seu hub IoT e aguarda uma confirmação antes de enviar a próxima mensagem:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Este método envia uma nova mensagem de dispositivo à nuvem um segundo depois que o hub de IoT confirma a mensagem anterior. A mensagem contém um objeto serializado JSON com o deviceId e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

11. Substitua o método **principal** com o seguinte código que cria um thread para enviar mensagens de dispositivo à nuvem para seu hub IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Salve e feche o arquivo App.java.

13. Para criar o aplicativo de **dispositivo simulado** usando Maven, execute o seguinte comando no prompt de comando na pasta do dispositivo simulado:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como uma retirada exponencial), conforme sugerido no artigo da MSDN [Tratamento de falhas temporárias][lnk-transient-faults].

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando-na pasta d2c de leitura, execute o seguinte comando para começar a monitorar a primeira partição no seu hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de cliente do serviço de Java IoT Hub para monitorar mensagens do dispositivo na nuvem][7]

2. No prompt de comando-na pasta do dispositivo simulado, execute o seguinte comando para começar a enviar dados de telemetria para seu hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Aplicativo de cliente Java IoT Hub dispositivo para enviar mensagens de dispositivo à nuvem][8]

3. O bloco de **uso** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![Azure portal uso lado a lado mostrando o número de mensagens enviadas para IoT Hub][43]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um hub IoT novo no portal do Azure e depois criado uma identidade de dispositivo no registro de identidade do hub. Você usou este dispositivo identidade para habilitar o aplicativo do dispositivo simulado enviar mensagens de dispositivo à nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub. 

Para continuar a introdução ao IoT Hub e explorar outros cenários IoT consulte:

- [Conectar seu dispositivo][lnk-connect-device]
- [Introdução ao gerenciamento de dispositivo][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como estender sua solução IoT e processar mensagens de dispositivo à nuvem em escala, consulte as [mensagens de dispositivo à nuvem do processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/