<properties
    pageTitle="Monitorar seu APIs com gerenciamento de API Azure, Hubs de evento e Runscope"
    description="Aplicativo de exemplo demonstrando a política de log para eventhub conexão gerenciamento de API do Azure, Hubs de evento do Azure e Runscope para HTTP registro em log e monitoramento"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Monitorar seu APIs com gerenciamento de API Azure, Hubs de evento e Runscope

O [serviço de gerenciamento de API](api-management-key-concepts.md) fornece vários recursos para aprimorar o processamento de solicitações HTTP enviados para seu API HTTP. Entretanto, a existência das solicitações e respostas são temporárias. A solicitação é feita e ele flui através do serviço de gerenciamento de API para sua API de back-end. Sua API processa a solicitação e uma resposta flui através de volta para o consumidor de API. O serviço de gerenciamento de API mantém algumas estatísticas importantes sobre as APIs para exibição no painel portal do Publisher, mas além que, sumiram os detalhes.

Usando a de [log para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [política](api-management-howto-policies.md) no serviço de gerenciamento de API, você pode enviar quaisquer detalhes da solicitação e resposta a um [Hub de evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Há vários motivos por que talvez você queira gerar eventos de mensagens HTTP está sendo enviadas para seu APIs. Alguns exemplos incluem trilha de auditoria de atualizações, a análise de uso, alertas de exceção e 3º integrações de terceiros.   

Este artigo mostra como capturar a mensagem de solicitação e resposta HTTP inteira, enviá-la a um Hub de evento e, em seguida, retransmissão essa mensagem para um serviço que fornece HTTP registro em log e monitoramento de serviços de terceiros.

## <a name="why-send-from-api-management-service"></a>Por que enviar do serviço de gerenciamento de API?
É possível gravar middleware HTTP que pode se conecte com estruturas de API HTTP para capturar solicitações e respostas HTTP e feed-los em log e monitoramento de sistemas. A desvantagem dessa abordagem é a middleware HTTP precisa ser integrado ao back-end API e deve corresponder a plataforma da API. Se houver várias APIs cada um deles deve implantar o middleware. Geralmente há motivos por back-end APIs não pode ser atualizado.

Usando o serviço de gerenciamento de API do Azure para integrar com o log de infraestrutura fornece uma solução centralizada e independente de plataforma. Também é scalable, em parte devido aos recursos de [replicação geográfica](api-management-howto-deploy-multi-region.md) do gerenciamento de API do Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Por que enviar a um Hub de evento do Azure?
É uma aconselhável perguntar, por que criar uma política específica Hubs de evento do Azure? Há muitos lugares diferentes, onde talvez queira fazer logon Minhas solicitações. Por que não basta enviar as solicitações diretamente para o destino final?  Essa é uma opção. No entanto, ao fazer solicitações de log de um serviço de gerenciamento de API, é necessário considerar como mensagens de log afetará o desempenho da API. Graduais aumentos de carga podem ser tratados aumentando disponíveis instâncias de componentes do sistema ou aproveitando de replicação geográfica. No entanto, curtos picos de tráfego podem causar solicitações deve ser atrasada significativamente se solicitações a infraestrutura de log começar a reduzir sob carga.

Os Hubs de evento do Azure foi projetado para grandes volumes de entrada de dados, com capacidade para lidar com um número muito maior de eventos do que o número de solicitações HTTP maioria dos processo APIs. O Hub de evento atua como um tipo de buffer sofisticado entre seu serviço de gerenciamento de API e a infraestrutura que armazenará e processar as mensagens. Isso garante que o desempenho da API não será afetado devido a infraestrutura de log.  

Depois que os dados foi passados a um Hub de evento, ele é mantido e esperará para consumidores de evento Hub processá-lo. Hub do evento, não importa como ele será processado, basta importantes sobre como garantir que a mensagem serão entregues com êxito.     

Evento Hubs têm a capacidade de eventos de fluxo para vários grupos de consumidor. Isso permite que eventos sejam processados pelo sistemas completamente diferentes. Isso permite oferecer suporte a muitos cenários de integração sem colocar atrasos de adição do processamento da solicitação API dentro do serviço de gerenciamento de API como apenas um evento precisa ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicativo/http
Um Hub de evento aceita dados de evento como uma cadeia de caracteres simple. O conteúdo desta sequência completamente está até você. Para poder empacotar uma solicitação HTTP e enviá-lo a Hubs de evento, precisamos formatar a cadeia de caracteres com as informações de solicitação ou resposta. Em situações como essa, se houver um formato existente podemos reutilizar, talvez não precisamos escrever nossa própria análise de código. Inicialmente considera uma usando o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar solicitações e respostas HTTP. No entanto, esse formato é otimizado para armazenar uma sequência de solicitações HTTP em um formato JSON com base. Continha um número de elementos obrigatórios que adicionou complexidade desnecessária para o cenário de passar a mensagem HTTP durante a transmissão.  

Uma opção alternativa foi usar o `application/http` tipo de mídia, conforme descrito na especificação de HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Este tipo de mídia usa o mesmo formato exato que é usado para enviar mensagens HTTP durante a transmissão, mas a mensagem inteira pode ser colocar no corpo da solicitação HTTP outra. Em nosso caso apenas vamos usar o corpo como nossa mensagem para enviar aos Hubs de evento. Convenientemente, há um analisador que existe em bibliotecas de [Cliente do Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que podem analisar esse formato e convertê-la em nativa `HttpRequestMessage` e `HttpResponseMessage` objetos.

Para poder criar esta mensagem que precisamos aproveitar c# com base em [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) de gerenciamento de API do Azure. Aqui está a política que envia uma mensagem de solicitação HTTP para Hubs de evento do Azure.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Declaração de política
Há algumas coisas determinadas vale a pena mencionar sobre essa expressão de política. A política de log para eventhub tem um atributo chamado id agente que se refere ao nome do registrador que foi criado dentro do serviço de gerenciamento de API. Os detalhes de como configurar um agente de log de eventos Hub no serviço de gerenciamento de API podem ser encontrados no documento [como registrar eventos no Azure Hubs de evento no gerenciamento de API do Azure](api-management-howto-log-event-hubs.md). O atributo segundo é um parâmetro opcional que instrui Hubs de evento que partição para armazenar a mensagem. Hubs de evento usam partições para habilitar scalabilty e exigem um mínimo de dois. A entrega ordenada de mensagens somente certamente dentro de uma partição. Se não podemos instruir o Hub de evento no qual partição coloca a mensagem, ela usará um algoritmo alternada para distribuir a carga. No entanto, que podem causar algumas das nossas mensagens sejam processados fora de ordem.  

### <a name="partitions"></a>Partições
Para garantir que nossos mensagens são entregues para consumidores em ordem e aproveitar o recurso de distribuição de carga de partições, eu escolhi enviar mensagens de solicitação HTTP para uma partição e mensagens de resposta HTTP em uma segunda partição. Isso garante uma distribuição de carga par e podemos garantir que todas as solicitações serão consumidas na ordem e todas as respostas serão consumidas na ordem. É possível por uma resposta a ser consumida antes da solicitação correspondente, mas como que não é um problema, como temos um mecanismo diferente para correlação solicita respostas e sabemos que solicitações sempre aparecem antes de respostas.

### <a name="http-payloads"></a>Cargas HTTP
Depois de construção a `requestLine` podemos verificar se o corpo da solicitação deve ser truncado. O corpo da solicitação é truncado para 1024 somente. Isso pode ser aumentado, porém mensagens individuais do Hub de evento limitam-se para 256KB, portanto, é provável que algumas mensagens HTTP corpos será não se encaixam em uma única mensagem. Ao fazer log e a análise de uma grande quantidade de informações pode ser derivada de apenas a linha de solicitação HTTP e os cabeçalhos. Além disso, muitas solicitações de API retornam apenas corpos pequenos e então a perda de valor de informações por truncar grandes corpos é bastante mínima em comparação com a redução de transferência, processamento e os custos de armazenamento para manter todo o conteúdo de corpo. Uma observação final sobre o corpo de processamento é que precisamos passar `true` às<string>método () porque podemos estiver lendo o conteúdo do corpo, mas também foi desejado back-end API possam ler o corpo. Passando true para este método podemos fazer com que o corpo buffer para que ele possa ser lido uma segunda vez. Isso é importante a considerar se você tiver uma API que faz o upload de arquivos muito grandes ou usa sondagem longa. Nesses casos seria melhor evitar todo o corpo de leitura.   

### <a name="http-headers"></a>Cabeçalhos HTTP
Cabeçalhos HTTP podem ser transferidos simplesmente por para o formato de mensagem em um formato de par chave/valor simples. Podemos optou por retirar determinados campos confidenciais de segurança, para evitar desnecessariamente perdendo informações de credenciais. É provável que chaves de API e outras credenciais seriam usados para fins de análise. Se desejamos fazer análise no produto específico e usuário que estão usando e poderia recebemos isso o `context` objeto e adicioná-las à mensagem.     
### <a name="message-metadata"></a>Metadados de mensagem
Ao criar a mensagem completa para enviar para o hub de evento, a primeira linha não é realmente parte do `application/http` mensagem. A primeira linha é metadados adicionais consiste se a mensagem é uma solicitação ou mensagem de resposta e uma id de mensagem que é usada para solicitações de respostas de correlação. A id da mensagem é criada usando outra política que tem esta aparência:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Podemos poderia ter criado a mensagem de solicitação, armazenado que em uma variável até que a resposta foi retornada e simplesmente enviada a solicitação e resposta como uma única mensagem. No entanto, enviando a solicitação e resposta de maneira independente e usando um id de mensagem para correlação das duas, podemos obter um pouco mais flexibilidade no tamanho da mensagem, a capacidade de tirar proveito de várias partições enquanto mantém a ordem das mensagens e a solicitação aparecerão em nosso painel de log mais cedo. Também pode haver alguns cenários onde uma resposta válida nunca é enviada para o hub de evento, possivelmente devido a um erro de solicitação fatal no serviço de gerenciamento de API, mas ainda teremos um registro da solicitação.

A política para enviar a mensagem de resposta HTTP parece muito semelhante à solicitação e então a configuração de política concluída tem esta aparência:

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

O `set-variable` diretiva cria um valor que pode ser acessado por ambos os `log-to-eventhub` política no `<inbound>` seção e o `<outbound>` seção.  

## <a name="receiving-events-from-event-hubs"></a>Receber eventos de Hubs de evento
Eventos de Hub de evento do Azure são recebidos usando o [protocolo AMQP](http://www.amqp.org/). A equipe do barramento de serviço do Microsoft fez cliente bibliotecas disponíveis para facilitar os consumindo eventos. Há duas abordagens diferentes têm suportadas, um está sendo um *Consumidor direto* e o outro está usando o `EventProcessorHost` classe. Exemplos dessas duas abordagens podem ser encontrados no [Guia de programação de Hubs de evento](../event-hubs/event-hubs-programming-guide.md). A versão curta das diferenças é, `Direct Consumer` lhe dá controle total e a `EventProcessorHost` faz parte do trabalho canalização para você mas faz determinadas suposições sobre como você processará os eventos.  

### <a name="eventprocessorhost"></a>EventProcessorHost
Neste exemplo, usaremos a `EventProcessorHost` para simplificar, porém pode não a melhor opção para esse cenário específico. `EventProcessorHost`faz o trabalho árduo de garantir que você não precisa se preocupar sobre uso de threads problemas dentro de uma classe de processador de evento específico. No entanto, em nosso cenário, estamos simplesmente converter a mensagem em outro formato e passando ao longo para outro serviço usando um método assíncrono. Não é necessário para atualizar o estado compartilhado e, portanto, não há risco de lidar com threads problemas. Para a maioria dos cenários, `EventProcessorHost` é provavelmente a melhor opção e certamente é a opção mais fácil.     

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central ao usar `EventProcessorHost` é criar uma uma implementação do `IEventProcessor` interface que contém o método `ProcessEventAsync`. A essência desse método é mostrada aqui:

  assíncrono {IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) tarefa

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Uma lista de objetos EventData são passados para o método e iteramos sobre essa lista. Os bytes de cada método são analisados em um objeto de HttpMessage e esse objeto é passado para uma instância de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
O `HttpMessage` instância contém três partes de dados:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

O `HttpMessage` instância contém um `MessageId` GUID que nos permite conectar-se a solicitação HTTP para a resposta HTTP correspondente e um valor booliano que identifica se o objeto contiver uma instância de um HttpRequestMessage e HttpResponseMessage. Usando a interna no classes de HTTP de `System.Net.Http`, conseguiu aproveitar o `application/http` analisar o código que está incluído em `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
O `HttpMessage` instância é então encaminhada a implementação de `IHttpMessageProcessor` que é uma interface que eu criei para desassociar o recebimento e interpretação do evento do Hub de evento do Azure e o processamento real dele.


## <a name="forwarding-the-http-message"></a>Encaminhar a mensagem HTTP
Neste exemplo, eu decidiu seria interessante enviar a solicitação de HTTP sobre para [Runscope](http://www.runscope.com). Runscope é um serviço baseado em nuvem especializada em HTTP depuração, registro em log e monitoramento. Eles tem uma camada livre, portanto, é fácil experimentar e nos permite ver as solicitações HTTP no fluindo em tempo real através de nosso serviço de gerenciamento de API.

O `IHttpMessageProcessor` implementação parecida com esta,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Conseguiu tirar proveito de uma [biblioteca de cliente existente para Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) que torna mais fácil push `HttpRequestMessage` e `HttpResponseMessage` instâncias para cima em seus serviços. Para acessar a API Runscope você precisará de uma conta e uma chave API. Instruções para obter uma chave API podem ser encontradas no screencast [Criando aplicativos à API de Runscope de acesso](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Exemplo completos
O [código fonte](https://github.com/darrelmiller/ApimEventProcessor) e testes de amostra estão no Github. Você precisará de um [Serviço de gerenciamento de API](api-management-get-started.md), [Um Hub de evento conectada](api-management-howto-log-event-hubs.md)e uma [Conta de armazenamento](../storage/storage-create-storage-account.md) para executar a amostra para si mesmo.   

A amostra é apenas um simple aplicativo de Console que ouve para eventos que vêm do Hub de evento, converte-os em uma `HttpRequestMessage` e `HttpResponseMessage` objetos e encaminha-lo a API Runscope.

Na imagem animada a seguir, você pode ver uma solicitação seja feita para uma API no Portal do desenvolvedor, o aplicativo de Console mostrando a mensagem sendo recebido, processadas e encaminhado e, em seguida, a solicitação e resposta aparecendo no Inspetor de tráfego de Runscope.

![Demonstração de solicitação seja encaminhada para Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
Serviço de gerenciamento de API Azure oferece um lugar ideal para capturar o tráfego HTTP viajando para e do seu APIs. Azure Hubs de evento é uma solução altamente escalável e de baixo custo para capturar o tráfego e alimentação-sistemas de processamento secundário para o registro em log, monitoramento e outros sofisticados analytics. Conectando-se ao tráfego de festa 3º monitorar sistemas como Runscope é um simples como algumas dezenas linhas de código.

## <a name="next-steps"></a>Próximas etapas
-   Saiba mais sobre Hubs de evento do Azure
    -   [Começar com Hubs de evento do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guia de programação de Hubs de evento](../event-hubs/event-hubs-programming-guide.md)
-   Saiba mais sobre a integração de gerenciamento de API e Hubs de evento
    -   [Como fazer logon eventos Hubs de evento do Azure no gerenciamento de API do Azure](api-management-howto-log-event-hubs.md)
    -   [Referência de entidade do agente](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referência de política de log para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    