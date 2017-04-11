<properties
    pageTitle="Aplicativo de várias camadas de .NET | Microsoft Azure"
    description="Um tutorial .NET que ajuda você a desenvolver um aplicativo de várias camado no Azure que usa filas de barramento de serviço para se comunicar entre os níveis."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplicativo de várias camadas de .NET usando filas de barramento de serviço do Azure

## <a name="introduction"></a>Introdução

Desenvolvendo para o Microsoft Azure é fácil usando o Visual Studio e o SDK gratuita do Azure para .NET. Este tutorial orienta você pelas etapas para criar um aplicativo que usa vários recursos Azure em execução no seu ambiente local. As etapas pressupõem que você tenha sem experiência anterior usando o Azure.

Você aprenderá a seguir:

-   Como ativar o computador para desenvolvimento Azure com um único download e instalar.
-   Como usar o Visual Studio para desenvolver o Azure.
-   Como criar um aplicativo de várias camadas no Azure usando funções de trabalho e da web.
-   Como comunicar-se entre os níveis usando filas de barramento de serviço.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

Neste tutorial, você vai criar e executar o aplicativo de vários nível em um serviço de nuvem Azure. O front-end será uma função de web do ASP.NET MVC e back-end será uma função de trabalho que usa uma fila de barramento de serviço. Você pode criar o mesmo aplicativo de várias camado com o front-end como um projeto da web que é implantado em um site do Azure em vez de um serviço na nuvem. Para obter instruções sobre o que fazer de forma diferente em um front-end do site Azure, consulte a seção [próximas etapas](#nextsteps) . Você também pode experimentar o tutorial de [aplicativo do .NET no local/nuvem híbrida](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

A captura de tela a seguir mostra o aplicativo concluído.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Visão geral do cenário: comunicação entre função

Para enviar um pedido para processamento, o componente de UI front-end, executando na função da web, deve interagir com a lógica de camada intermediária em execução na função de trabalho. Este exemplo usa barramento de serviço orientado para a comunicação entre as camadas de mensagens.

Usando mensagens de comunicação entre as camadas intermediária e web separa os dois componentes. Em contraste com direta de mensagens (ou seja, TCP ou HTTP), a camada da web não conectar-se para a camada intermediária diretamente; em vez disso, ele envia unidades de trabalho, como mensagens, em barramento de serviço, que retém confiavelmente até que a camada intermediária está pronta para consumir e processá-los.

Barramento de serviço fornece duas entidades para dar suporte ao sistema de mensagens intermediário: tópicos e filas. Com filas, cada mensagem enviada para a fila é consumida por um único destinatário. Tópicos de suportam o padrão de publicação/assinatura em que cada mensagem publicada é disponibilizada para uma assinatura registrada com o tópico. Cada assinatura logicamente mantém sua própria fila de mensagens. Assinaturas também podem ser configuradas com regras de filtro que restringem o conjunto de mensagens passadas à fila de assinatura para aqueles que correspondem ao filtro. O exemplo a seguir usa filas de barramento de serviço.

![][1]

Esse mecanismo de comunicação tem várias vantagens em relação direta de mensagens:

-   **Separação temporal.** Com o padrão de mensagens assíncrono, produtores e consumidores não precisam ser onlinehttps ao mesmo tempo. Barramento de serviço confiavelmente armazena mensagens até que o participante de consumo está pronto para recebê-los. Isso permite que os componentes do aplicativo distribuído seja desconectado, seja volutariamente, por exemplo, para manutenção ou devido a uma falha de componente, sem afetar o sistema como um todo. Além disso, o aplicativo de consumo talvez só precise ficar online durante determinadas horas do dia.

-   **Redistribuição de carga.** Em muitos aplicativos, a carga do sistema varia ao longo do tempo, enquanto o tempo de processamento necessário para cada unidade de trabalho é geralmente constante. Intermediating produtores de mensagem e consumidores com uma fila significa que o aplicativo de consumo (trabalhador) só precisa ser configurado para acomodar a carga média em vez de carga de pico. A profundidade da fila cresce e como a carga de entrada varia de contratos. Isso diretamente economiza dinheiro em termos a quantidade de infraestrutura necessária para a carga de aplicativo de serviço.

-   **Balanceamento de carga.** Como carregar aumenta, mais processos de trabalho podem ser adicionados a leitura da fila. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, este balanceamento de carga baseado em extração permite que uso ideal das máquinas trabalhador mesmo se as máquinas trabalhador diferem em termos de capacidade de processamento, como eles busca mensagens em seu próprio ritmo máximo. Esse padrão geralmente é chamado de padrão *que competem consumidor* .

    ![][2]

As seções a seguir discutem o código que implementa essa arquitetura.

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar a desenvolver aplicativos do Azure, obtenha as ferramentas e configure seu ambiente de desenvolvimento.

1.  Instale o SDK do Azure para .NET em [obter ferramentas e o SDK][].

2.  Clique em **instalar o SDK** para a versão do Visual Studio que você está usando. As etapas deste tutorial usam Visual Studio 2015.

4.  Quando solicitado a executar ou salvar o instalador, clique em **Executar**.

5.  No **Web Platform Installer**, clique em **instalar** e prossiga com a instalação.

6.  Quando a instalação estiver concluída, você terá tudo o que é necessário para começar a desenvolver o aplicativo. O SDK inclui ferramentas que permitem que você facilmente desenvolver aplicativos do Azure no Visual Studio. Se você não tiver o Visual Studio instalado, o SDK também instala o livre Visual Studio Express.

## <a name="create-a-namespace"></a>Criar um namespace

A próxima etapa é criar um namespace de serviço e obter uma chave de assinatura de acesso compartilhado (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto barramento de serviço. Uma chave SAS é gerada pelo sistema quando um namespace é criado. A combinação de namespace e chave SAS fornece as credenciais para o barramento de serviço autenticar o acesso a um aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Criar uma função de web

Nesta seção, você cria o front-end do seu aplicativo. Primeiro, crie as páginas que seu aplicativo exibe.
Depois disso, adicione código que envia itens a uma fila de barramento de serviço e exibe informações de status sobre a fila.

### <a name="create-the-project"></a>Criar o projeto

1.  Usando privilégios de administrador, inicie o Microsoft Visual Studio. Para iniciar o Visual Studio com privilégios de administrador, clique com botão direito no ícone do programa **Visual Studio** e clique em **Executar como administrador**. O emulador de computação Azure, discutido posteriormente neste artigo, exige que o Visual Studio seja iniciado com privilégios de administrador.

    No Visual Studio, no menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.

2.  De **Modelos instalados**, em **Visual c#**, clique em **nuvem** e clique em **Serviço de nuvem do Azure**. Nome do projeto **MultiTierApp**. Clique em **Okey**.

    ![][9]

3.  Do **.NET Framework 4,5** funções, clique duas vezes em **Função da Web do ASP.NET**.

    ![][10]

4.  Passe o mouse sobre **WebRole1** em **solução de serviço de nuvem do Azure**, clique no ícone de lápis e renomeie a função web para **FrontendWebRole**. Clique em **Okey**. (Verifique se que você inserir "Frontend" com uma letra minúscula 'e,' não "FrontEnd".)

    ![][11]

5.  Na caixa de diálogo **Novo projeto ASP.NET** , na lista **Selecionar um modelo** , clique em **MVC**.

    ![][12]

6. Ainda na caixa de diálogo **Novo projeto ASP.NET** , clique no botão de **Autenticação de alteração** . Na caixa de diálogo **Alterar autenticação** , clique em **Sem autenticação**e clique em **Okey**. Para este tutorial, você está implantando um aplicativo que não precisa de um logon do usuário.

    ![][16]

7. Novamente na caixa de diálogo **Novo projeto ASP.NET** , clique em **Okey** para criar o projeto.

6.  No **Solution Explorer**, no projeto **FrontendWebRole** , **referências**de atalho, clique em **Gerenciar pacotes do NuGet**.

7.  Clique na guia **Procurar** , procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de uso.

    ![][13]

    Observe que os conjuntos de cliente necessário agora referenciados e alguns novos arquivos de código foram adicionados.

9.  No **Solution Explorer**, clique com botão direito **modelos** , clique em **Adicionar**e clique em **classe**. Na caixa **nome** , digite o nome **OnlineOrder.cs**. Clique em **Adicionar**.

### <a name="write-the-code-for-your-web-role"></a>Escreva o código para sua função web

Nesta seção, você criar várias páginas que exibe seu aplicativo.

1.  No arquivo OnlineOrder.cs no Visual Studio, substitua a definição de namespace existente com o seguinte código:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  No **Solution Explorer**, clique duas vezes em **Controllers\HomeController.cs**. Adicione as seguintes instruções de **uso** na parte superior do arquivo para incluir os namespaces para o modelo que você acabou de criar, bem como barramento de serviço.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Também no arquivo HomeController.cs no Visual Studio, substitua a definição de namespace existente com o seguinte código. Este código contém métodos para lidar com o envio de itens na fila.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  No menu **Build** , clique em **Criar solução** para testar a precisão do seu trabalho até o momento.

5.  Agora, crie o modo de exibição para o `Submit()` método que você criou anteriormente. Clique com botão direito dentro do `Submit()` método (a sobrecarga de `Submit()` que não leva parâmetros) e escolha **Adicionar modo de exibição**.

    ![][14]

6.  Aparece uma caixa de diálogo para criar o modo de exibição. Na lista **modelo** , escolha **criar**. Na lista de **classe do modelo** , clique na classe **OnlineOrder** .

    ![][15]

7.  Clique em **Adicionar**.

8.  Agora, altere o nome exibido do seu aplicativo. No **Solution Explorer**, clique duas vezes na **Views\Shared\\cshtml** arquivo para abri-lo no editor do Visual Studio.

9.  Substitua todas as ocorrências do **Meu aplicativo do ASP.NET** com **os produtos do LITWARE**.

10. Remova os links de **página inicial**, **sobre**e **contato** . Exclua o código realçado:

    ![][28]

11. Por fim, modifique a página de envio para incluir algumas informações sobre a fila. No **Solution Explorer**, clique duas vezes no arquivo **Views\Home\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a seguinte linha após `<h2>Submit</h2>`. Por agora, o `ViewBag.MessageCount` está vazia. Você preenchê-la mais tarde.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Agora você tiver implementado a interface do usuário. Você pode pressionar **F5** para executar o aplicativo e confirmar que ele ficará conforme esperado.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Escreva o código para envio de itens a uma fila de barramento de serviço

Agora, adicione código para envio de itens a uma fila. Primeiro, crie uma classe que contém as informações de conexão de fila do barramento de serviço. Em seguida, inicialize sua conexão de Global.aspx.cs. Finalmente, atualize o código de envio que você criou anteriormente em HomeController.cs para realmente enviar itens a uma fila de barramento de serviço.

1.  No **Solution Explorer**, clique com botão direito **FrontendWebRole** (com o botão direito no projeto, não a função). Clique em **Adicionar**e, em seguida, clique em **classe**.

2.  Nome da classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.

3.  Agora, adicione o código que encapsula as informações de conexão e inicializa a conexão para uma fila de barramento de serviço. Substituir todo o conteúdo de QueueConnector.cs com o seguinte código e insira valores para `your Service Bus namespace` (seu nome de namespace) e `yourKey`, que é a **chave primária** obtido anteriormente do portal do Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Agora, certifique-se de que seu método **inicializar** obtém chamado. No **Solution Explorer**, clique duas vezes em **Global.asax\Global.asax.cs**.

5.  Adicione a seguinte linha de código no final do método **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Finalmente, atualize o código de web que você criou anteriormente, para enviar itens na fila. No **Solution Explorer**, clique duas vezes em **Controllers\HomeController.cs**.

7.  Atualizar o `Submit()` método (a sobrecarga sem parâmetros) da seguinte maneira para obter a contagem de mensagem para a fila.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Atualizar o `Submit(OnlineOrder order)` método (a sobrecarga que utiliza um parâmetro) da seguinte maneira para enviar informações de ordem à fila.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Agora você pode executar o aplicativo novamente. Cada vez que você enviar um pedido, a contagem de mensagens aumenta.

    ![][18]

## <a name="create-the-worker-role"></a>Criar a função de trabalho

Agora, você irá criar a função de trabalho que processa o envio de pedidos. Este exemplo usa o modelo de projeto do Visual Studio de **Função de trabalho com fila de barramento de serviço** . Você já obtida as credenciais necessárias a partir do portal.

1. Verifique se que você conectou Visual Studio à sua conta do Azure.

2.  No Visual Studio, no **Solution Explorer** , clique na pasta de **funções** sob o projeto **MultiTierApp** .

3.  Clique em **Adicionar**e, em seguida, clique em **Novo projeto de função de trabalho**. Caixa de diálogo **Adicionar novo projeto de função** é exibida.

    ![][26]

4.  Na caixa de diálogo **Adicionar novo projeto de função** , clique em **Função de trabalho com fila de barramento de serviço**.

    ![][23]

5.  Na caixa **nome** , nomeie o projeto **OrderProcessingRole**. Clique em **Adicionar**.

6.  Copie a cadeia de conexão que você obteve na etapa 9 da seção "Criar um namespace de barramento de serviço" na área de transferência.

7.  No **Solution Explorer**, clique com botão direito do **OrderProcessingRole** que você criou na etapa 5 (certifique-se de que você **OrderProcessingRole** em **funções**e não a classe de atalho). Clique em **Propriedades**.

8.  Na guia **configurações** da caixa de diálogo **Propriedades** , clique dentro da caixa de **valor** para **Microsoft.ServiceBus.ConnectionString**e, em seguida, cole o valor de ponto de extremidade que você copiou na etapa 6.

    ![][25]

9.  Crie uma classe **OnlineOrder** para representar os pedidos como processo-los da fila. Você pode reutilizar uma classe que você já criou. No **Solution Explorer**, clique com botão direito da classe **OrderProcessingRole** (com o botão direito no ícone de classe, não a função). Clique em **Adicionar**, clique em **Item existente**.

10. Navegue até a subpasta para **FrontendWebRole\Models**e clique duas vezes em **OnlineOrder.cs** para adicioná-lo deste projeto.

11. No **WorkerRole.cs**, altere o valor da variável **NomeDaFila** `"ProcessingQueue"` para `"OrdersQueue"` conforme mostrado no código a seguir.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Adicione o seguinte usando instrução na parte superior do arquivo WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. No `Run()` funcionar, dentro a `OnMessage()` chamar, substitua o conteúdo da `try` cláusula com o código a seguir.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Você concluiu o aplicativo. Você pode testar o aplicativo completo clicando no projeto MultiTierApp na Solution Explorer, selecionando **Definir como projeto de inicialização**e, em seguida, pressionar F5. Observe que a contagem de mensagens não é incrementado, porque a função de trabalho processa itens da fila e os marca como concluído. Você pode ver a saída de rastreamento de sua função trabalhador visualizando a IU emulador de computação do Azure. Você pode fazer isso clicando no ícone de emulador na área de notificação da barra de tarefas e selecionando **Mostrar calcular emulador interface do usuário**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Próximas etapas  

Para saber mais sobre barramento de serviço, consulte os seguintes recursos:  

* [Barramento de serviço Azure][sbmsdn]  
* [Página de serviço de barramento de serviço][sbwacom]  
* [Como usar filas de barramento de serviço][sbwacomqhowto]  

Para saber mais sobre cenários de vários níveis, consulte:  

* [Aplicativo de várias camadas de .NET usando Blobs, filas e tabelas de armazenamento][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtenha ferramentas e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  