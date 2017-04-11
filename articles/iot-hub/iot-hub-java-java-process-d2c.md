<properties
    pageTitle="Processar mensagens de dispositivo à nuvem IoT Hub (Java) | Microsoft Azure"
    description="Siga este tutorial Java para saber padrões úteis para processar mensagens do IoT Hub dispositivo na nuvem."
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
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Tutorial: Como processar mensagens de dispositivo à nuvem do IoT Hub usando Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que permite confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e um aplicativo back-end. Outros tutoriais ([Introdução ao IoT Hub] e [Enviar mensagens de nuvem para dispositivo com IoT Hub][lnk-c2d]) mostram como usar a dispositivo na nuvem e nuvem para dispositivo mensagens funcionalidade básica de IoT Hub.

Este tutorial se baseia o código mostrado no tutorial [começar a usar o Hub de IoT] e mostra dois padrões scalable que você pode usar para processar mensagens de dispositivo à nuvem:

- O armazenamento confiável das mensagens de dispositivo à nuvem no [armazenamento de blob do Microsoft Azure]. Um cenário comum é a análise de *caminho frio* , na qual você armazena dados de telemetria em bolhas para usar como entrada em processos de análise. Esses processos podem ser controlados por ferramentas como [Fábrica de dados do Azure] ou a pilha de [HDInsight (Hadoop)] .

- O processamento confiável das mensagens de dispositivo à nuvem *interativo* . Mensagens de dispositivo à nuvem são interativas quando elas são disparadores imediatos para um conjunto de ações no back-end do aplicativo. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que aciona inserindo uma permissão em um sistema CRM. Por outro lado, mensagens de *ponto de dados* simplesmente feed em um mecanismo de análise. Por exemplo, telemetria de temperatura de um dispositivo que será armazenado para análise posterior é uma mensagem de ponto de dados.

Porque IoT Hub expõe um [Hub de evento][lnk-event-hubs]-ponto de extremidade compatível para receber mensagens de dispositivo na nuvem, neste tutorial usa uma instância de [EventProcessorHost] . Esta instância:

* Confiavelmente armazena mensagens de *ponto de dados* em armazenamento de blob do Microsoft Azure.
* Encaminha *interativo* mensagens de dispositivo à nuvem para uma Azure [fila de barramento de serviço] para processamento imediato.

Barramento de serviço ajuda a garantir confiável processamento de mensagens interativas, pois fornece pontos de verificação por mensagem e tempo janela de duplicação.

> [AZURE.NOTE] Uma instância de **EventProcessorHost** é apenas uma maneira de processar mensagens interativas. Outras opções incluem [Azure serviço tecidos] [ lnk-service-fabric] e [A análise de fluxo Azure][lnk-stream-analytics].

No final deste tutorial, você executar três aplicativos de console de Java:

* **dispositivo simulado**, uma versão modificada do aplicativo criado no tutorial [começar a usar o Hub de IoT] , envia mensagens de dispositivo à nuvem do ponto de dados todas as mensagens de dispositivo à nuvem segundo e interativo cada 10 segundos. Este aplicativo usa o protocolo AMQP para se comunicar com IoT Hub.
* **processar mensagens de d2c** usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com o evento Hub. Ele e confiavelmente armazena mensagens de ponto de dados em armazenamento de blob do Microsoft Azure e encaminhe mensagens interativas para uma fila de barramento de serviço.
* **processo interativo mensagens** eliminação filas as mensagens interativas da fila barramento de serviço.

> [AZURE.NOTE] Hub de IoT tem suporte SDK muitas plataformas de dispositivo e idiomas, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo simulado neste tutorial com um dispositivo físico e como conectar dispositivos a um IoT Hub, consulte o [Azure IoT Developer Center].

Este tutorial é diretamente aplicável a outras maneiras de consumir mensagens de evento Hub compatível, como projetos de [HDInsight (Hadoop)] . Para obter mais informações, consulte o [guia de desenvolvedor do Azure IoT Hub - dispositivo para a nuvem].

Para concluir este tutorial, você precisa do seguinte:

+ Uma versão completa do trabalho do tutorial [Introdução ao IoT Hub] .

+ Java SE 8. <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Java para este tutorial em Windows ou Linux.

+ Maven 3.  <br/> [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial em Windows ou Linux.

+ Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você deve ter algum conhecimento básico de [Armazenamento do Azure] e [Barramento de serviço do Azure].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensagens interativas em um dispositivo simulado

Nesta seção, você modificar o aplicativo de dispositivo simulado que você criou no tutorial [Introdução ao IoT Hub] para enviar mensagens de dispositivo à nuvem interativas para o hub de IoT.

1. Use um editor de texto para abrir o arquivo de simulated-device\src\main\java\com\mycompany\app\App.java. Este arquivo contém o código para o aplicativo do **dispositivo simulado** que você criou o tutorial de [Introdução ao IoT Hub] .

2. Adicione a seguinte classe aninhada para a classe de **aplicativo** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Essa classe é semelhante à classe **MessageSender** do projeto do **dispositivo simulado** . As únicas diferenças são que você agora defina a propriedade do sistema **MessageId** , e uma propriedade personalizada chamado **messageType**.
    O código atribui um identificador exclusivo universal (UUID) para a propriedade **MessageId** . O barramento de serviço pode usar este identificador eliminação duplicar as mensagens recebidas. O exemplo utiliza a propriedade **messageType** distinguir interativo das mensagens de ponto de dados. O aplicativo passa essas informações nas propriedades de mensagem, em vez de no corpo da mensagem, para que o processador de evento não precisa desserializar a mensagem para executar o roteamento de mensagens.

    > [AZURE.NOTE] É importante criar o **MessageId** usado para eliminar a duplicação de mensagens interativas no código de dispositivo. Comunicações de rede intermitente ou outras falhas, podem resultar em várias retransmissões da mesma mensagem do dispositivo. Você também pode usar uma ID de mensagem semântico, como um hash dos campos de dados relevantes de mensagem, no lugar de um UUID.

3. Modificar o método **principal** para enviar as duas mensagens interativas e ponto de dados mensagens conforme mostrado no seguinte trecho de código:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar uma política de repetição como retirada exponencial, conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

5. Para criar o aplicativo de **dispositivo simulado** usando Maven, execute o seguinte comando no prompt de comando na pasta do dispositivo simulado:

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Processar mensagens de dispositivo à nuvem

Nesta seção, você criar um aplicativo de console de Java que processa mensagens de dispositivo à nuvem do IoT Hub. Hub de IOT expõe um [Hub de evento]-ponto de extremidade compatível habilitar um aplicativo ler mensagens de dispositivo na nuvem. Este tutorial usa a classe [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para obter mais informações sobre como processar mensagens de Hubs de evento, consulte o tutorial de [Introdução com Hubs de evento] .

O desafio principal quando você implementa armazenamento confiável das mensagens de ponto de dados ou encaminhamento de mensagens interativas, é que o processamento de eventos depende do consumidor de mensagem para fornecer pontos de verificação para seu andamento. Além disso, para obter uma taxa de transferência alta, quando você lê Hubs de eventos que você deve fornecer pontos de verificação em lotes grandes. Essa abordagem cria a possibilidade de processamento duplicados para um grande número de mensagens, se houver uma falha e você reverter para o ponto de verificação anterior. Neste tutorial, você verá como sincronizar gravações de armazenamento do Azure e windows de duplicação barramento de serviço com pontos de verificação de **EventProcessorHost** .

Para escrever mensagens de armazenamento do Azure confiável, o exemplo usa o recurso de confirmação de bloco individual do [bloco blobs][Azure Block Blobs]. O processador de evento acumula mensagens na memória até o momento para fornecer um ponto de verificação. Por exemplo, após o buffer acumulado das mensagens atinge o tamanho máximo de bloco de 4 MB, ou após o barramento de serviço duplicação janela de tempo decorrido. Em seguida, antes do ponto de verificação, o código se compromete um novo bloco com o blob.

O processador de evento usa Hubs de evento mensagem desloca como bloco IDs. Esse mecanismo permite que o processador de evento executar uma verificação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado de uma falha possível entre confirmar um bloco e o ponto de verificação.

> [AZURE.NOTE] Este tutorial usa uma única conta de armazenamento do Azure para gravar todas as mensagens recuperadas a partir do IoT Hub. Para decidir se você precisa usar várias contas de armazenamento do Azure em sua solução, consulte [diretrizes de escalabilidade de armazenamento do Azure].

O aplicativo usa o recurso de duplicação barramento de serviço para evitar duplicatas quando ele processa mensagens interativas. O dispositivo simulado marca cada mensagem interativa com exclusivo **MessageId**. Este id barramento de serviço para garantir que, na janela de tempo especificado duplicação, sem duas mensagens com a mesma **MessageId** são entregues os receptores. Essa duplicação, junto com a semântica de conclusão por mensagem fornecida por filas de barramento de serviço, torna mais fácil implementar o processamento confiável de mensagens interativas.

Para certificar-se de que nenhuma mensagem é enviada novamente fora da janela de duplicação, o código sincroniza o mecanismo de ponto de verificação de **EventProcessorHost** com a janela de duplicação da fila de barramento de serviço. Essa sincronização é feita forçando um ponto de verificação pelo menos uma vez sempre que a janela de duplicação ociosa (neste tutorial, a janela é uma hora).

> [AZURE.NOTE] Este tutorial usa uma única fila de barramento de serviço particionada para todas as mensagens interativas recuperadas de IoT Hub de processo. Para obter mais informações sobre como usar filas de barramento de serviço para atender a requisitos de escalabilidade de sua solução, consulte a documentação de [Barramento de serviço do Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurar uma conta de armazenamento do Azure e uma fila de barramento de serviço

Para usar a classe de [EventProcessorHost] , você deve ter uma conta de armazenamento do Azure para habilitar o **EventProcessorHost** registrar informações de ponto de verificação. Você pode usar uma conta de armazenamento do Azure existente ou siga as instruções em [Sobre o armazenamento do Azure] para criar um novo. Anote a cadeia de conexão de conta de armazenamento do Azure.

> [AZURE.NOTE] Quando você copia e cola a cadeia de conexão de conta de armazenamento do Azure, verifique se há sem espaços incluídos.

Você também precisa uma fila de barramento de serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila programaticamente, com uma janela de duplicação de uma hora, como explicado em [como usar o serviço barramento filas][fila de barramento de serviço]. Como alternativa, você pode usar o [portal clássico Azure][lnk-classic-portal], seguindo estas etapas:

1. No canto inferior esquerdo, clique em **novo** . Clique em **Serviços de aplicativo** > **Barramento de serviço** > **fila** > **Criar personalizada**. Insira o nome **d2ctutorial**, selecione uma região e use um namespace existente ou crie um novo. Anote o nome do namespace, você precisa-la mais tarde neste tutorial. Na próxima página, selecione **Habilitar detecção de duplicidades**e definir a **janela de tempo do histórico de detecção de duplicar** para uma hora. Clique na marca de seleção no canto inferior direito para salvar sua configuração de fila.

    ![Criar uma fila no portal do Azure][30]

2. Na lista de filas de barramento de serviço, clique em **d2ctutorial**e, em seguida, clique em **Configurar**. Crie duas diretivas de acesso compartilhado, um chamado **Enviar** com permissões **Enviar** e chamado um **ouvir** com permissões de **ouvir** . Anote os valores de **chave primária** para ambas as diretivas, você precisa-las mais tarde neste tutorial. Quando terminar, clique em **Salvar** na parte inferior.

    ![Configurar uma fila no portal do Azure][31]

### <a name="create-the-event-processor"></a>Criar o processador de evento

Nesta seção, você criar um aplicativo Java para processar mensagens do ponto de extremidade compatível com o evento Hub.

A primeira tarefa é adicionar um projeto de Maven chamado **processar-d2c-mensagens** que recebe mensagens de dispositivo à nuvem do ponto de extremidade compatível com o evento de Hub IoT Hub e roteia as mensagens para outros serviços de back-end.

1. Na pasta iot java-get-iniciados que você criou o tutorial de [Introdução ao IoT Hub] , crie um projeto de Maven chamado **processar-d2c-mensagens** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de processo de d2c de mensagens.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta processo de d2c de mensagens e adicione as seguintes dependências para o nó de **dependências** . Essas dependências permitem que você usar o azure-eventhubs, azure-eventhubs-eph e pacotes de servicebus azure em seu aplicativo para interagir com seu hub IoT e fila de barramento de serviço:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

A próxima tarefa é adicionar uma classe **ErrorNotificationHandler** ao projeto.

1. Use um editor de texto para criar um arquivo de process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Adicione o seguinte código ao arquivo para exibir mensagens de erro da instância **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Salve e feche o arquivo ErrorNotificationHandler.java.

Agora você pode adicionar uma classe que implementa a interface **IEventProcessor** . A classe **EventProcessorHost** chama essa classe para processar mensagens de dispositivo à nuvem recebidas de IoT Hub. O código nessa classe implementa a lógica para armazenar mensagens confiavelmente em um contêiner de blob e encaminhar mensagens interativas para fila barramento de serviço.

O método **onEvents** define a variável de **latestEventData** que controla o número de deslocamento e sequência da mensagem mais recente lido por esse processador de evento. Lembre-se de que cada processador é responsável por uma única partição. O método **onEvents** , em seguida, recebe um lote de mensagens do IoT Hub e processos-los da seguinte maneira: ele envia mensagens interativas para a fila de barramento de serviço e acrescenta o buffer de memória **toAppend** mensagens de ponto de dados. Se o buffer de memória atinge o limite de bloco de 4 MB ou as janelas de tempo de duplicação ociosa (uma hora após o último ponto de verificação neste tutorial), o método dispara um ponto de verificação.

O método **AppendAndCheckPoint** primeiro gera um **blockId** para o bloco acrescentar ao blob. Armazenamento do Azure requer que todos bloquear IDs para ter o mesmo comprimento, para que o método preencha o deslocamento com zeros à esquerda. Em seguida, se um bloco com essa ID já estiver no blob, o método substitui-lo com o conteúdo de buffer atual.

> [AZURE.NOTE] Para simplificar o código, este tutorial usa um único blob por partição para armazenar as mensagens. Uma solução real poderia implementar sem interrupção criando arquivos adicionais após um determinado período de tempo ou quando eles atingirem um determinado tamanho de arquivo. Lembre-se de que um blob de bloco Azure pode conter no máximo 195 GB de dados.

A próxima tarefa é implementar a interface **IEventProcessor** :

1. Use um editor de texto para criar um arquivo de process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.

2. Adicione o seguintes importações e a definição de classe para o arquivo de EventProcessor.java. A classe **EventProcessor** implementa a interface **IEventProcessor** que define o comportamento do cliente Hubs de evento:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Adicione os seguintes métodos para a classe **EventProcessor** para implementar a interface **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Adicione as seguintes variáveis de nível de classe à classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Adicione um método de **AppendAndCheckPoint** com a assinatura a seguir à classe **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Adicione o seguinte código para o método **AppendAndCheckPoint** para recuperar o deslocamento e sequência número da mensagem atual na partição:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. No método **AppendAndCheckPoint** , use o valor de deslocamento atual para criar uma instância de **BlockEntry** para o próximo bloco salvar o blob:

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. No método **AppendAndCheckPoint** , carregue o último conjunto de mensagens para o blob de bloco e recuperar a lista atual de blocos:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. No método **AppendAndCheckPoint** , criar o bloco inicial em um novo blob ou acrescentar o bloco para o blob existente:

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Finalmente no método **AppendAndCheckPoint** , crie um ponto de verificação na partição e preparar para salvar o próximo bloco de mensagens:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. No método **onEvents** , adicione o seguinte código para receber mensagens do ponto de extremidade do IoT Hub e encaminhar mensagens interativas na fila do barramento de serviço. Chame o método **AppendAndCheckPoint** quando o bloco é completo ou o tempo limite expirar:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Finalmente no método **onEvents** , adicione uma cláusula 'else if' para chamar o **AppendAndCheckPoint** se o tempo limite expirar enquanto não existem mensagens vêm do IoT Hub:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Salve as alterações no arquivo EventProcessor.java.

A tarefa final do projeto de **processo de d2c de mensagens** é adicionar código para o método **principal** que cria uma instância de **EventProcessorHost** .

1. Use um editor de texto para abrir o arquivo de process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Adicione as seguintes instruções de **Importar** o arquivo:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Adicione a seguinte variável de nível de classe para a classe **App** . Substitua **{yourstorageaccountconnectionstring}** com a cadeia de conexão da conta de armazenamento do Azure feitas anote anteriormente na seção [provisionar uma conta de armazenamento do Azure e uma fila de barramento de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Adicione as seguintes variáveis de nível de classe para a classe de **aplicativo** e substituir **{yourservicebusnamespace}** com namespace barramento de serviço e **{yourservicebussendkey}** **Enviar** chave da sua fila. Você fez anteriormente uma anotação dos valores de chave namespace e **escutar** na seção [provisionar uma conta de armazenamento do Azure e uma fila de barramento de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Adicione as seguintes variáveis de nível de classe para a classe **App** . Substitua o valor de ponto de extremidade compatível com o evento Hub **{youreventhubcompatibleendpoint}** . O valor de ponto de extremidade parecido com **sua … namespace** para que você deve remover o **sb: / /** prefixo e sufixo a **.servicebus.windows.net/** . Substitua **{youreventhubcompatiblename}** com o nome do evento Hub compatível. Substitua **{youriothubkey}** com a chave de **iothubowner** . Você fez uma anotação desses valores na [criar um IoT Hub] [ lnk-create-an-iot-hub] seção o tutorial de *Introdução ao Azure IoT Hub para Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modifice a assinatura do método **principal** da seguinte maneira:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Adicione o seguinte código para o método **principal** para obter uma referência para o contêiner de blob que armazena as mensagens:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Adicione o seguinte código para o método **principal** para obter uma referência para o serviço de barramento de serviço:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. No método **principal** , configurar e criar uma instância de **EventProcessorHost** . A opção **setInvokeProcessorAfterReceiveTimeout** assegura que a instância de **EventProcessorHost** chama o método de **onEvents** na interface **IEventProcessor** mesmo quando não existem mensagens para processar. O método **onEvents** , em seguida, sempre chama o método **AppendAndCheckPoint** quando o tempo limite expirar.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. No método **principal** , registre a implementação de **IEventProcessor** com a instância de **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Por fim, adicione lógica para o método **principal** para desligar a instância de **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Salve e feche o arquivo process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. Para criar o aplicativo de **mensagens de d2c de processo** usando Maven, execute o seguinte comando no prompt de comando na pasta processo de d2c de mensagens:

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Receber mensagens interativas

Nesta seção, você escrever um aplicativo de console de Java que recebe as mensagens interativas da fila barramento de serviço.

A primeira tarefa é adicionar um projeto de Maven chamado **processo interativo mensagens** que recebe mensagens enviadas na fila barramento de serviço das instâncias de **EventProcessor** .

1. Na pasta iot java-get-iniciados que você criou o tutorial de [Introdução ao IoT Hub] , crie um projeto de Maven chamado **processo interativo mensagens** usando o seguinte comando no prompt de comando. Observe que esse é um comando longo, único:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu prompt de comando, navegue para a nova pasta de processo interativo mensagens.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta processo interativo mensagens e adicionar a seguinte dependência ao nó **dependências** . Essa dependência permite que você use o pacote do azure servicebus em seu aplicativo para interagir com sua fila de barramento de serviço:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

A próxima tarefa é adicionar código para recuperar mensagens da fila barramento de serviço.

1. Use um editor de texto para abrir o arquivo de process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Adicione o seguinte `import` instruções para o arquivo:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Adicione as seguintes variáveis de nível de classe para a classe de **aplicativo** e substituir **{yourservicebusnamespace}** com namespace barramento de serviço e **{yourservicebuslistenkey}** **ouvir** chave da sua fila. Você fez anteriormente uma anotação dos valores de chave namespace e **escutar** na seção [provisionar uma conta de armazenamento do Azure e uma fila de barramento de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Adicione a seguinte classe aninhada para a classe de **aplicativo** para receber mensagens de fila:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modifice a assinatura do método **principal** da seguinte maneira:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. No método **principal** , adicione o seguinte código para começar a ouvir para novas mensagens:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Salve e feche o arquivo process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. Para criar o aplicativo de **processo interativo mensagens** usando Maven, execute o seguinte comando no prompt de comando na pasta processo interativo mensagens:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os três aplicativos.

1.  Para executar o aplicativo de **processo interativo mensagens** , em um prompt de comando ou shell, navegue até a pasta processo interativo mensagens e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o processo interativo mensagens][processinteractive]

2.  Para executar o aplicativo de **mensagens de d2c de processo** , navegue até a pasta de mensagens de d2c de processo em um prompt de comando ou shell e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o processo de d2c de mensagens][processd2c]

3.  Para executar o aplicativo do **dispositivo simulado** , em um prompt de comando ou shell, navegue até a pasta do dispositivo simulado e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o dispositivo simulado][simulateddevice]

> [AZURE.NOTE] Para ver as atualizações no seu blob, talvez você precise reduzir a constante **MAX_BLOCK_SIZE** da classe **StoreEventProcessor** para um valor menor, como **1024**. Essa alteração é útil porque ele leva algum tempo para atingir o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho menor do bloco, você não precisa esperar tanto para ver o blob sendo criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais escaláveis.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como processar confiavelmente interativas mensagens de dispositivo na nuvem e ponto de dados usando a classe [EventProcessorHost] .

[Como enviar mensagens de nuvem para dispositivo com IoT Hub] [ lnk-c2d] mostra como enviar mensagens para seus dispositivos de back-end.

Para ver exemplos de soluções de ponta a ponta completas que usam IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com IoT Hub, consulte o [Guia de desenvolvedor do IoT Hub].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Armazenamento de blob do Microsoft Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Fábrica de dados do Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Fila de barramento de serviço]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guia de desenvolvedor do Azure IoT Hub - dispositivo para nuvem]: iot-hub-devguide-messaging.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de serviço Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia de desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao IoT Hub]: iot-hub-java-java-getstarted.md
[Central de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Começar com Hubs de evento]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Escalabilidade de armazenamento Azure diretrizes]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub