<properties
   pageTitle="Pilha de comunicação de serviços WCF confiável | Microsoft Azure"
   description="Pilha de comunicação WCF interna no serviço tecidos fornece comunicação de WCF do serviço de cliente para serviços confiáveis."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicação baseada em WCF para serviços confiáveis
A estrutura de serviços confiável permite aos autores de serviço escolher a pilha de comunicação que deseja usar para seus serviços. Eles podem plug-in na pilha de comunicação de sua escolha por meio de **ICommunicationListener** retornada dos métodos [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . O framework fornece uma implementação da pilha de comunicação com base em Windows Communication Foundation (WCF) para autores de serviço que deseja usar comunicação baseada em WCF.

## <a name="wcf-communication-listener"></a>Ouvinte de comunicação do WCF
A implementação de específicas do WCF de **ICommunicationListener** é fornecida pela classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** .

Fim de que dizer que temos um contrato de serviço do tipo`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um ouvinte de comunicação do WCF no serviço da seguinte maneira.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escrevendo clientes para a pilha de comunicação do WCF
Para escrever clientes para se comunicar com os serviços usando o WCF, a estrutura fornece **WcfClientCommunicationFactory**, que é a implementação específicas do WCF de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação WCF pode ser acessado da **WcfCommunicationClient** criado pela **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Código do cliente pode usar o **WcfCommunicationClientFactory** juntamente com o **WcfCommunicationClient** que implementa **ServicePartitionClient** para determinar o ponto de extremidade do serviço e se comunicar com o serviço.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] O padrão ServicePartitionResolver pressupõe que o cliente está executando no mesmo cluster que o serviço. Se isto é não o caso, crie um objeto de ServicePartitionResolver e passar os pontos de extremidade de conexão de cluster.

## <a name="next-steps"></a>Próximas etapas
* [Chamada de procedimento remoto com remota serviços confiáveis](service-fabric-reliable-services-communication-remoting.md)

* [Web API com OWIN nos serviços confiáveis](service-fabric-reliable-services-communication-webapi.md)

* [Protegendo a comunicação para serviços confiável](service-fabric-reliable-services-secure-communication.md)
