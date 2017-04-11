## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com EventProcessorHost em Java

EventProcessorHost é uma classe Java que simplifica receba eventos de Hubs de evento pelo gerenciamento de pontos de verificação persistentes e paralelo recebe desses Hubs de evento. Usando EventProcessorHost pode dividir eventos entre vários receptores, mesmo quando hospedado em nós diferentes. Este exemplo mostra como usar EventProcessorHost para um único destinatário.

###<a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para poder usar EventProcessorHost, você deve ter uma [conta de armazenamento do Azure][]:

1. Faça logon no [portal do Azure clássico][]e clique em **novo** na parte inferior da tela.

2. Clique em **Serviços de dados**, e em seguida, **armazenamento**, **Criar rápida**e digite um nome para a sua conta de armazenamento. Selecione sua região desejada e, em seguida, clique em **Criar conta de armazenamento**.

    ![][11]

3. Clique na conta de armazenamento recém-criado e clique em **Gerenciar teclas de acesso**:

    ![][12]

    Copie a chave primária de acesso para usar mais tarde neste tutorial.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Criar um projeto de Java usando o Host de EventProcessor

A biblioteca de cliente Java para Hubs de evento está disponível para uso em projetos Maven do [Repositório Central Maven][Maven Package]e podem ser referenciados usando a seguinte declaração de dependência dentro de seu arquivo de projeto Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Explicitamente para diferentes tipos de ambientes de compilação, você pode obter os arquivos JAR mais recentes disponibilizados do [Repositório Central Maven] [ Maven Package] ou do [ponto de distribuição de lançamento no GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Para o exemplo a seguir, primeiro crie um novo projeto de Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. A classe será chamada ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Use o código a seguir para criar uma nova classe chamada ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Criar uma classe final chamado ```EventProcessorSample```, usando o seguinte código.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Substitua os campos a seguir com os valores usados quando você criou o Hub de evento e a conta de armazenamento.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] Este tutorial usa uma única instância do EventProcessorHost. Para aumentar a produtividade, é recomendável que você execute várias instâncias do EventProcessorHost. Nesses casos, as diversas instâncias automaticamente coordenar uns com os outros para balanceamento de carga os eventos recebidos. Se você quiser vários receptores para cada processo *todos* os eventos, você deve usar o conceito de **ConsumerGroup** . Ao receber eventos em máquinas diferentes, talvez seja útil especificar nomes para instâncias de EventProcessorHost com base nas máquinas (ou funções de) no qual eles foram implantados.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Conta de armazenamento Azure]: ../articles/storage/storage-create-storage-account.md
[Azure portal clássico]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

