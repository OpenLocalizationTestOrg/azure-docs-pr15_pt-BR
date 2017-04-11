<properties
    pageTitle="Processar mensagens de dispositivo à nuvem IoT Hub (.Net) | Microsoft Azure"
    description="Siga este tutorial para saber padrões úteis para processar mensagens do IoT Hub dispositivo na nuvem."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Tutorial: Como processar mensagens de dispositivo à nuvem IoT Hub usando .net

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

No final deste tutorial, você executar três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [começar a usar o Hub de IoT] , envia dados ponto dispositivo à nuvem mensagens de todas as mensagens de dispositivo à nuvem segundo e interativo cada 10 segundos. Este aplicativo usa o protocolo AMQP para se comunicar com IoT Hub.
* **ProcessDeviceToCloudMessages** usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com o evento Hub. Ele e confiavelmente armazena mensagens de ponto de dados em armazenamento de blob do Microsoft Azure e encaminhe mensagens interativas para uma fila de barramento de serviço.
* **ProcessD2CInteractiveMessages** eliminação filas as mensagens interativas da fila barramento de serviço.

> [AZURE.NOTE] Hub de IoT tem suporte SDK muitas plataformas de dispositivo e idiomas, incluindo C, Java e JavaScript. Para saber como substituir o dispositivo simulado neste tutorial com um dispositivo físico e como conectar dispositivos a um IoT Hub, consulte o [Azure IoT Developer Center].

Este tutorial é diretamente aplicável a outras maneiras de consumir mensagens de evento Hub compatível, como projetos de [HDInsight (Hadoop)] . Para obter mais informações, consulte o [guia de desenvolvedor do Azure IoT Hub - dispositivo para a nuvem].

Para concluir este tutorial, você precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você deve ter algum conhecimento básico de [Armazenamento do Azure] e [Barramento de serviço do Azure].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensagens interativas em um dispositivo simulado

Nesta seção, você modificar o aplicativo de dispositivo simulado que você criou no tutorial [Introdução ao IoT Hub] para enviar mensagens de dispositivo à nuvem interativas para o hub de IoT.

1. No Visual Studio, no projeto **SimulatedDevice** , adicione o seguinte método à classe **programa** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Esse método é semelhante ao método **SendDeviceToCloudMessagesAsync** no projeto **SimulatedDevice** . As únicas diferenças são que você agora defina a propriedade do sistema **MessageId** , e uma propriedade de usuário chamada **messageType**.
    O código atribui um identificador exclusivo (GUID) para a propriedade **MessageId** . O barramento de serviço pode usar este identificador eliminação duplicar as mensagens recebidas. O exemplo utiliza a propriedade **messageType** distinguir interativa de mensagens de ponto de dados. O aplicativo passa essas informações nas propriedades de mensagem, em vez de no corpo da mensagem, para que o processador de evento não precisa desserializar a mensagem para executar o roteamento de mensagens.

    > [AZURE.NOTE] É importante criar o **MessageId** usado para eliminar a duplicação de mensagens interativas no código de dispositivo. Comunicações de rede intermitente ou outras falhas, podem resultar em várias retransmissões da mesma mensagem do dispositivo. Você também pode usar uma ID de mensagem semântico, como um hash dos campos de dados relevantes de mensagem, no lugar de um GUID.

2. Adicione o seguinte método no método **principal** , logo antes do `Console.ReadLine()` linha:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar uma política de repetição como retirada exponencial, conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="process-device-to-cloud-messages"></a>Processar mensagens de dispositivo à nuvem

Nesta seção, você criar um aplicativo de console do Windows que processa mensagens de dispositivo à nuvem do IoT Hub. Hub de IOT expõe um [Hub de evento]-ponto de extremidade compatível habilitar um aplicativo ler mensagens de dispositivo na nuvem. Este tutorial usa a classe [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para obter mais informações sobre como processar mensagens de Hubs de evento, consulte o tutorial de [Introdução com Hubs de evento] .

O desafio quando você implementar armazenamento confiável das mensagens de ponto de dados ou encaminhamento de mensagens interativos é que o processamento de evento depende o consumidor de mensagem para fornecer pontos de verificação para seu andamento. Além disso, para obter uma taxa de transferência alta, quando você lê Hubs de eventos que você deve fornecer pontos de verificação em lotes grandes. Essa abordagem cria a possibilidade de processamento duplicados para um grande número de mensagens se houver uma falha e você reverter para o ponto de verificação anterior. Neste tutorial, você verá como sincronizar gravações de armazenamento do Azure e windows de duplicação barramento de serviço com pontos de verificação de **EventProcessorHost** .

Para escrever mensagens de armazenamento do Azure confiável, o exemplo usa o recurso de confirmação de bloco individual do [bloco blobs][Azure Block Blobs]. O processador de evento acumula mensagens na memória até o momento para fornecer um ponto de verificação. Por exemplo, após o buffer acumulado das mensagens atinge o tamanho máximo de bloco de 4 MB, ou após o barramento de serviço duplicação janela de tempo decorrido. Em seguida, antes do ponto de verificação, o código se compromete um novo bloco com o blob.

O processador de evento usa Hubs de evento mensagem desloca como bloco IDs. Esse mecanismo permite que o processador de evento executar uma verificação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado de uma falha possível entre confirmar um bloco e o ponto de verificação.

> [AZURE.NOTE] Este tutorial usa uma única conta de armazenamento do Azure para gravar todas as mensagens recuperadas a partir do IoT Hub. Para decidir se você precisa usar várias contas de armazenamento do Azure em sua solução, consulte [diretrizes de escalabilidade de armazenamento do Azure].

O aplicativo usa o recurso de duplicação barramento de serviço para evitar duplicatas quando ele processa mensagens interativas. O dispositivo simulado marca cada mensagem interativa com exclusivo **MessageId**. Essas IDs habilitar barramento de serviço garantir que, na janela de tempo especificado duplicação, sem duas mensagens com a mesma **MessageId** são entregues os receptores. Essa duplicação, junto com a semântica de conclusão por mensagem fornecida por filas de barramento de serviço, torna mais fácil implementar o processamento confiável de mensagens interativas.

Para certificar-se de que nenhuma mensagem é enviada novamente fora da janela de duplicação, o código sincroniza o mecanismo de ponto de verificação de **EventProcessorHost** com a janela de duplicação da fila de barramento de serviço. Essa sincronização é feita forçando um ponto de verificação pelo menos uma vez sempre que a janela de duplicação ociosa (neste tutorial, a janela é uma hora).

> [AZURE.NOTE] Este tutorial usa uma única fila de barramento de serviço particionada para todas as mensagens interativas recuperadas de IoT Hub de processo. Para obter mais informações sobre como usar filas de barramento de serviço para atender a requisitos de escalabilidade de sua solução, consulte a documentação de [Barramento de serviço do Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurar uma conta de armazenamento do Azure e uma fila de barramento de serviço
Para usar a classe de [EventProcessorHost] , você deve ter uma conta de armazenamento do Azure para habilitar o **EventProcessorHost** registrar informações de ponto de verificação. Você pode usar uma conta de armazenamento do Azure existente ou siga as instruções em [Sobre o armazenamento do Azure] para criar um novo. Anote a cadeia de conexão de conta de armazenamento do Azure.

> [AZURE.NOTE] Quando você copia e cola a cadeia de conexão de conta de armazenamento do Azure, verifique se há sem espaços incluídos.

Você também precisa uma fila de barramento de serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila programaticamente, com uma janela de duplicação de uma hora, como explicado em [como usar o serviço barramento filas][fila de barramento de serviço]. Como alternativa, você pode usar o [portal clássico Azure][lnk-classic-portal], seguindo estas etapas:

1. No canto inferior esquerdo, clique em **novo** . Clique em **Serviços de aplicativo** > **Barramento de serviço** > **fila** > **Criar personalizada**. Insira o nome **d2ctutorial**, selecione uma região e use um namespace existente ou crie um novo. Na próxima página, selecione **Habilitar detecção de duplicidades**e definir a **janela de tempo do histórico de detecção de duplicar** para uma hora. Clique na marca de seleção no canto inferior direito para salvar sua configuração de fila.

    ![Criar uma fila no portal do Azure][30]

2. Na lista de filas de barramento de serviço, clique em **d2ctutorial**e, em seguida, clique em **Configurar**. Crie duas diretivas de acesso compartilhado, um chamado **Enviar** com permissões **Enviar** e chamado um **ouvir** com permissões de **ouvir** . Quando terminar, clique em **Salvar** na parte inferior.

    ![Configurar uma fila no portal do Azure][31]

3. Clique em **Painel de controle** na parte superior e, em seguida, **informações de Conexão** na parte inferior. Anote as cadeias de caracteres de conexão de duas.

    ![Painel de fila no portal do Azure][32]

### <a name="create-the-event-processor"></a>Criar o processador de evento

1. Na solução Visual Studio atual, para criar um projeto do Visual c# Windows usando o modelo de projeto de **Aplicativo de Console** , clique em **arquivo** > **Adicionar** > **Novo projeto**. Verifique se que a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **ProcessDeviceToCloudMessages**e clique **Okey**.

    ![Novo projeto no Visual Studio][10]

2. No Solution Explorer, clique com botão direito no projeto **ProcessDeviceToCloudMessages** e clique em **Gerenciar pacotes do NuGet**. Caixa de diálogo **NuGet Package Manager** é exibida.

3. Procurar **WindowsAzure.ServiceBus**, clique em **instalar**e aceite os termos de uso. Essa operação downloads, instalações e adiciona uma referência para o [pacote do NuGet de barramento de serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Procurar **Microsoft.Azure.ServiceBus.EventProcessorHost**, clique em **instalar**e aceite os termos de uso. Essa operação downloads, instalações e adiciona uma referência ao [Hub de evento de barramento de serviço do Azure - pacote EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

5. O projeto de **ProcessDeviceToCloudMessages** de atalho, clique em **Adicionar**e, em seguida, clique em **classe**. Nomeie a nova classe **StoreEventProcessor**e, em seguida, clique em **Okey** para criar a classe.

6. Adicione as instruções a seguir na parte superior do arquivo StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Substitua o seguinte código para o corpo da classe:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    A classe **EventProcessorHost** chama essa classe para processar mensagens de dispositivo à nuvem recebidas de IoT Hub. O código nessa classe implementa a lógica para armazenar mensagens confiavelmente em um contêiner de blob e encaminhar mensagens interativas para fila barramento de serviço.

    O método **OpenAsync** inicializa a variável **currentBlockInitOffset** , que controla o deslocamento atual da primeira mensagem, leia por esse processador de evento. Lembre-se de que cada processador é responsável por uma única partição.

    O método **ProcessEventsAsync** recebe um lote de mensagens do IoT Hub e processos-los da seguinte maneira: ele envia mensagens interativas para a fila de barramento de serviço e acrescenta mensagens de ponto de dados para o buffer de memória chamado **toAppend**. Se o buffer de memória atinge o limite de 4 MB ou as janelas de tempo de duplicação ociosa (uma hora após um ponto de verificação neste tutorial), o aplicativo dispara um ponto de verificação.

    O método **AppendAndCheckpoint** primeiro gera um blockId para o bloco de acrescentar. Requer o armazenamento do Azure todos bloquear IDs para ter o mesmo comprimento, para que o método preencha o deslocamento com zeros à esquerda - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Em seguida, se um bloco com essa ID já estiver no blob, o método substitui-lo com o conteúdo atual do buffer.

    > [AZURE.NOTE] Para simplificar o código, este tutorial usa um único blob por partição para armazenar as mensagens. Uma solução real poderia implementar sem interrupção criando arquivos adicionais após um determinado período de tempo ou quando eles atingirem um determinado tamanho de arquivo. Lembre-se de que um blob de bloco Azure pode conter no máximo 195 GB de dados.

8. Na classe **programa** , adicione a seguinte instrução **usando** na parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modificar o método **principal** da classe do **programa** da seguinte maneira. Substitua **{cadeia de conexão do hub iot}** com a cadeia de conexão **iothubowner** do tutorial de [Introdução ao IoT Hub] . Substitua a cadeia de conexão de armazenamento com a cadeia de conexão que você anotou no início desta seção. Substitua a cadeia de conexão do barramento de serviço com permissões de **Enviar** para a fila denominada **d2ctutorial** que você anotou no início desta seção:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Para simplificar, este tutorial usa uma única instância da classe [EventProcessorHost] . Para obter mais informações, consulte o [Guia de programação de Hubs de evento].

## <a name="receive-interactive-messages"></a>Receber mensagens interativas
Nesta seção, você escrever um aplicativo de console do Windows que recebe as mensagens interativas da fila barramento de serviço. Para obter mais informações sobre como projetar uma solução usando o barramento de serviço, consulte [criar aplicativos de vários níveis com barramento de serviço][].

1. Na solução atual Visual Studio, crie um projeto do Visual c# Windows usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **ProcessD2CInteractiveMessages**.

2. No Solution Explorer, clique com botão direito no projeto **ProcessD2CInteractiveMessages** e clique em **Gerenciar pacotes do NuGet**. Essa operação exibe a janela do **Gerenciador de pacotes do NuGet** .

3. Procurar **WindowsAzure.ServiceBus**, clique em **instalar**e aceite os termos de uso. Essa operação downloads, instalações e adiciona uma referência para o [Barramento de serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Adicione as seguintes instruções de **uso** na parte superior do arquivo **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Finalmente, adicione as seguintes linhas para o método **principal** . Substitua a cadeia de conexão com permissões de **ouvir** para a fila denominada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  No Visual Studio, no Solution Explorer, clique com botão direito sua solução e selecione **Definir projetos de inicialização**. Selecionar **vários projetos de inicialização**e, em seguida, selecione **Iniciar** como a ação para os projetos **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **ProcessD2CInteractiveMessages** .

2.  Pressione **F5** para iniciar os aplicativos de três console. O aplicativo **ProcessD2CInteractiveMessages** deve processar cada mensagem interativa enviada a partir do aplicativo **SimulatedDevice** .

  ![Três aplicativos de console][50]

> [AZURE.NOTE] Para ver as atualizações no seu blob, talvez você precise reduzir a constante **MAX_BLOCK_SIZE** da classe **StoreEventProcessor** para um valor menor, como **1024**. Essa alteração é útil porque ele leva algum tempo para atingir o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho menor do bloco, você não precisa esperar tanto para ver o blob sendo criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais escaláveis.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como processar confiavelmente interativas mensagens de dispositivo na nuvem e ponto de dados usando a classe [EventProcessorHost] .

[Como enviar mensagens de nuvem para dispositivo com IoT Hub] [ lnk-c2d] mostra como enviar mensagens para seus dispositivos de back-end.

Para ver exemplos de soluções de ponta a ponta completas que usam IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com IoT Hub, consulte o [Guia de desenvolvedor do IoT Hub].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Armazenamento de blob do Microsoft Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Fábrica de dados do Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Fila de barramento de serviço]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guia de desenvolvedor do Azure IoT Hub - dispositivo para nuvem]: iot-hub-devguide-messaging.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de serviço Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia de desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Central de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Começar com Hubs de evento]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Escalabilidade de armazenamento Azure diretrizes]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guia de programação de Hubs de evento]: ../event-hubs/event-hubs-programming-guide.md
[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Criar aplicativos de vários níveis com barramento de serviço]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
