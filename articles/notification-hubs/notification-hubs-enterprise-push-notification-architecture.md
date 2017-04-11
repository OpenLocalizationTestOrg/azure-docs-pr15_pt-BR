<properties
    pageTitle="Notificação Hubs - arquitetura de envio da empresa"
    description="Orientação sobre como usar Hubs de notificação do Azure em um ambiente corporativo"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Diretrizes de arquitetura de envio do Enterprise

Empresas hoje são gradualmente movendo em direção a criação aplicativos móveis para qualquer um de seus usuários finais (externo) ou para os funcionários (internos). Eles têm sistemas de back-end existentes no lugar seja mainframes ou alguns aplicativos LoB que devem ser integrados a arquitetura do aplicativo móvel. Este guia falar sobre a melhor maneira de fazer essa integração recomendar solução possível para cenários comuns.

Um requisito frequente é para enviar notificação de envio para os usuários por meio de seus aplicativos móveis quando ocorre um evento de interesse nos sistemas de back-end. Por exemplo um cliente bancário que tenha o aplicativo de bancário do banco em seu iPhone deseja ser notificado quando um cartão de débito é feita acima de um determinado período de sua conta ou um cenário de intranet onde um funcionário do departamento de finanças que possui um aplicativo de aprovação de orçamento no seu Windows Phone deseja ser notificado quando ele recebe uma solicitação de aprovação.

A conta bancária ou processamento de aprovação é provável ser realizadas em algum sistema de back-end que deve iniciar um envio para o usuário. Pode haver vários desses sistemas de back-end que todos devem criar o mesmo tipo de lógica para implementar o envio quando um evento dispara uma notificação. A complexidade aqui está na integração de vários sistemas de back-end junto com um sistema de envio único onde os usuários finais podem se inscrever em diferentes notificações e pode até mesmo haver vários aplicativos móveis, por exemplo, no caso de aplicativos móveis intranet onde um aplicativo móvel talvez queira receber notificações de vários desses sistemas de back-end. Os sistemas de back-end não souber ou precisa saber de envio semântica/tecnologia para que uma solução comum aqui foi normalmente apresentar um componente que controla os sistemas de back-end para todos os eventos de interesse e é responsável por enviar as mensagens de envio para o cliente.
Aqui, falaremos sobre uma solução ainda melhor usando o barramento de serviço do Azure - modelo de tópico/assinatura que reduza a complexidade enquanto faz a solução scalable.

Aqui está a arquitetura geral da solução (generalizado com vários aplicativos móveis mas igualmente aplicáveis quando há apenas um aplicativo móvel)

## <a name="architecture"></a>Arquitetura

![][1]

A peça chave neste diagrama arquitetônico é barramento de serviço do Azure que fornece um tópicos/assinaturas (mais na [programação do serviço de barramento Pub/Sub]) do modelo de programação. O receptor, o que é nesse caso, o móvel back-end (normalmente [Serviço móvel do Azure], que vai iniciar um envio para os aplicativos móveis) não recebe mensagens diretamente de sistemas de back-end, mas em vez disso, temos uma camada de abstração intermediária fornecida pelo [Barramento de serviço do Azure] que habilita móvel back-end receber mensagens de um ou mais sistemas de back-end. Um tópico de barramento de serviço precisa ser criada para cada um dos sistemas de back-end por exemplo, conta, RH, financeiro que são basicamente "tópicos" de interesse que iniciará mensagens sejam enviadas como notificação de envio. Os sistemas de back-end enviará mensagens para esses tópicos. Um back-end Mobile pode se inscrever em um ou mais tópicos pela criação de uma assinatura de barramento de serviço. Isto irá dão direito móvel back-end para receber uma notificação do sistema back-end correspondente. Back-end móvel continua escutar mensagens em suas assinaturas e assim que uma mensagem chega, ela ativa novamente e envia como notificação para o seu hub de notificação. Hubs de notificação então entrega eventualmente a mensagem para o aplicativo móvel. Para resumir os componentes importantes, temos:

1. Sistemas de back-end (sistemas LoB/herdado)
    - Cria o tópico de barramento de serviço
    - Envia a mensagem
2. Móvel back-end
    - Cria a inscrição no serviço
    - Recebe uma mensagem (do sistema de back-end)
    - Envia uma notificação para clientes (via Hub de notificação do Azure)
3. Aplicativo móvel
    - Recebe e exibir notificação

###<a name="benefits"></a>Benefícios:

1. A separação entre o receptor (serviço móvel do aplicativo/via Hub de notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicionais sendo integrados com alteração mínima.
2. Isso também torna o cenário de vários aplicativos móveis ser capaz de receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo:

###<a name="prerequisites"></a>Pré-requisitos
Você deve concluir os seguintes tutoriais se familiarizar com os conceitos bem como criação comuns e as etapas de configuração:

1. [Programação de serviço barramento Pub/Subisso] explica detalhes de trabalhar com o serviço barramento tópicos/assinaturas, como criar um namespace para conter tópicos/assinaturas, como enviar e receber mensagens delas.
2. [Notificação Hubs - tutorial Universal do Windows] - isso explica como configurar um aplicativo da Windows Store e usar Hubs de notificação para registrar e, em seguida, receber notificações.

###<a name="sample-code"></a>Código de exemplo

O código de exemplo completo está disponível em [Amostras de Hub de notificação]. Ele é dividido em três componentes:

1. **EnterprisePushBackendSystem**

    a. Este projeto usa o pacote de Nuget *WindowsAzure.ServiceBus* e baseado em [programação de serviço barramento Pub/Sub].

    b. Este é um simples c# console aplicativo para simular um sistema de LoB que inicia a mensagem ser entregue para o aplicativo móvel.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`é usado para criar o tópico de barramento de serviço onde enviaremos mensagens.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`é usado para enviar as mensagens para este tópico de barramento de serviço. Aqui nós simplesmente está enviando um conjunto de mensagens aleatórios para o tópico periodicamente com o objetivo de amostra. Normalmente haverá um sistema de back-end que enviará mensagens quando ocorre um evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    a. Este projeto usa os pacotes *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* Nuget e baseia-se na [programação do serviço de barramento Pub/Sub].

    b. Este é outro c# console app que podemos será executado como um [WebJob do Azure] , pois ele precisa executar continuamente para escutar mensagens dos sistemas LoB/back-end. Isso fará parte do seu celular back-end.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`é usado para criar uma assinatura de barramento de serviço para o tópico onde o sistema de back-end enviará mensagens. Dependendo do cenário de negócios, esse componente criará uma ou mais assinaturas para tópicos correspondentes (por exemplo, alguns podem estar recebendo mensagens do sistema de RH, alguns do sistema de finanças e assim por diante)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification é usado para ler a mensagem o tópico usar sua assinatura e se a leitura for bem-sucedida elaborar uma notificação (no cenário de amostra uma notificação do sistema nativo Windows) para ser enviadas para o aplicativo móvel usando Hubs de notificação do Azure.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Para publicar isso como um **WebJob**, clique com o botão direito na solução no Visual Studio e selecione **Publicar como WebJob**

    ![][2]

    f. Selecione o perfil de publicação e criar um novo site do Azure se ele não existir já que hospedará este WebJob e uma vez que o site e **Publicar**.

    ![][3]

    g. Configure o trabalho para ser "Executar continuamente" para que quando fizer logon no [Portal de clássico do Azure] você verá algo semelhante ao seguinte:

    ![][4]


3. **EnterprisePushMobileApp**

    a. Este é um aplicativo da Windows Store que receber notificações do sistema da WebJob executando como parte do seu celular back-end e exibi-la. Isso se baseia na [Notificação Hubs - tutorial Universal do Windows].  

    b. Certifique-se de que seu aplicativo está ativado para receber notificações do sistema.

    c. Certifique-se de que os seguintes Hubs de notificação de código de registro esteja sendo chamado no aplicativo de inicialização (após substituir a *HubName* e *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemplo de execução:

1. Verifique se seu WebJob está sendo executado com êxito e agendado para "Executar continuamente".
2. Execute o **EnterprisePushMobileApp** que iniciará o aplicativo da Windows Store.
3. Execute o aplicativo de console de **EnterprisePushBackendSystem** que será simular LoB back-end e começarão a enviar mensagens e você verá as notificações do sistema que aparecem como a seguinte:

    ![][5]

4. As mensagens foram enviadas originalmente para os tópicos de barramento de serviço que estava sendo monitorado por assinaturas de barramento de serviço no seu trabalho na Web. Depois que uma mensagem foi recebida, uma notificação foi criada e enviada para o aplicativo móvel. É possível examinar os logs de WebJob para confirmar o processamento quando você vai para o link de Logs no [Portal de clássico do Azure] para o seu trabalho na Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos de Hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço móvel do Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Barramento de serviço Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação de serviço barramento Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notificação Hubs - tutorial Universal do Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal clássico]: https://manage.windowsazure.com/
