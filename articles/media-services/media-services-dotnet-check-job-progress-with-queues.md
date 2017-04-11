<properties 
    pageTitle="Usar o armazenamento de fila do Azure para monitorar as notificações de trabalho de serviços de mídia com .NET | Microsoft Azure" 
    description="Aprenda a usar o armazenamento de fila do Azure para monitorar as notificações de trabalho de serviços de mídia. O exemplo de código escrito em c# e usa o SDK dos serviços de mídia para .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016"   
    ms.author="juliako"/>

# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Usar o armazenamento de fila do Azure para monitorar as notificações de trabalho de serviços de mídia com .NET

Quando você executa trabalhos, você geralmente exige uma maneira de controlar o andamento do trabalho. Você pode verificar o progresso usando o armazenamento de fila do Azure para monitorar as notificações de trabalho de serviços de mídia (conforme descrito neste tópico) ou definir um manipulador de eventos StateChanged (conforme descrito [neste](media-services-check-job-progress.md) tópico.  

## <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications"></a>Usar o armazenamento de fila do Azure para monitorar as notificações de trabalho de serviços de mídia

Serviços de mídia do Microsoft Azure tem a capacidade de enviar mensagens de notificação para o [armazenamento do Azure fila](../storage-dotnet-how-to-use-queues.md#what-is) ao processar trabalhos de mídia. Este tópico mostra como obter essas mensagens de notificação do armazenamento de fila.

Mensagens entregues ao armazenamento de fila podem ser acessadas de qualquer lugar do mundo. Fila Azure arquitetura de mensagens é confiável e altamente escaláveis. Armazenamento de fila de sondagem é recomendável usar outros métodos. 

Um cenário comum para ouvir notificações de serviços de mídia é se você está desenvolvendo um sistema de gerenciamento de conteúdo que precisa executar algumas tarefas adicionais após um trabalho de codificação conclui (por exemplo, acionar a próxima etapa de fluxo de trabalho ou publicar conteúdo). 

###<a name="considerations"></a>Considerações

Considere o seguinte ao desenvolver aplicativos de serviços de mídia que usam fila do armazenamento do Azure.

- O serviço de filas não oferece uma garantia de primeiro-na-primeira-out (FIFO) ordenadas entrega. Para obter mais informações, consulte [filas do Azure e Azure serviço barramento filas comparada e Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Azure filas de armazenamento não é um serviço de envio; Você precisa poll fila. 
- Você pode ter qualquer número de filas. Para obter mais informações, consulte [API REST do serviço de fila](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Azure filas de armazenamento tem algumas limitações e especificações que são descritas no seguinte artigo: [filas do Azure e Azure serviço barramento filas comparada e Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###<a name="code-example"></a>Exemplo de código

O exemplo de código desta seção faz o seguinte:

1. Define a classe de **EncodingJobMessage** mapeia para o formato de mensagem de notificação. O código desserializa mensagens recebidas da fila em objetos do tipo **EncodingJobMessage** .
1. Carrega as informações de conta de armazenamento e serviços de mídia do arquivo App. Usa essas informações para criar os objetos **CloudMediaContext** e **CloudQueue** .
1. Cria a fila que recebe mensagens de notificação sobre o trabalho de codificação.
1. Cria o ponto de extremidade de notificação que é mapeado para a fila.
1. Anexa o ponto de extremidade de notificação para o trabalho e envia o trabalho de codificação. Você pode ter vários pontos de extremidade de notificação, anexados à tarefa.
1. Neste exemplo, estamos apenas interessados em estados finais de processamento de trabalho, para que podemos passar **NotificationJobState.FinalStatesOnly** para o método **AddNew** . 
        
        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Se você passar NotificationJobState.All, você deve esperar obter todas as notificações de alteração de estado: enfileirados -> agendadas -> processamento -> concluído. No entanto, conforme mencionado anteriormente, o serviço de filas de armazenamento do Azure não garante entrega ordenada. Você pode usar a propriedade de carimbo de hora (definida no tipo de EncodingJobMessage no exemplo abaixo) para mensagens de pedido. É possível que você receber mensagens de notificação duplicados. Use a propriedade de ETag (definida no tipo de EncodingJobMessage) para verificar se há duplicatas. Também é possível que algumas notificações de alteração de estado serão ignoradas. 
1. Aguarda o trabalho ir para o estado concluído marcando fila a cada 10 segundos. Exclui mensagens depois que elas tenham sido processadas.
1. Exclui a fila e o ponto de extremidade de notificação.

>[AZURE.NOTE]A maneira recomendada para monitorar o estado de uma tarefa é por ouvir a mensagem de notificação, conforme mostrado no exemplo a seguir.
>
>Como alternativa, você pode verificar estado de um trabalho usando a propriedade **IJob.State** .  Uma mensagem de notificação sobre a conclusão de uma tarefa pode chegar antes do estado em **IJob** está definido como **concluído**. A propriedade **IJob.State** reflete o estado preciso com um pequeno atraso.

    
    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;
    
    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control. 
            public String MessageVersion { get; set; }
        
            // Type of the event. Valid values are 
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }
        
            // ETag is used to help the customer detect if 
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }
        
            // Time of occurrence of the event.
            public String TimeStamp { get; set; }
    
            // Collection of values specific to the event.
    
            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
    
            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint 
            //          that triggered the notification.
            //     State- The state of the Endpoint. 
            //          Valid values are: Registered and Unregistered.
    
            public IDictionary<string, object> Properties { get; set; }
        }
    
        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;
    
            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
    
            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
    
    
                string endPointAddress = Guid.NewGuid().ToString();
    
                // Create the context. 
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
    
                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);
    
                // Create the notification point that is mapped to the queue.
                _notificationEndPoint = 
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
    
    
                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }
    
                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }
    
    
            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
    
                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
    
                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();
    
                return queue;
            }
     
    
            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
    
                //Create an encrypted asset and upload the mp4. 
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
                    inputMediaFilePath);
    
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
                // Create a task with the conversion details, using a configuration file. 
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "H264 Multiple Bitrate 720p",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
                    _notificationEndPoint);
    
                job.Submit();
    
                return job;
            }
    
            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;
    
                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;
    
                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));
    
                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
    
                            Console.WriteLine();
    
                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }
    
                            // We are only interested in messages 
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;
    
                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
    
                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}", 
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }
    
                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages, 
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);
    
                        throw new TimeoutException();
                    }
                }
            }
       
            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
                    assetCreationOptions);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);
    
                return asset;
            }
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }

O exemplo acima produziu a saída a seguir. Você valores variará.
    
    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4
    
    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35
    
    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
    State: Finished
    

## <a name="next-step"></a>Próxima etapa

Serviços de mídia de revisão caminhos de aprendizagem

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
