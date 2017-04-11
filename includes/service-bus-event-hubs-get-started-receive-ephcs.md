## <a name="receive-messages-with-eventprocessorhost"></a>Receber mensagens com EventProcessorHost

[EventProcessorHost][] é uma classe de .NET que simplifica receba eventos de Hubs de evento pelo gerenciamento de pontos de verificação persistentes e paralelo recebe desses Hubs de evento. Usando [EventProcessorHost][], você pode dividir eventos entre vários receptores, mesmo quando hospedado em nós diferentes. Este exemplo mostra como usar [EventProcessorHost][] para um único destinatário. O exemplo de [processamento de escala out evento][] mostra como usar [EventProcessorHost][] com vários receptores.

Para usar [EventProcessorHost][], você deve ter uma [conta de armazenamento do Azure][]:

1. Faça logon no [portal do Azure][]e clique em **novo** na parte superior esquerda da tela.

2. Clique em **dados + armazenamento**, clique em **conta de armazenamento**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. Na lâmina **Criar conta de armazenamento** , digite um nome para a conta de armazenamento. Escolha uma assinatura do Azure, grupo de recursos e local criar o recurso. Clique em **criar**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. Na lista de contas de armazenamento, clique na conta de armazenamento recém-criado.

5. Na lâmina de conta de armazenamento, clique em **teclas de acesso**. Copie o valor de **CHAVE1** usar posteriormente neste tutorial.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual c# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto **receptor**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. No Solution Explorer, clique com botão direito a solução e clique em **Gerenciar pacotes NuGet para solução**.

6. Clique na guia **Procurar** , procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Certifique-se de que o nome do projeto (**receptor**) está especificado na caixa de **versões** . Clique em **instalar**e aceite os termos de uso.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

    Visual Studio downloads, instalações e adiciona uma referência ao [Hub de evento de barramento de serviço do Azure - pacote EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

7. O projeto **receptor** de atalho, clique em **Adicionar**e, em seguida, clique em **classe**. Nomeie a nova classe **SimpleEventProcessor**e, em seguida, clique em **Adicionar** para criar a classe.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Adicione as instruções a seguir na parte superior do arquivo SimpleEventProcessor.cs:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Em seguida, substitua o seguinte código para o corpo da classe:

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Essa classe será chamada pela **EventProcessorHost** processe eventos recebido de Hub do evento. Observe que o `SimpleEventProcessor` classe usa um cronômetro periodicamente chamar o método de ponto de verificação no contexto **EventProcessorHost** . Isso garante que, se o destinatário é reiniciado, ela perderá não mais de cinco minutos de processamento de trabalho.

9. Na classe **programa** , adicione o seguinte `using` instrução na parte superior do arquivo:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Em seguida, substitua o `Main` método na `Program` classe com o código a seguir, substituindo o nome do Hub de evento e a cadeia de conexão do nível de namespace salva anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] Este tutorial usa uma única instância do [EventProcessorHost][]. Para aumentar a produtividade, é recomendável que você execute várias instâncias do [EventProcessorHost][], conforme mostrado no exemplo de [escala processamento de evento][] . Nesses casos, as diversas instâncias automaticamente coordenar uns com os outros para carregar saldo os eventos recebidos. Se você quiser vários receptores para cada processo *todos* os eventos, você deve usar o conceito de **ConsumerGroup** . Ao receber eventos em máquinas diferentes, talvez seja útil especificar nomes para instâncias de [EventProcessorHost][] com base nas máquinas (ou funções de) no qual eles foram implantados. Para obter mais informações sobre esses tópicos, consulte os tópicos de [Visão geral de Hubs de evento][] e [Guia de programação de Hubs de evento][] .

<!-- Links -->
[Visão geral de Hubs de evento]: ../articles/event-hubs/event-hubs-overview.md
[Guia de programação de Hubs de evento]: ../articles/event-hubs/event-hubs-programming-guide.md
[Dimensionados processamento de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Conta de armazenamento Azure]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portal do Azure]: https://portal.azure.com