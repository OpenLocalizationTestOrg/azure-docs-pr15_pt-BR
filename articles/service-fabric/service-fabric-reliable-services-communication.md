<properties
   pageTitle="Visão geral de comunicação confiável dos serviços | Microsoft Azure"
   description="Visão geral do modelo de comunicação serviços confiáveis, incluindo ouvintes de abertura nos serviços, a resolução de pontos de extremidade e comunicar-se entre os serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como usar os APIs de comunicação de serviços confiáveis

Azure tecidos de serviço como uma plataforma é completamente independente sobre a comunicação entre os serviços. Todos os protocolos e pilhas são aceitáveis, de UDP para HTTP. Ele é o desenvolvedor do serviço para escolher como os serviços devem se comunicar. A estrutura do aplicativo de serviços confiáveis fornece pilhas de comunicação interna, além de APIs que você pode usar para criar os componentes de comunicação personalizados. 

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço

A API de serviços confiável usa uma interface simples para comunicação de serviço. Para abrir um ponto de extremidade para o serviço, basta implemente essa interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Você poderá adicionar sua implementação de ouvinte de comunicação retornando-o em um método de substituição de classe baseada em serviços.

Para serviços sem estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Para serviços de estado:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Em ambos os casos, você deve retornar uma coleção de ouvintes. Isso permite que o seu serviço escutam em vários pontos de extremidade, potencialmente usando protocolos diferentes, usando vários ouvintes. Por exemplo, você pode ter um ouvinte HTTP e um ouvinte WebSocket separado. Cada ouvinte obtém um nome e a coleção resultante de *nome: endereço* pares é representado por um objeto JSON quando um cliente solicita os endereços ouvintes para uma instância de serviço ou uma partição.

Em um serviço de estado, a substituição retorna uma coleção de ServiceInstanceListeners. Um ServiceInstanceListener contém uma função para criar um ICommunicationListener e atribui-lhe um nome. Para serviços de estado, a substituição retorna uma coleção de ServiceReplicaListeners. Este é um pouco diferente de seu correspondente sem estado, como um ServiceReplicaListener tem uma opção para abrir um ICommunicationListener em réplicas secundárias. Não só você pode usar vários ouvintes de comunicação em um serviço, mas você também pode especificar quais ouvintes aceitam solicitações em réplicas secundárias e quais ouvir somente em réplicas principais.

Por exemplo, você pode ter um ServiceRemotingListener que leva chamadas RPC apenas em réplicas principais e um ouvinte segundo, personalizado que leva leitura solicitações em réplicas secundárias sobre HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Ao criar vários ouvintes para um serviço, cada ouvinte **deve** receber um nome exclusivo.

Finalmente, descreva os pontos de extremidade que são necessários para o serviço do [serviço manifesto](service-fabric-application-model.md) sob a seção em pontos de extremidade.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode acessar os recursos de ponto de extremidade alocados a partir do `CodePackageActivationContext` na `ServiceContext`. O ouvinte pode iniciar escutar solicitações quando ele é aberto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Recursos de ponto de extremidade são comuns para o pacote de todo o serviço, e eles são alocados pelo serviço tecidos quando o pacote de serviço está ativado. Várias réplicas de serviço hospedadas no mesmo ServiceHost podem compartilhar a mesma porta. Isso significa que o ouvinte de comunicação deve oferece suporte para compartilhamento de porta. A maneira recomendada de fazer isso é o ouvinte de comunicação usar a partição ID e ID de réplica/instância quando ele gera o endereço de ouvir.

### <a name="service-address-registration"></a>Registro de endereço do serviço

Um serviço do sistema chamado de *Serviço de cadastramento* é executado no serviço tecidos clusters. O serviço de nomenclatura é um registrador de serviços e seus respectivos endereços que cada instância ou réplica do serviço está escutando. Quando o `OpenAsync` método de um `ICommunicationListener` for concluído, seu retorno valor obtém registrada no serviço de nomenclatura. Esse valor de retorno que é publicado no serviço de nomenclatura é uma cadeia de caracteres cujo valor pode ser absolutamente qualquer coisa. Esse valor de cadeia de caracteres é o que os clientes verão quando eles pedirem para um endereço para o serviço do serviço de nomenclatura.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Serviço tecidos fornece APIs que permite que clientes e outros serviços pedir este endereço por nome do serviço. Isso é importante porque o endereço do serviço não é estático. Serviços são movidos em torno do cluster para fins de balanceamento e disponibilidade de recursos. Este é o mecanismo que permite aos clientes resolver o endereço de ouvinte para um serviço.

> [AZURE.NOTE] Para uma completa passo a passo sobre como gravar uma `ICommunicationListener`, consulte [Serviços de Web API do serviço tecidos com OWIN hospedagem interna](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Comunicação com um serviço
A API de serviços confiável fornece as seguintes bibliotecas para gravar clientes que se comunicar com os serviços.

### <a name="service-endpoint-resolution"></a>Resolução de ponto de extremidade do serviço
A primeira etapa para a comunicação com um serviço é para resolver um endereço de ponto de extremidade da partição ou instância do serviço que você deseja falar. O `ServicePartitionResolver` utilitário de classe é um primitivo básico que ajuda os clientes a determinar o ponto de extremidade de um serviço no tempo de execução. Na terminologia do serviço tecidos, o processo de determinar o ponto de extremidade de um serviço é denominado a *resolução de ponto de extremidade do serviço*.

Para se conectar aos serviços dentro de um cluster, `ServicePartitionResolver` podem ser criados usando as configurações padrão. Este é o uso recomendado na maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Para se conectar aos serviços em um cluster diferente, um `ServicePartitionResolver` podem ser criados com um conjunto de pontos de extremidade do cluster gateway. Observe que os pontos de extremidade do gateway são diferentes apenas pontos de extremidade para conexão com o mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Como alternativa, `ServicePartitionResolver` podem ser fornecidas uma função para a criação de um `FabricClient` usar internamente: 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`é o objeto que é usado para se comunicar com o cluster de estrutura de serviço de várias operações de gerenciamento no cluster. Isso é útil quando você quiser mais controle sobre como `ServicePartitionResolver` interage com seu cluster. `FabricClient`executa um cache internamente e é geralmente cara criem, portanto é importante reutilizar `FabricClient` instâncias tanto quanto possíveis. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Um método de resolver, em seguida, é usado para recuperar o endereço de um serviço ou uma partição de serviço para serviços particionadas.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Um endereço de serviço pode ser resolvido facilmente usando um `ServicePartitionResolver`, mas mais trabalho é necessário para garantir o endereço resolvido pode ser usado corretamente. Seu cliente precisará detectar se a tentativa de conexão falhou devido a um erro temporário e pode ser repetida (por exemplo, serviço movido ou está temporariamente indisponível), ou um erro permanente (por exemplo, o serviço foi excluído ou o recurso solicitado não existe mais). Instâncias de serviço ou réplicas podem mover de nó para nó a qualquer momento por vários motivos. O endereço de serviço resolvido por meio de `ServicePartitionResolver` podem ser atualizados no momento em que o código do seu cliente tenta se conectar. Nesse caso novamente o cliente precisará resolver novamente o endereço. Fornecendo anterior `ResolvedServicePartition` indica que o solucionador precisa tente novamente em vez de simplesmente recuperar um endereço em cache.

Normalmente, o código do cliente não precisa trabalhar com o `ServicePartitionResolver` diretamente. Ele é criado e passado para fábricas de cliente de comunicação da API de serviços confiável. As fábricas usam o solucionador internamente para gerar um objeto de cliente que pode ser usado para se comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Fábricas e clientes de comunicação

A biblioteca de fábrica de comunicação implementa um padrão de repetição de manipulação de falhas típico que facilita tentando conexões com pontos de extremidade do serviço resolvido. A biblioteca de fábrica fornece o mecanismo de repetição enquanto você fornece os manipuladores de erro.

`ICommunicationClientFactory`Define a interface base implementada por uma fábrica de cliente de comunicação que produz os clientes que podem falar com um serviço de estrutura de serviço. A implementação do CommunicationClientFactory depende da pilha de comunicação usada pelo serviço do serviço tecidos onde o cliente deseja se comunicar. A API de serviços confiável fornece uma `CommunicationClientFactoryBase<TCommunicationClient>`. Isso fornece uma implementação base do `ICommunicationClientFactory` interface e executa tarefas que são comuns a todas as pilhas de comunicação. (Essas tarefas incluem usando um `ServicePartitionResolver` para determinar o ponto de extremidade do serviço). Os clientes geralmente implementar a classe de CommunicationClientFactoryBase abstrata para lidar com lógica específica a pilha de comunicação.

O cliente de comunicação apenas recebe um endereço e usa para conectar a um serviço. O cliente pode usar qualquer protocolo que desejar.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

O client factory é responsável principalmente para a criação de clientes de comunicação. Para clientes que não manter uma conexão persistente, como um cliente HTTP, fábrica só precisa criar e retornar o cliente. Outros protocolos que manter uma conexão persistente, como alguns protocolos binários, também devem ser validados pela fábrica para determinar se ou não a conexão precisa ser recriada.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Finalmente, um manipulador de exceção é reponsible para determinar a ação que será executada quando ocorre uma exceção. Exceções são categorizadas em **passível de repetição** e **passível de repetição não**. 

 - Exceções **não passível de repetição** simplesmente obtém novamente lançadas volta para o chamador. 
 - Exceções **Retriable** ainda mais são categorizadas em **temporárias** e **não transitório**.
  - Exceções **temporárias** são aquelas que podem ser recuperadas simplesmente sem resolver novamente o endereço de ponto de extremidade do serviço. Esses incluirá temporárias problemas de rede ou respostas de erros de serviço diferente daquelas que indicam que o endereço de ponto de extremidade do serviço não existe. 
  - Exceções de **transitório não** são aqueles que exigem o endereço do ponto de extremidade do serviço sejam resolvidos novamente. Isto inclui as exceções que indicam que o ponto de extremidade do serviço não pôde ser alcançado, indicando que o serviço foi movido para um nó diferente. 

O `TryHandleException` toma uma decisão sobre uma determinada exceção. Se ele **não sabe** o que decisões sobre uma exceção, ele deve retornar **false**. Se ele **reconhece** o que decisões para fazer, deve definir o resultado de acordo e retornar **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Juntando as peças
Com um `ICommunicationClient`, `ICommunicationClientFactory`, e `IExceptionHandler` criado em torno de um protocolo de comunicação, um `ServicePartitionClient` é delimita todos juntos e fornece o loop de resolução de endereço de partição de manipulação de falhas e serviço em torno desses componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Próximas etapas
 - Veja um exemplo de comunicação de HTTP entre serviços em um [projeto de amostra no GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chamadas de procedimento remoto com remota serviços confiáveis](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que usa OWIN nos serviços confiável](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicação WCF usando serviços confiáveis](service-fabric-reliable-services-communication-wcf.md)
