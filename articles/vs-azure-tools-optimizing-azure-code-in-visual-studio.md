<properties
   pageTitle="Otimizar o código Azure no Visual Studio | Microsoft Azure"
   description="Saiba mais sobre como Azure código ferramentas de otimização no Visual Studio ajudam a tornar seu código mais robusto e melhor desempenho."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Otimizar o código Azure

Quando você estiver programando aplicativos que usam o Microsoft Azure, existem algumas práticas de codificação que você deverá seguir para ajudar a evitar problemas com escalabilidade de aplicativo, comportamento e desempenho em um ambiente de nuvem. A Microsoft fornece uma ferramenta de análise de código do Azure que reconhece e identifica vários desses problemas comumente encontrados e ajuda você a resolvê-los. Você pode baixar a ferramenta no Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Azure regras de análise de código

A ferramenta de análise de código do Azure usa as seguintes regras para sinalizar automaticamente seu código Azure quando encontrar problemas conhecidos afetam o desempenho. Detectados problemas aparecem como um avisos ou erros de compilador. Correções de código ou sugestões para resolver o erro ou aviso geralmente são fornecidas por meio de um ícone de lâmpada.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Evite usar o modo de estado de sessão (em processo) padrão

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Descrição

Se você usar o modo de estado de sessão (em processo) padrão para aplicativos em nuvem, você pode perder o estado da sessão.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Por padrão, o modo de estado de sessão especificado no arquivo Web. config está em processo. Além disso, se nenhuma entrada especificada no arquivo de configuração, o modo de estado de sessão padrão em processo. O modo em processo armazena estado de sessão na memória no servidor web. Quando uma instância seja reiniciada ou uma nova instância é usada para o suporte de failover ou balanceamento de carga, o estado da sessão armazenado na memória no servidor web não é salvo. Essa situação impede que o aplicativo está sendo scalable na nuvem.

Estado de sessão do ASP.NET suporta várias opções de armazenamento diferentes para os dados de estado de sessão: InProc, StateServer, SQL Server, personalizado e desligado. É recomendável que você use o modo de personalizado para hospedar dados em um armazenamento de estado de sessão externo, como o [provedor de estado de sessão do Azure para relacionada](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Solução

Uma solução recomendada é para armazenar o estado de sessão em um serviço de cache gerenciado. Saiba como usar o [provedor de estado de sessão do Azure para relacionada](http://go.microsoft.com/fwlink/?LinkId=401521) para armazenar seu estado de sessão. Você também pode armazenar o estado de sessão em outros locais para garantir que seu aplicativo esteja scalable na nuvem. Para saber mais sobre alternativa soluções leia [Modos de estado de sessão](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Método de execução não deve ser assíncrono

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Descrição

Criar métodos assíncronos (como [esperar](https://msdn.microsoft.com/library/hh156528.aspx)) fora o método [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e depois chamar os métodos de assíncrono de [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Declarar o método [[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) como assíncronas faz com que a função de trabalho inserir um loop reiniciar.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Chamar métodos de assíncrono dentro do método [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) faz com que o tempo de execução do serviço de nuvem à Lixeira a função de trabalho. Quando uma função de trabalho é iniciado, todos os execução do programa ocorre dentro do método [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Sair do método [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) faz com que a função de trabalho reiniciar. Quando o tempo de execução de função de trabalho atinge o método assíncrono, ele envia todas as operações após o método assíncrono e, em seguida, retorna. Isso faz com que a função de trabalho sair do método [[[[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e reiniciar. Na próxima iteração de execução, a função de trabalho atinge o método assíncrono novamente e reinicia, fazendo com que a função de trabalho para a Lixeira novamente também.

### <a name="solution"></a>Solução

Coloque todas as operações de assíncrono fora o método [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Em seguida, chame o método de refatorado assíncrono de dentro do método [[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , como RunAsync () .wait. A ferramenta de análise de código do Azure pode ajudá-lo a corrigir esse problema.

O trecho de código a seguir demonstra a correção de código para esse problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Usar a autenticação de assinatura de acesso compartilhado do serviço barramento

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Descrição

Use assinatura de acesso compartilhado (SAS) para autenticação. Serviço de controle de acesso (ACS) está sendo excluída para autenticação de barramento de serviço.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Para maior segurança, Active Directory do Azure está substituindo autenticação ACS com autenticação SAS. Consulte o [Azure Active Directory é o futuro da ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) para obter informações sobre o plano de transição.

### <a name="solution"></a>Solução

Use a autenticação de SAS em seus aplicativos. O exemplo a seguir mostra como usar um token SAS existente para acessar um namespace do barramento de serviço ou entidade.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Consulte os tópicos a seguir para obter mais informações.

- Para obter uma visão geral, consulte [Autenticação de assinatura de acesso compartilhado com barramento de serviço](https://msdn.microsoft.com/library/dn170477.aspx)

- [Como usar autenticação de assinatura de acesso compartilhado com barramento de serviço](https://msdn.microsoft.com/library/dn205161.aspx)

- Para um projeto de amostra, consulte [usando compartilhados acesso assinatura (SAS) autenticação com assinaturas do barramento de serviço](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Considere usar o método OnMessage para evitar "receber loop"

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Descrição

Para evitar indo para um "loop de recebimento", chamar o método **OnMessage** é a melhor solução para receber mensagens que chamar o método de **recebimento** . No entanto, se você deve usar o método de **recebimento** e você especifica o tempo de espera de um servidor não padrão, verifique se que o tempo de espera do servidor for mais de um minuto.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Ao chamar **OnMessage**, o cliente inicia uma bombeador interno que monitora constantemente a fila ou assinatura. Este bombeador contém um loop infinito que emite uma chamada para receber mensagens. Se a chamada expire, ele emite uma nova chamada. O intervalo de tempo limite é determinado pelo valor da propriedade [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) do [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)que está sendo usado.

A vantagem de usar **OnMessage** comparada a **receber** é que os usuários não precisam poll para mensagens, manipular exceções, processar várias mensagens em paralelo e conclua as mensagens manualmente.

Se você chamar **recebimento** sem usar seu valor padrão, certifique-se de que o valor de *ServerWaitTime* é mais de um minuto. Definindo *ServerWaitTime* para mais de um minuto impede que o servidor expirando antes que a mensagem for recebida totalmente.

### <a name="solution"></a>Solução

Consulte os seguintes exemplos de código para usos recomendados. Para obter mais detalhes, consulte [O método de QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)e [O método de QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Para melhorar o desempenho da infraestrutura de mensagens do Azure, consulte o padrão de design [Assíncrono introdução de mensagens](https://msdn.microsoft.com/library/dn589781.aspx).

A seguir é um exemplo de como usar **OnMessage** para receber mensagens.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

A seguir é um exemplo do uso de **recebimento** com o tempo de espera do servidor padrão.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

A seguir é um exemplo do uso de **recebimento** com um tempo de espera do servidor não padrão.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Considere o uso de métodos de barramento de serviço assíncronos

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Descrição

Use métodos de barramento de serviço assíncronos para melhorar o desempenho com comunicação de mensagens.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Usando métodos assíncronos permite concorrência de programa de aplicativo porque executando cada chamada não bloquear o thread principal. Ao usar o serviço barramento mensagens métodos, executando uma operação (enviar, receber, excluir, etc) leva tempo. Esse tempo inclui o processamento da operação pelo serviço barramento de serviço além a latência da solicitação e resposta. Para aumentar o número de operações por hora, operações devem executar simultaneamente. Para obter mais informações consulte [Práticas recomendadas para desempenho melhorias usando o serviço barramento orientado de mensagens](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solução

Consulte [A classe de QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) para obter informações sobre como usar o método assíncrono recomendado.

Para melhorar o desempenho da infraestrutura de mensagens do Azure, consulte o padrão de design [Assíncrono introdução de mensagens](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Considere partição tópicos e filas do barramento de serviço

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Descrição

Filas de barramento de serviço de partição e tópicos para obter melhor desempenho com barramento de serviço de mensagens.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Partição tópicos e filas do barramento de serviço aumenta o desempenho produtividade e disponibilidade do serviço, porque a produtividade geral de uma fila particionada ou tópico já não é limitada pelo desempenho de um agente de única mensagem ou o armazenamento de mensagens. Além disso, uma interrupção temporária de um armazenamento de mensagens não disponibiliza um particionada fila ou tópico. Para obter mais informações, consulte [Partição entidades de mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solução

O trecho de código a seguir mostra como partição entidades mensagens.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Para obter mais informações, consulte [filas de barramento de serviço particionado e tópicos | Blog do Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) e confira a amostra de [Fila particionado do Microsoft Azure serviço barramento](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>Não defina SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Descrição

Evite usar SharedAccessStartTimeset para a hora atual para iniciar imediatamente a política de acesso compartilhado. Você precisa definir essa propriedade se você deseja iniciar a política de acesso compartilhado posteriormente.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Sincronização do relógio faz com que uma diferença de tempo pequeno entre data centers. Por exemplo, talvez você pense logicamente definindo a hora de início de uma política SAS de armazenamento como a hora atual usando Now ou um método semelhante fará com que a política SAS efetivadas imediatamente. No entanto, as diferenças de horário ligeiras entre data centers podem causar problemas com isso desde que algumas vezes do data center podem ser ligeiramente posteriores a hora de início, enquanto outros com antecedência-lo. Como resultado, a política SAS pode expirar rapidamente (ou até mesmo imediatamente) se o ciclo de vida de política é definido muito curto.

Para obter mais orientações sobre como usar a assinatura de acesso compartilhado no armazenamento do Azure, consulte [Introdução ao tabela SAS (compartilhados acesso assinatura), fila SAS e a atualização Blob SAS - Blog de equipe de armazenamento do Microsoft Azure - Site Home - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução

Remova a instrução que define a hora de início da política acesso compartilhado. A ferramenta de análise de código do Azure fornece uma correção para esse problema. Para obter mais informações sobre gerenciamento de segurança, consulte o padrão de design [Padrão de chave manobrista](https://msdn.microsoft.com/library/dn568102.aspx).

O trecho de código a seguir demonstra a correção de código para esse problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Compartilhado a hora de expiração deve ser mais de cinco minutos de política de acesso

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Descrição

Pode haver quanto um minuto cinco diferença em relógios entre data centers em locais diferentes devido a uma condição conhecida como "relógio distorção". Para impedir que as associações de segurança token de política de expiração antes da data planejada, defina a hora de expiração sejam mais de cinco minutos.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Data centers em diferentes locais no mundo sincronizam por um sinal de relógio. Como demora para sinal de relógio para viagem para locais diferentes, pode ser uma variação de tempo entre data centers em diferentes locais geográficos embora tudo supostamente é sincronizado. Essa diferença de horário pode afetar o intervalo de tempo e expiração de início do diretiva acesso compartilhadas. Portanto, para garantir a política de acesso compartilhado entra em vigor imediatamente, não especifica a hora de início. Além disso, verifique se que a hora de vencimento for mais de 5 minutos para impedir que o tempo limite antecipado.

Para obter mais informações sobre como usar a assinatura de acesso compartilhado em armazenamento do Azure, consulte [Introdução ao tabela SAS (compartilhados acesso assinatura), fila SAS e a atualização Blob SAS - Blog de equipe de armazenamento do Microsoft Azure - Site Home - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução

Para obter mais informações sobre gerenciamento de segurança, consulte o padrão de design [Padrão de chave manobrista](https://msdn.microsoft.com/library/dn568102.aspx).

A seguir é um exemplo de não especificar uma hora de início de política de acesso compartilhado.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

A seguir é um exemplo de especificar uma hora de início de política de acesso compartilhadas com uma duração de expiração de política maior que cinco minutos.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Para obter mais informações, consulte [criar e usar uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Use CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Descrição

Usando a classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) para projetos como site do Azure e serviços móveis Azure não introduzir problemas de tempo de execução. Como prática recomendada, no entanto, é recomendável usar nuvem[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) como uma forma unificada de gerenciar configurações para todos os aplicativos de nuvem do Azure.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

CloudConfigurationManager lê o arquivo de configuração apropriado para o ambiente do aplicativo.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solução

Refatorar seu código para usar a [Classe de CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Uma correção de código para esse problema é fornecida pela ferramenta de análise de código do Azure.

O trecho de código a seguir demonstra a correção de código para esse problema. Substituir

`var settings = ConfigurationManager.AppSettings["mySettings"];`

com

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Aqui está um exemplo de como armazenar a configuração em um arquivo App. config ou Web. config. Adicione as configurações na seção appSettings do arquivo de configuração. O que vem a seguir é o arquivo Web. config para o exemplo de código anterior.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Evitar o uso de cadeias de caracteres de conexão embutido

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Descrição

Se você usar cadeias de caracteres de conexão embutidos e você precisa atualizá-los mais tarde, você precisa fazer alterações em seu código-fonte e recompilar o aplicativo. No entanto, se você armazenar suas cadeias de caracteres de conexão em um arquivo de configuração, você pode alterá-las posteriormente simplesmente atualizando o arquivo de configuração.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Embutir cadeias de caracteres de conexão é uma prática ruim porque ele apresenta problemas quando cadeias de caracteres de conexão precisam ser alterada rapidamente. Além disso, se o projeto precisa fazer check-in ao controle de origem, cadeias de caracteres de conexão embutida introduzem vulnerabilidades de segurança, desde que as cadeias de caracteres podem ser visualizadas no código-fonte.

### <a name="solution"></a>Solução

Armazene cadeias de conexão nos arquivos de configuração ou ambientes Azure.

- Para aplicativos standalone, use App para armazenar as configurações de cadeia de conexão.

- Para aplicativos web hospedado no IIS, use o Web. config para armazenar cadeias de caracteres de conexão.

- Para aplicativos do ASP.NET vNext, use configuration.json para armazenar cadeias de caracteres de conexão.

Para obter informações sobre como usar arquivos de configurações como Web. config ou App, consulte [Diretrizes de configuração de Web do ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Para obter informações sobre como Azure trabalho de variáveis de ambiente, consulte [Sites do Azure: como cadeias de caracteres de aplicativo e trabalhar de cadeias de caracteres de Conexão](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Para obter informações sobre como armazenar a cadeia de conexão no controle de origem, consulte [Evite colocar informações confidenciais como cadeias de caracteres de conexão em arquivos que estão armazenadas no repositório de código fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Usar arquivo de configuração de diagnóstico

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Descrição

Em vez de configurar as configurações do diagnóstico em seu código como usando o Microsoft.WindowsAzure.Diagnostics API de programação, você deve configurar as configurações do diagnóstico no arquivo diagnostics.wadcfg. (Ou, se você usa o Azure SDK 2,5 de diagnostics.wadcfgx). Fazendo isso, você pode alterar as configurações do diagnóstico sem precisar recompilar seu código.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Antes do Azure SDK 2,5 (que usa o diagnóstico do Azure 1.3), diagnóstico do Azure (WAD) pôde ser configurada usando vários métodos diferentes: adicionando-lo para o blob de configuração do armazenamento, usando o código fundamental, configuração declarativa ou a configuração padrão. No entanto, a melhor maneira de configurar diagnósticos é usar um arquivo de configuração de XML (diagnostics.wadcfg ou diagnositcs.wadcfgx para o SDK 2,5 e posterior) no projeto de aplicativo. Nesta abordagem, o arquivo diagnostics.wadcfg completamente define a configuração e pode ser atualizado e redistribuído à vontade. Misturar o uso do arquivo de configuração diagnostics.wadcfg com os métodos de programação de configurações usando as classes [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)ou [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)pode causar confusão. Para obter mais informações, consulte [inicializar ou alterar configuração de diagnóstico do Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

Começando com WAD 1.3 (incluído no Azure SDK 2.5), não é possível usar código para configurar diagnósticos. Como resultado, você só pode fornecer a configuração ao aplicando ou atualizar a extensão de diagnóstico.

### <a name="solution"></a>Solução

Use o designer de configuração de diagnóstico para mover as definições de diagnóstico para o arquivo de configuração de diagnóstico (diagnositcs.wadcfg ou diagnositcs.wadcfgx para o SDK 2,5 e posterior). Também é recomendável que você instale [Azure SDK 2,5](http://go.microsoft.com/fwlink/?LinkId=513188) e use o recurso de diagnóstico mais recente.

1. No menu de atalho para a função que você deseja configurar, escolha Propriedades e, em seguida, escolha a guia Configuração.

1. Na seção **Diagnóstico** , certifique-se de que a caixa de seleção **Habilitar o diagnóstico** está selecionada.

1. Escolha o botão **Configurar** .

  ![Acessando a opção habilitar o diagnóstico](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Consulte [Configurar o diagnóstico de serviços de nuvem do Azure e máquinas virtuais](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para obter mais informações.


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Evite declarando DbContext objetos como estático

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Descrição

Para economizar memória, evite declarando DBContext objetos como estático.

Compartilhe suas ideias e os comentários em [comentários de análise de código do Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Objetos de DBContext mantiverem os resultados da consulta de cada chamada. Em objetos estáticos DBContext não são descartados até que o domínio de aplicativo é descarregado. Portanto, um objeto DBContext estático pode consumir grandes quantidades de memória.

### <a name="solution"></a>Solução

Declarar DBContext como uma variável local ou campo não estático instância, usá-lo para uma tarefa e deixe-ser descartada após o uso.

O exemplo a seguir classe de controlador MVC mostra como usar o objeto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre optimzing e aplicativos do Azure de solução de problemas, consulte [Solucionar problemas de um aplicativo web no serviço de aplicativo do Azure usando o Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
