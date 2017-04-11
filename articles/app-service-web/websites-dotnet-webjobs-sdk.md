<properties 
    pageTitle="O que é o SDK do Azure WebJobs" 
    description="Uma introdução ao SDK do WebJobs do Azure. Explica o que é o SDK, cenários típicos é útil para e amostras de código." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>O que é o SDK do Azure WebJobs

## <a id="overview"></a>Visão geral

Este artigo explica o que é o SDK WebJobs, examina alguns cenários comuns que ele é útil para e fornece uma visão geral de como usá-lo em seu código.

[WebJobs](websites-webjobs-resources.md) é um recurso de serviço de aplicativo do Azure que permite executar um programa ou script no mesmo contexto de um aplicativo web, API aplicativo ou aplicativo móvel. A finalidade do [WebJobs SDK](websites-webjobs-resources.md) é simplificar o código que você escreve para tarefas comuns que um WebJob pode realizar, como o processamento de imagens, fila de processamento, agregação RSS, manutenção de arquivo e enviar emails. O SDK do WebJobs possui recursos internos para trabalhar com o armazenamento do Azure e barramento de serviço, para agendamento de tarefas e tratamento de erros e para muitos outros cenários comuns. Além disso, ele foi projetado para ser extensível. O [SDK do WebJobs é abrir origem](https://github.com/Azure/azure-webjobs-sdk/), e há um [repositório de origem para extensões de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

O SDK do WebJobs inclui os seguintes componentes:

* **Pacotes do NuGet**. Pacotes do NuGet que você adicionar a um projeto de aplicativo de Console do Visual Studio fornecem uma estrutura que seu código usa por decorar seus métodos com atributos de WebJobs SDK.
  
* **Painel de controle**. Parte do WebJobs SDK está incluído no serviço de aplicativo do Azure e fornece monitoramento sofisticados e diagnósticos para programas que usam os pacotes NuGet. Você não precisa escrever código para usar os recursos de monitoramento e diagnóstico.

## <a id="scenarios"></a>Cenários

Aqui estão alguns cenários típicos que você pode manipular mais facilmente com o SDK do Azure WebJobs:

* Imagem do trabalho de CPU intenso processamento ou outro. Um recurso comum de sites é a capacidade de carregar imagens ou vídeos. Muitas vezes você deseja manipular o conteúdo depois carregá-lo, mas você não deseja fazer com que o usuário Aguarde enquanto você fazer isso.

* Processamento de fila. Uma maneira comum para web front-end para se comunicar com um serviço de back-end é usar filas. Quando o site precisa fazer seu trabalho, ele envia uma mensagem em uma fila. Um serviço de back-end recebe mensagens da fila e faz o trabalho. Você poderia usar filas para processamento de imagens: por exemplo, depois que o usuário carrega um número de arquivos, colocar os nomes de arquivo em uma mensagem de fila para ser capturada pelo back-end para processamento. Ou você pode usar filas para melhorar a capacidade de resposta do site. Por exemplo, em vez de escrever diretamente para um banco de dados do SQL, gravar uma fila, informe o usuário que tiver terminado e permita o back-end serviço alça alta latência banco de dados relacional a trabalhar. Para um exemplo de fila de processamento com o processo de imagem, consulte o [tutorial WebJobs SDK Introdução](websites-dotnet-webjobs-sdk-get-started.md).

* Agregação RSS. Se você tiver um site que mantém uma lista de RSS feeds, você pode retirar em todos os artigos de feeds em um processo de plano de fundo.

* Arquivo de manutenção, como agregar ou limpeza de arquivos de log.  Você pode ter arquivos de log sendo criados por vários sites ou para períodos de tempo separada que você deseja combinar para executar trabalhos de análise neles. Ou talvez você queira agendar uma tarefa para execução semanal para limpar os arquivos de log antigos.

* Ingresso em tabelas do Azure. Você pode ter arquivos armazenados e blobs e deseja analisá-los e armazenar os dados em tabelas. A função de ingresso poderia estar escrevendo muitas linhas (milhões em alguns casos) e o SDK WebJobs possibilita a implementar essa funcionalidade facilmente. O SDK também fornece monitoramento em tempo real de indicadores de progresso como o número de linhas gravadas na tabela.

* Outras tarefas de execução longa que você deseja executar em uma thread de plano de fundo, como o [envio de emails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Qualquer tarefa que você deseja executar em uma agenda, como executar uma operação de backup todas as noites.

Em muitos desses cenários que você talvez queira dimensionar um aplicativo web para executar em várias VMs, que seriam executado WebJobs vários simultaneamente. Em alguns cenários, isso pode resultar nos mesmos dados sendo processados várias vezes, mas isso não é um problema quando você usa a fila interna, blob e gatilhos de barramento de serviço do WebJobs SDK. O SDK garante que suas funções serão processadas apenas uma vez para cada mensagem ou blob.

O SDK do WebJobs também torna mais fácil lidar com situações de tratamento de erros comuns. Você pode configurar alertas para enviar notificações quando uma função falha e você pode definir tempos limite para que uma função é automaticamente cancelada se não concluído dentro de um limite de tempo especificado.

## <a id="code"></a>Exemplos de código

O código para lidar com tarefas comuns que funcionam com o armazenamento do Azure é simples. Em seu aplicativo de Console `Main` método é criar uma `JobHost` objeto que coordenadas as chamadas para métodos que você escreve. A estrutura de WebJobs SDK sabe quando chamar seus métodos e que os valores de parâmetro para usar com base nos atributos de WebJobs SDK que usar neles. O SDK fornece *disparadores* que especificam o que condições causar a função a ser chamado e *fichários* que especificam como obter informações dentro e fora parâmetros do método.

Por exemplo, o atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) faz com que uma função a ser chamado quando uma mensagem for recebida em uma fila e se o formato da mensagem for JSON para um tipo personalizado ou uma matriz de bytes, a mensagem é desserializada automaticamente. O atributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) aciona um processo sempre que um novo blob é criado em uma conta de armazenamento do Azure.

Aqui está um programa simples que controla uma fila e cria um blob para cada mensagem de fila recebido:

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

O `JobHost` objeto é um contêiner para um conjunto de funções de plano de fundo. O `JobHost` objeto monitora as funções, inspeções para eventos que disparam-los, e executa as funções quando ocorrem eventos do disparador. Ligar para uma `JobHost` método para indicar se você deseja que o processo de contêiner para executar no thread atual ou um thread de plano de fundo. No exemplo, o `RunAndBlock` método executa o processo continuamente no segmento atual.

Porque o `ProcessQueueMessage` método neste exemplo tem um `QueueTrigger` atributo, o disparador dessa função é a recepção de uma nova mensagem de fila. O `JobHost` objeto inspeções para novas mensagens de fila na fila especificada ("webjobsqueue" neste exemplo) e quando uma for encontrada, ele chama `ProcessQueueMessage`. 

O `QueueTrigger` atributo associa a `inputText` parâmetro para o valor da mensagem fila. E o `Blob` atributo associa um `TextWriter` objeto para um blob denominado "blobname" em um contêiner chamado "containername".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

A função usa esses parâmetros para gravar o valor da mensagem fila o blob:

        writer.WriteLine(inputText);

Os recursos de disparadores e fichário do SDK WebJobs simplificam o código que você precisa escrever. O código de baixo nível necessário para processar arquivos, blobs ou filas ou iniciar tarefas agendadas, é feito para você pela estrutura WebJobs SDK. Por exemplo, a estrutura cria filas que não existirem ainda, abre a fila, leituras fila mensagens, exclui fila mensagens quando o processamento for concluído, cria contêineres de blob que não existem ainda, grava blobs e assim por diante.

O exemplo de código a seguir mostra uma variedade de disparadores em um WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, e `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Agendamento

O `TimerTrigger` atributo lhe permite às funções de disparadores para executar em um cronograma. Você pode programar uma WebJob como um todo por meio das funções individuais Azure ou cronograma de um WebJob usando o SDK do WebJobs `TimerTrigger`. Aqui está um exemplo de código.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Para mais código de amostra, consulte [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) no repositório do azure-webjobs-extensões de sdk em GitHub.com.

## <a name="extensibility"></a>Extensibilidade

Você não está limitado a funcionalidade interna – o WebJobs SDK permite escrever fichários e disparadores personalizados.  Por exemplo, você pode escrever disparadores para eventos de cache e agendas periódicas. Um [abrir repositório de origem](https://github.com/Azure/azure-webjobs-sdk-extensions) contém um [guia detalhado sobre extensibilidade WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) e código de exemplo para ajudar você a começar a escrever sua própria disparadores e fichários.

## <a id="workerrole"></a>Usando o SDK WebJobs fora do WebJobs

Um programa que usa o SDK do WebJobs é um aplicativo de Console padrão e podem ser executados em qualquer lugar – ele não precisa executar como um WebJob. Você pode testar o programa localmente em seu computador de desenvolvimento e em produção você pode executá-lo em uma função de trabalho do serviço de nuvem ou um serviço do Windows se você preferir um desses ambientes. 

No entanto, o painel só está disponível como uma extensão para um serviço de aplicativo do Azure web app. Se você quiser executar fora de um WebJob e ainda usar o painel, você pode configurar um aplicativo web para usar a mesma conta de armazenamento que se refere a cadeia de caracteres de conexão do WebJobs SDK Dashboard a e que WebJobs Dashboard do web app mostrará dados sobre execução de função do seu programa que está sendo executado em outro local. Você pode obter ao painel usando a URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para obter mais informações, consulte [Obtendo um painel para o desenvolvimento local com o SDK do WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mas observe que a postagem no blog mostra um nome de cadeia de conexão antigo. 

## <a id="nostorage"></a>Recursos de painel

O SDK do WebJobs oferece várias vantagens, mesmo se você não usa WebJobs SDK disparadores ou fichários:

* Você pode chamar funções no painel.
* Você pode reproduzir funções no painel.
* Você pode exibir logs no painel de controle, vinculados a determinada WebJob (logs de aplicativo, gravados usando Console.Out, Console.Error, rastreamento, etc.) ou vinculado à invocação de função específica que gerou (logs gravados usando um `TextWriter` objeto que o SDK passa para a função como um parâmetro). 

Para obter mais informações, consulte [como invocar manualmente uma função](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [como escrever logs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Próximas etapas

Para obter mais informações sobre o SDK WebJobs, consulte [Recursos de recomendados de WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

Para obter informações sobre os aperfeiçoamentos mais recentes para o SDK WebJobs, consulte as [Notas de versão](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
