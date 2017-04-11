<properties
   pageTitle="Ajudar a comunicação segura para serviços no serviço tecidos | Microsoft Azure"
   description="Visão geral de como ajudar a comunicação segura para serviços confiáveis que estejam em execução em um cluster de estrutura de serviço do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Comunicação segura de ajuda para serviços em estrutura de serviço do Azure

Segurança é um dos aspectos mais importantes de comunicação. A estrutura do aplicativo de serviços confiáveis fornece algumas pilhas de comunicação pré-criadas e ferramentas que você pode usar para melhorar a segurança. Este artigo fala sobre como melhorar a segurança quando você estiver usando o remota de serviço e a pilha de comunicação do Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ajudar a proteger um serviço quando você estiver usando o remota de serviço

Usaremos existente [exemplo](service-fabric-reliable-services-communication-remoting.md) que explica como configurar a comunicação remota para serviços confiáveis. Para ajudar a proteger um serviço quando você estiver usando o remota de serviço, siga estas etapas:

1. Criar uma interface, `IHelloWorldStateful`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto seu serviço. Seu serviço usará `FabricTransportServiceRemotingListener`, que é declarado na `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` namespace. Este é um `ICommunicationListener` implementação que fornece recursos de comunicação remota.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Adicione configurações de ouvinte e credenciais de segurança.

    Certifique-se de que o certificado que você deseja usar para ajudar a proteger sua comunicação de serviço é instalado em todos os nós no cluster. Há duas maneiras que você pode fornecer configurações de ouvinte e credenciais de segurança:

    1. Forneça diretamente no código de serviço:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Forneça usando um [pacote de configuração](service-fabric-application-model.md):

        Adicionar um `TransportSettings` seção no arquivo Settings. XML.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Nesse caso, o `CreateServiceReplicaListeners` método ficará assim:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Se você adicionar um `TransportSettings` seção no arquivo Settings. XML sem qualquer prefixo, `FabricTransportListenerSettings` carregará todas as configurações desta seção por padrão.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Nesse caso, o `CreateServiceReplicaListeners` método ficará assim:

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Quando você chama métodos em um serviço protegido usando a pilha de comunicação remota, em vez de usar o `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe para criar um proxy de serviço, use `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passar `FabricTransportSettings`, que contém `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o código do cliente é executado como parte de um serviço, você pode carregar `FabricTransportSettings` do arquivo Settings. XML. Crie uma seção de TransportSettings que seja parecida com o código de serviço, conforme mostrado anteriormente. Faça as seguintes alterações para o código do cliente:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não está executando como parte de um serviço, você pode criar um arquivo de client_name.settings.xml no mesmo local onde está o client_name.exe. Em seguida, crie uma seção de TransportSettings desse arquivo.

    Semelhante ao serviço, se você adicionar um `TransportSettings` seção sem um prefixo no cliente settings.xml/client_name.settings.xml, `FabricTransportSettings` carregará todas as configurações desta seção por padrão.

    Nesse caso, o código anterior é ainda mais simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Ajudar a proteger um serviço quando você estiver usando uma pilha de comunicação baseada em WCF

Usaremos existente [exemplo](service-fabric-reliable-services-communication-wcf.md) que explica como configurar uma pilha de comunicação baseada em WCF para serviços confiáveis. Para ajudar a proteger um serviço quando você estiver usando uma pilha de comunicação baseada em WCF, siga estas etapas:

1. Para o serviço, você precisa ajudar a proteger o ouvinte de comunicação WCF (`WcfCommunicationListener`) que você criar. Para fazer isso, modifique o `CreateServiceReplicaListeners` método.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. No cliente, o `WcfCommunicationClient` classe que foi criado no [exemplo](service-fabric-reliable-services-communication-wcf.md) anterior permanece inalterada. Mas você precisa substituir o `CreateClientAsync` método `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Use `SecureWcfCommunicationClientFactory` para criar um cliente de comunicação WCF (`WcfCommunicationClient`). Use o cliente para chamar métodos de serviço.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Próximas etapas

* [Web API com OWIN nos serviços confiáveis](service-fabric-reliable-services-communication-webapi.md)
