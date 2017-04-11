<properties 
    pageTitle="Barramento de serviço orientado mensagens tutorial .NET | Microsoft Azure"
    description="Comunicação tutorial .NET mensagens."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Tutorial do barramento de serviço orientado mensagens .NET

Barramento de serviço do Azure fornece duas soluções de mensagens abrangentes – uma, através de um serviço centralizado "retransmissão" em execução na nuvem que ofereça suporte a uma variedade de protocolos de transporte diferentes e Web services padrões, incluindo SOAP, WS-* e restante. O cliente não precisa de uma conexão direta para o serviço local nem ele precisa saber onde o serviço reside e o serviço de local não precisa de qualquer portas de entrada abertas no firewall.

A segunda solução mensagens permite que os recursos de mensagens "intermediário". Eles podem ser considerados como assíncronas ou desassociado recursos de mensagens que assina publicar de suporte, separação temporal e cenários usando a infraestrutura de mensagens do barramento de serviço de balanceamento de carga. Comunicação desacoplada tem muitas vantagens; Por exemplo, clientes e servidores podem conectar conforme necessário e executar as operações de maneira assíncrona.

Este tutorial destina-se para fornecer uma visão geral e experiência prática com filas, um dos componentes principais do barramento de serviço orientado a mensagens. Depois de você trabalhar com a sequência de tópicos neste tutorial, você terá um aplicativo que preenche uma lista de mensagens, cria uma fila e envia mensagens para essa fila. Finalmente, o aplicativo recebe exibe as mensagens da fila e limpará seus recursos e sai. Para um tutorial correspondente que descreve como criar um aplicativo que usa a retransmissão de barramento de serviço, consulte o [barramento de serviço enviadas mensagens tutorial](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introdução e os pré-requisitos

Filas oferecem primeiro em, entrega de mensagens de PEPS (primeiro) para um ou mais consumidores concorrência. FILA significa que mensagens normalmente devem ser recebidos e processados pelos receptores na ordem temporal na qual eles foram enfileirados, e cada mensagem será recebida e processada pelo consumidor de apenas uma mensagem. Dos principais benefícios em usando filas é atingir *temporal separação* de componentes de aplicativo: em outras palavras, o produtores e consumidores não precisa enviar e receber mensagens ao mesmo tempo, desde que as mensagens são armazenadas permanentemente na fila. Uma vantagem relacionada é *redistribuição de carga*, que permite produtores e consumidores de enviar e receber mensagens com diferentes taxas.

A seguir estão algumas etapas administrativas e de pré-requisito, que você deverá seguir antes de iniciar o tutorial. A primeira é para criar um namespace de serviço e para obter uma chave de assinatura (SAS) acesso compartilhado. Um namespace fornece um limite de aplicativo para cada aplicativo exposto barramento de serviço. Uma chave SAS é gerada automaticamente pelo sistema quando um namespace de serviço é criado. A combinação do namespace do serviço e a chave SAS fornece uma credencial com a qual barramento de serviço autentica o acesso a um aplicativo.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Criar um namespace de serviço e obter uma chave SAS

A primeira etapa é criar um namespace de serviço e para obter uma chave de [Assinatura de acesso compartilhado](service-bus-sas-overview.md) (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto barramento de serviço. Uma chave SAS é gerada automaticamente pelo sistema quando um namespace de serviço é criado. A combinação do namespace do serviço e a chave SAS fornece uma credencial para barramento de serviço autenticar o acesso a um aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

A próxima etapa é criar um projeto do Visual Studio e escrever duas funções de auxiliar que carregar uma lista delimitada por vírgulas de mensagens em um objeto de .NET [lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) digitada.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Abra o Visual Studio como administrador clicando o programa no menu Iniciar e clicando em **Executar como administrador**.

1. Crie um novo projeto de aplicativo de console. Clique no menu **arquivo** , selecione **novo**e clique em **projeto**. Na caixa de diálogo **Novo projeto** , clique em **Visual c#** (se o **Visual c#** não aparecer, procure em **Outros idiomas**), clique no modelo de **Aplicativo de Console** e chame- **QueueSample**. Use o **local**padrão. Clique em **Okey** para criar o projeto.

1. Use o Gerenciador de pacote do NuGet para adicionar as bibliotecas de barramento de serviço ao seu projeto:
    1. No Solution Explorer, clique com botão direito no projeto **QueueSample** e clique em **Gerenciar pacotes do NuGet**.
    2. Na caixa de diálogo **Gerenciar pacotes Nuget** , clique na guia **Procurar** , procure **Barramento de serviço do Azure**e clique em **instalar**.
<br />
1. No Solution Explorer, clique duas vezes no arquivo Program.cs para abri-lo no editor do Visual Studio. Alterar o nome do namespace de seu nome padrão de `QueueSample` para `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modificar o `using` instruções como mostrado no código a seguir.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Crie um arquivo de texto chamado Data.csv e copiar no seguinte texto delimitado por vírgulas.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Salve e feche o arquivo Data.csv e lembrar o local em que ele foi salvo.

1. No Solution Explorer, clique com botão direito no nome do seu projeto (neste exemplo, **QueueSample**), clique em **Adicionar**, clique em **Item existente**.

1. Navegue até o arquivo de Data.csv que você criou na etapa 6. Clique em arquivo, clique em **Adicionar**. Certifique-se de que * *todos os arquivos (*.*) * * é selecionado na lista de tipos de arquivo.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Criar um método que analisa uma lista de mensagens

1. No `Program` classe antes do `Main()` método, declare duas variáveis: um tipo de **tabela de dados**, para conter a lista de mensagens em Data.csv. Outro deve ser do tipo lista de objetos, digitado altamente para [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). O segundo é a lista de mensagens de comunicação que etapas subsequentes no tutorial usarão.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Fora `Main()`, defina uma `ParseCSV()` método que analisa a lista de mensagens de Data.csv e carrega as mensagens em uma tabela de [tabela de dados](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) , como mostrado aqui. O método retorna um objeto de **tabela de dados** .

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. No `Main()` método, adicione uma instrução que chama o `ParseCSVFile()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Criar um método que carrega a lista de mensagens

1. Fora `Main()`, defina uma `GenerateMessages()` método que usa o objeto de **tabela de dados** retornado por `ParseCSVFile()` e carrega a tabela em uma lista digitada das mensagens de comunicação. O método retorna o objeto de **lista** , como no exemplo a seguir. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. Em `Main()`, logo após a chamada para `ParseCSVFile()`, adicione uma instrução que chama o `GenerateMessages()` método com o valor de retorno `ParseCSVFile()` como um argumento:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obter credenciais de usuário

1. Primeiro, crie três variáveis de cadeia de caracteres global para armazenar esses valores. Declarar essas variáveis logo após as anterior declarações de variáveis; Por exemplo:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Em seguida, crie uma função que aceita e armazena o namespace do serviço e a chave SAS. Adicionar este método fora `Main()`. Por exemplo: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. Em `Main()`, logo após a chamada para `GenerateMessages()`, adicione uma instrução que chama o `CollectUserInput()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Criar a solução

No menu **Build** no Visual Studio, você pode clicar **Build Solution** ou pressione **Ctrl + Shift + B** para confirmar a precisão do seu trabalho até o momento.

## <a name="create-management-credentials"></a>Criar credenciais de gerenciamento

Nesta etapa, você define as operações de gerenciamento que você usará para criar acesso compartilhado com a qual seu aplicativo será autorizado de credenciais de assinatura (SAS).

1. Para maior clareza, este tutorial coloca todas as operações de fila em um método separado. Criar um assíncrono `Queue()` método na `Program` classe, após o `Main()` método. Por exemplo:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. A próxima etapa é criar uma credencial SAS usando um objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . O método de criação utiliza o nome de chave de SAS e valor obtido no `CollectUserInput()` método. Adicione o seguinte código para o `Queue()` método:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Crie um novo objeto de gerenciamento de namespace, com um URI que contém o nome do namespace e as credenciais de gerenciamento obtidas na etapa anterior, como argumentos. Adicione este código logo após o código adicionado na etapa anterior. Certifique-se de substituir `<yourNamespace>` com o nome do seu namespace de serviço:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Exemplo

Neste ponto, seu código deve ser semelhante ao seguinte:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Enviar mensagens à fila

Nesta etapa, você cria uma fila e envia as mensagens contidas na lista de mensagens de comunicação para essa fila.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Criar fila e enviar mensagens à fila

1. Primeiro, crie a fila. Por exemplo, chame- `myQueue`e declare logo após as operações de gerenciamento que você adicionou na `Queue()` método na última etapa:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. No `Queue()` método, crie um objeto de fábrica mensagens com um barramento de serviço recém-criado URI como um argumento. Adicione o seguinte código diretamente após as operações de gerenciamento que você adicionou na última etapa. Certifique-se de substituir `<yourNamespace>` com o nome do seu namespace de serviço:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Em seguida, crie o objeto de fila usando a classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Adicione o seguinte código diretamente após o código que você adicionou na última etapa:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Em seguida, adicione o código que percorre a lista de mensagens de comunicação que você criou anteriormente, enviando cada à fila. Adicione o seguinte código diretamente após a `CreateQueueClient()` instrução na etapa anterior:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Receber mensagens de fila

Nesta etapa, você pode obter a lista de mensagens da fila que você criou na etapa anterior.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Criar um receptor e receber mensagens de fila

No `Queue()` método, percorrer a fila e receber as mensagens usando o método [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , impressão de cada mensagem no console. Adicione o seguinte código diretamente após o código que você adicionou na etapa anterior:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Observe que o `Thread.Sleep` só é usado para simular a mensagem processamento e você provavelmente não seria necessário em um aplicativo real mensagens.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Encerrar o método de fila e limpar recursos

Logo após o código anterior, adicione o seguinte código para limpar os recursos de fábrica e fila de mensagem:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Chamar o método de fila

A última etapa é adicionar uma instrução que chama o `Queue()` método de `Main()`. Adicione a seguinte linha de código realçada no final do Main ():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Exemplo

O código a seguir contém o aplicativo **QueueSample** completo.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Criar e executar o aplicativo de QueueSample

Agora que você concluiu as etapas anteriores, você pode criar e executar o aplicativo **QueueSample** .

### <a name="build-the-queuesample-application"></a>Criar o aplicativo de QueueSample

No Visual Studio, no menu **Build** , clique em **Criar solução**ou pressione **Ctrl + Shift + B**. Se você encontrar erros, verifique se seu código está correto com base no exemplo completo apresentado no final da etapa anterior.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrava como criar um cliente de barramento de serviço de aplicativo e serviço usando os recursos de mensagens de barramento de serviço orientado. Para um tutorial semelhante que usa barramento de serviço de [retransmissão](service-bus-messaging-overview.md#Relayed-messaging), consulte o [barramento de serviço enviadas mensagens tutorial](../service-bus-relay/service-bus-relay-tutorial.md).

Para saber mais sobre o [Barramento de serviço](https://azure.microsoft.com/services/service-bus/), consulte os tópicos a seguir.

- [Visão geral de mensagens do barramento de serviço](service-bus-messaging-overview.md)
- [Fundamentos de barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura de barramento de serviço](service-bus-architecture.md)

