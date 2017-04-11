<properties 
    pageTitle="Tutorial de retransmissão de barramento de serviço | Microsoft Azure"
    description="Crie um cliente de barramento de serviço de aplicativo e serviço usando o serviço de retransmissão de barramento."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Tutorial de retransmissão de barramento de serviço

Este tutorial descreve como criar um aplicativo de cliente de barramento de serviço simple e o serviço usando os recursos de "retransmissão" barramento de serviço. Para um tutorial correspondente que usa barramento de serviço [intermediário de mensagens](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consulte o [Serviço barramento orientado mensagens .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Trabalho este tutorial fornece uma compreensão das etapas que são necessárias para criar um aplicativo de cliente e serviço de barramento de serviço. Como seus equivalentes WCF, um serviço é uma construção que expõe pontos de extremidade de um ou mais, cada um dos quais expõe uma ou mais operações de serviço. O ponto de extremidade de um serviço Especifica um endereço onde o serviço pode ser encontrado, uma ligação que contém as informações que um cliente deve se comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço aos seus clientes. A principal diferença entre um WCF e um serviço de barramento de serviço é que o ponto de extremidade é exposto na nuvem, em vez de localmente em seu computador.

Depois de você trabalhar com a sequência de tópicos neste tutorial, você terá um serviço em execução e um cliente que pode invocar as operações do serviço. O primeiro tópico descreve como configurar uma conta. As próximas etapas descrevem como definir um serviço que usa um contrato, como implementar o serviço e como configurar o serviço no código. Eles também descrevem como hospedar e executar o serviço. O serviço que é criado é hospedado automaticamente e o cliente e o serviço executados no mesmo computador. Você pode configurar o serviço usando o código ou um arquivo de configuração.

As três etapas finais descrevem como criar um aplicativo cliente, configure o aplicativo de cliente e criar e usam um cliente que pode acessar a funcionalidade do host.

Todos os tópicos nessa seção presumem que você está usando o Visual Studio como ambiente de desenvolvimento.

## <a name="sign-up-for-an-account"></a>Inscrever-se para uma conta

A primeira etapa é para criar um namespace e para obter uma chave de assinatura de acesso compartilhado (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto barramento de serviço. Uma chave SAS é gerada automaticamente pelo sistema quando um namespace de serviço é criado. A combinação do namespace do serviço e a chave SAS fornece as credenciais para o barramento de serviço autenticar o acesso a um aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definir um contrato de serviço WCF para usar com o barramento de serviço

O contrato de serviço Especifica quais operações (a terminologia de serviço Web para métodos ou funções) o serviço oferece suporte. Contratos são criados definindo uma interface C++, c# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específico. Cada interface deve ter o atributo de [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) aplicado a ele, e cada operação deve ter o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) aplicado a ele. Se um método em uma interface que possui o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , esse método não é exposto. O código para essas tarefas é fornecido no exemplo a seguir o procedimento. Para uma discussão maior de contratos e serviços, consulte [projetar e implementar serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para criar um contrato de serviço barramento com uma interface

1. Abra o Visual Studio como administrador clicando o programa no menu **Iniciar** e selecionando **Executar como administrador**.

2. Crie um novo projeto de aplicativo de console. Clique no menu **arquivo** , selecione **novo**e clique em **projeto**. Na caixa de diálogo **Novo projeto** , clique **Visual c#** (se o **Visual c#** não aparecer, procure em **Outros idiomas**). Clique no modelo de **Aplicativo de Console** e nomeie- **EchoService**. Clique em **Okey** para criar o projeto.

    ![][2]

3. Instale o pacote de serviço barramento NuGet. Este pacote adiciona automaticamente referências para as bibliotecas de barramento de serviço, bem como o WCF **ServiceModel**. [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o namespace que lhe permita acessar programaticamente os recursos básicos do WCF. Barramento de serviço usa muitos dos objetos e atributos do WCF para definir contratos de serviço.

    No Solution Explorer, clique com botão direito a solução e clique em **Gerenciar pacotes NuGet para solução**. Clique na guia **Procurar** , procure `Microsoft Azure Service Bus`. Verifique se o nome do projeto está selecionado na caixa de **versões** . Clique em **instalar**e aceite os termos de uso.

    ![][3]

3. No Solution Explorer, clique duas vezes o arquivo Program.cs para abri-lo no editor, se ele não ainda estiver aberto.

4. Adicione o seguinte usando instruções na parte superior do arquivo:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Altere o nome do namespace de seu nome padrão de **EchoService** para **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Neste tutorial usa o namespace c# **Microsoft.ServiceBus.Samples**, que é o namespace do tipo de contrato gerenciado que é usado no arquivo de configuração na etapa [Configurar o cliente do WCF](#configure-the-wcf-client) . Você pode especificar qualquer namespace desejado quando você cria este exemplo; No entanto, o tutorial não funcionarão, a menos que você modifique os namespaces do contrato e serviço da mesma forma, no arquivo de configuração do aplicativo. O namespace especificado no arquivo App deve ser a mesma que o namespace especificado em seus arquivos c#.

1. Logo após o `Microsoft.ServiceBus.Samples` declaração de namespace, mas dentro do namespace, definir uma nova interface denominada `IEchoContract` e aplicar o `ServiceContractAttribute` atributo para a interface com um valor de namespace de **http://samples.microsoft.com/ServiceModel/Relay/**. O valor de namespace difere do namespace que você usa em todo o escopo do seu código. Em vez disso, o valor de namespace é usado como um identificador exclusivo para este contrato. Especifica o namespace explicitamente impede que o valor de namespace padrão sendo adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Normalmente, o namespace de contrato de serviço contém um esquema de nomenclatura que inclui informações de versão. Incluindo informações de versão do namespace de contrato de serviço habilita serviços isolar principais alterações definindo um novo contrato de serviço com um novo namespace e expor-lo em um novo ponto de extremidade. Dessa maneira, clientes podem continuar a usar o contrato de serviço antigo sem precisar ser atualizados. Informações de versão podem consistir em uma data ou um número de compilação. Para obter mais informações, consulte [Controle de versão de serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Para os fins deste tutorial, o esquema de nomenclatura do namespace de contrato de serviço não contém informações de versão.

1. Dentro o `IEchoContract` interface, declarar um método para a operação única a `IEchoContract` contrato expõe na interface e aplicar o `OperationContractAttribute` atributo para o método que você deseja expor como parte do contrato de serviço barramento público.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Logo após o `IEchoContract` definição de interface, declare um canal que herda de ambos `IEchoContract` e também o `IClientChannel` interface, como mostrado aqui:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto WCF através do qual o cliente e host passam informações entre si. Posteriormente, você irá escrever código contra o canal para repetir informações entre os dois aplicativos.

1. No menu **Build** , clique em **Criar solução** ou pressione **Ctrl + Shift + B** para confirmar a precisão do seu trabalho até o momento.

### <a name="example"></a>Exemplo

O código a seguir mostra uma interface básica que define um contrato de serviço barramento.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface é criada, você pode implementar a interface.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementar o contrato WCF usar barramento de serviço

Criar uma retransmissão de barramento de serviço requer que você primeiro criar o contrato, que é definido usando uma interface. Para obter mais informações sobre como criar a interface do, consulte a etapa anterior. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe denominada `EchoService` que implementa definido pelo usuário `IEchoContract` interface. Após implementar a interface, você configurar a interface usando um arquivo de configuração de App. O arquivo de configuração contém informações necessárias para o aplicativo, como o nome do serviço e o nome do contrato e o tipo de protocolo que é usado para se comunicar com barramento de serviço. O código usado para essas tarefas é fornecido no exemplo a seguir o procedimento. Para uma discussão mais geral sobre como implementar um contrato de serviço, consulte [Implementando contratos de serviço](https://msdn.microsoft.com/library/ms733764.aspx) na documentação do WCF.

1. Criar uma nova classe denominada `EchoService` diretamente após a definição do `IEchoContract` interface. O `EchoService` classe implementa o `IEchoContract` interface. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Semelhante a outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação está localizada no mesmo arquivo como a definição de interface e o `Main` método.

1. Aplicar o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) para o `IEchoContract` interface. O atributo especifica o nome do serviço e namespace. Após fazer isso, a `EchoService` classe aparece da seguinte maneira:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementar o `Echo` método definido no `IEchoContract` interface no `EchoService` classe. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Clique em **criar**, clique em **Criar solução** para confirmar a precisão do seu trabalho.

### <a name="to-define-the-configuration-for-the-service-host"></a>Para definir a configuração para o host de serviço

1. O arquivo de configuração é muito semelhante a um arquivo de configuração do WCF. Ele inclui o nome do serviço, ponto de extremidade (ou seja, o local barramento de serviço expõe para clientes e hosts para se comunicar com os outros) e a ligação (o tipo de protocolo que é usado para se comunicar). A principal diferença é que esse ponto de extremidade do serviço configurado se refere a uma associação [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) , que não faz parte do .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) é uma das ligações de definido pelo barramento de serviço.

1. No **Solution Explorer**, clique duas vezes no arquivo de App para abri-lo no editor do Visual Studio.

2. No `<appSettings>` elemento, substitua os espaços reservados com o nome de seu espaço de nomes do serviço e as associações de segurança de chave que você copiou em uma etapa anterior. 

1. Dentro do `<system.serviceModel>` marcas, adicione uma `<services>` elemento. Você pode definir vários aplicativos de serviço barramento em um único arquivo de configuração. Entretanto, neste tutorial define apenas uma.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro do `<services>` elemento, adicione uma `<service>` elemento para definir o nome do serviço.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro do `<service>` elemento, definir o local do contrato de ponto de extremidade e também o tipo de vinculação para o ponto de extremidade.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto de extremidade define qual será a aparência de cliente para o aplicativo host. Posteriormente, o tutorial usa esta etapa para criar um URI que expõe totalmente host por meio do barramento de serviço. A vinculação declara que estamos usando TCP como o protocolo para se comunicar com barramento de serviço.

1. No menu **Build** , clique em **Criar solução** para confirmar a precisão do seu trabalho.

### <a name="example"></a>Exemplo

O código a seguir mostra a implementação do contrato de serviço.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código a seguir mostra o formato básico do arquivo App associado com o host de serviço.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Hospedar e execute um serviço Web básico para registrar com barramento de serviço

Essa etapa descreve como executar um serviço de barramento de serviço básico.

### <a name="to-create-the-service-bus-credentials"></a>Para criar as credenciais de barramento de serviço

1. Em `Main()`, criar duas variáveis na qual deseja armazenar o namespace e as associações de segurança de chave que são lidos da janela do console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A chave SAS será usada mais tarde para acessar o seu projeto de barramento de serviço. Namespace é passado como um parâmetro para `CreateServiceUri` para criar um serviço URI.

4. Usando um objeto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , declare que você usando uma chave SAS como o tipo de credencial. Adicione o seguinte código diretamente após o código adicionado na última etapa.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. Após o código que você adicionou na última etapa, crie um `Uri` instância para o endereço base do serviço. Esse URI especifica o esquema de barramento de serviço, o espaço para nome e o caminho da interface do serviço.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" é uma abreviação para o esquema de barramento de serviço e indica que estamos usando TCP como o protocolo. Isso também anteriormente foi indicado no arquivo de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) foi especificada como a associação.
    
    Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Criar e configurar o host de serviço

1. Definir o modo de conectividade para `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo que o serviço usa para se comunicar com barramento de serviço; HTTP ou TCP. Usando a configuração padrão `AutoDetect`, o serviço tenta se conectar ao serviço barramento sobre TCP se ele estiver disponível e HTTP se TCP não estiver disponível. Observe que isso é diferente do protocolo o serviço especifica para comunicação de cliente. Esse protocolo é determinado por associação usada. Por exemplo, um serviço pode usar a ligação [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) , que especifica que seu ponto de extremidade (exposto no barramento de serviço) se comunica com clientes sobre HTTP. Esse serviço mesmo poderia especificar **ConnectivityMode.AutoDetect** para que o serviço se comunica com barramento de serviço sobre TCP.

1. Crie o host do serviço, usando o URI criado anteriormente nesta seção.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Host do serviço é o objeto do WCF que cria o serviço. Aqui, você passar o tipo de serviço que você deseja criar (um `EchoService` tipo) e também para o endereço no qual você deseja expor o serviço.

1. Na parte superior do arquivo Program.cs, adicione referências a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. De volta ao `Main()`, configure o ponto de extremidade para habilitar o acesso público.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Esta etapa informa barramento de serviço que seu aplicativo pode ser encontrado publicamente examinando o serviço barramento ATOM feed para seu projeto. Se você definir **DiscoveryType** como **particular**, um cliente ainda poderá acessar o serviço. No entanto, o serviço não seria exibido quando ela Procura namespace barramento de serviço. Em vez disso, o cliente teria Saiba o caminho do ponto de extremidade com antecedência.

1. Aplica as credenciais de serviço para os pontos de extremidade do serviço definidos no arquivo App. config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Conforme mencionado na etapa anterior, você pode ter declarado vários serviços e pontos de extremidade no arquivo de configuração. Se você tivesse, este código seria percorrer o arquivo de configuração e procure por cada ponto de extremidade ao qual ele deverá se aplicar suas credenciais. No entanto, para este tutorial, o arquivo de configuração tem apenas um ponto de extremidade.

### <a name="to-open-the-service-host"></a>Para abrir o host de serviço

1. Abra o serviço.

    ```
    host.Open();
    ```

1. Informe o usuário que o serviço está em execução e explicam como desligar o serviço.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando terminar, feche o host de serviço.

    ```
    host.Close();
    ```

1. Pressione **Ctrl + Shift + B** para construir o projeto.

### <a name="example"></a>Exemplo

O exemplo a seguir inclui o contrato de serviço e a implementação de etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console. Compile o seguinte em um arquivo executável denominado EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Criar um cliente do WCF para o contrato de serviço

A próxima etapa é criar um aplicativo de cliente barramento de serviço básico e definir o contrato de serviço que você irá implementar em etapas posteriores. Observe que muitas dessas etapas se parecem com as etapas usadas para criar um serviço: definindo um contrato, editando uma App arquivo, utilizando as credenciais para se conectar ao barramento de serviço e assim por diante. O código usado para essas tarefas é fornecido no exemplo a seguir o procedimento.

1. Crie um novo projeto na solução Visual Studio atual para o cliente, fazendo o seguinte:
    1. No Solution Explorer, na mesma solução que contém o serviço, clique com botão direito a solução atual (não o projeto) e clique em **Adicionar**. Clique em **Novo projeto**.
    2. Na caixa de diálogo **Add New Project** , clique em **Visual c#** (se o **Visual c#** não aparecer, procure em **Outros idiomas**), selecione o modelo de **Aplicativo de Console** e chame- **EchoClient**.
    3. Clique em **Okey**.
<br />

1. No Solution Explorer, duas vezes no arquivo Program.cs o projeto **EchoClient** para abri-lo no editor de, se ele não ainda estiver aberto.

1. Alterar o nome do namespace de seu nome padrão de `EchoClient` para `Microsoft.ServiceBus.Samples`.

1. Instale o [pacote de serviço barramento NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). No Solution Explorer, clique com botão direito no projeto **EchoClient** e clique em **Gerenciar pacotes do NuGet**. Clique na guia **Procurar** , procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de uso.

    ![][3]

1. Adicionar uma `using` declaração do namespace [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no arquivo Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Adicione a definição de contrato de serviço ao namespace, conforme mostrado no exemplo a seguir. Observe que essa definição é idêntica à definição usada no projeto **serviço** . Você deve adicionar este código na parte superior da `Microsoft.ServiceBus.Samples` namespace.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Pressione **Ctrl + Shift + B** para criar o cliente.

### <a name="example"></a>Exemplo

O código a seguir mostra o status atual do arquivo Program.cs no projeto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar o cliente do WCF

Nesta etapa, você criar um arquivo de App para um aplicativo cliente básicas que acessa o serviço criado anteriormente neste tutorial. Esse arquivo App define o contrato, a ligação e o nome do ponto de extremidade. O código usado para essas tarefas é fornecido no exemplo a seguir o procedimento.

1. No Solution Explorer, no projeto **EchoClient** , clique duas vezes em **App** para abrir o arquivo no editor do Visual Studio.

2. No `<appSettings>` elemento, substitua os espaços reservados com o nome de seu espaço de nomes do serviço e as associações de segurança de chave que você copiou em uma etapa anterior.

1. Dentro do elemento de ServiceModel, adicione uma `<client>` elemento.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Esta etapa declara que você está definindo um aplicativo de cliente do WCF estilo.

1. Dentro do `client` elemento, defina o nome, contrato e tipo de vinculação para o ponto de extremidade.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Esta etapa define o nome do ponto de extremidade, o contrato definido no serviço e o fato de que o aplicativo cliente usa TCP para se comunicar com barramento de serviço. O nome do ponto de extremidade é usado na próxima etapa para vincular esta configuração de ponto de extremidade com o serviço de URI.

1. Clique em **arquivo**e em seguida **Salvar tudo**.

## <a name="example"></a>Exemplo

O código a seguir mostra o arquivo App para o cliente de eco.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementar o cliente do WCF para chamar barramento de serviço

Nesta etapa, você implementar um aplicativo cliente básicas que acessa o serviço que você criou anteriormente neste tutorial. Semelhante ao serviço, o cliente executa muitas das mesmas operações acessem barramento de serviço:

1. Define o modo de conectividade.
1. Cria o URI que localiza o serviço de host.
1. Define as credenciais de segurança.
1. Aplica as credenciais para a conexão.
1. Abre a conexão.
1. Executa as tarefas específicas do aplicativo.
1. Fecha a conexão.

No entanto, uma das principais diferenças é que o aplicativo cliente usa um canal para se conectar ao barramento de serviço, enquanto o serviço usa uma chamada a **ServiceHost**. O código usado para essas tarefas é fornecido no exemplo a seguir o procedimento.

### <a name="to-implement-a-client-application"></a>Implementar um aplicativo de cliente

1. Defina o modo de conectividade a **detecção automática**. Adicione o seguinte código dentro a `Main()` método do aplicativo **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Defina variáveis para armazenar os valores para o namespace do serviço e a chave SAS que são lidos no console do.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crie o URI que define o local do host no seu projeto de barramento de serviço.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crie o objeto de credencial para seu ponto de extremidade do serviço namespace.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crie a fábrica de canais que carrega a configuração descrita no arquivo App.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canal é um objeto do WCF que cria um canal através do qual se comunicar os aplicativos de serviço e cliente.

1. Aplica as credenciais de barramento de serviço.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crie e abra o canal para o serviço.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Gravar a interface do usuário básica e a funcionalidade para o eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Observe que o código usa a instância do objeto de canal como um proxy para o serviço.

1. Feche o canal e, em seguida, feche a fábrica.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Para executar os aplicativos

1. Pressione **Ctrl + Shift + B** para criar a solução. Isso cria o projeto de cliente e o projeto de serviço que você criou nas etapas anteriores.

2. Antes de executar o aplicativo cliente, você deve garantir que o aplicativo de serviço está em execução. No Explorador de solução no Visual Studio, a solução de **EchoService** de atalho, clique em **Propriedades**.

3. Na caixa de diálogo Propriedades da solução, clique em **Projeto de inicialização**, clique no botão de **vários projetos de inicialização** . Verifique se **que echoservice** aparece primeiro na lista. 

4. Configurar a caixa de **ação** para projetos **EchoService** tanto o **EchoClient** para **Iniciar**.

    ![][5]

5. Clique em **dependências do projeto**. Na caixa de **projetos** , selecione **EchoClient**. Na caixa **depende** , verifique se **que echoservice** está marcada.

    ![][6]

6. Clique em **Okey** para descartar a caixa de diálogo de **Propriedades** .

7. Pressione **F5** para executar os dois projetos.

8. Ambas as janelas de console abra e solicita o nome do namespace. O serviço deve ser executado primeiro, na janela do console **EchoService** , insira o namespace e pressione **Enter**.

9. Em seguida, você será solicitado para a sua chave SAS. Insira a chave SAS e pressione ENTER.

    Eis um exemplo de saída da janela do console. Observe que os valores fornecidos aqui são apenas por exemplo.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    Imprime o aplicativo de serviço para a janela de console o endereço na qual ele está atendendo, conforme visto no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Na janela do console **EchoClient** , insira as mesmas informações que você digitou anteriormente para o aplicativo de serviço. Siga as etapas anteriores para inserir o mesmo namespace de serviço e valores-chave SAS para o aplicativo cliente.

11. Depois de inserir esses valores, o cliente abre um canal para o serviço e solicita que você digite algum texto como visto no seguinte exemplo de saída do console.

    `Enter text to echo (or [Enter] to exit):` 

    Digite algum texto para enviar para o aplicativo de serviço e pressione Enter. Este texto é enviado para o serviço por meio da operação de serviço de eco e aparece na janela do console de serviço como a saída de exemplo a seguir.

    `Echoing: My sample text`

    O aplicativo cliente recebe o valor de retorno a `Echo` operação, que é o texto original e imprime sua janela de console. A seguir é exemplo de saída da janela do console do cliente.

    `Server echoed: My sample text`

12. Você pode continuar a enviar mensagens de texto do cliente para o serviço dessa maneira. Quando tiver terminado, pressione Enter nas janelas do console cliente e serviço para encerrar os dois aplicativos.

## <a name="example"></a>Exemplo

O exemplo a seguir mostra como criar um aplicativo cliente, como chamar as operações do serviço e como fechar o cliente após a chamada de operação é concluída.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrava como criar um cliente de barramento de serviço de aplicativo e serviço usando os recursos de "retransmissão" barramento de serviço. Para um tutorial semelhante que usa barramento de serviço de [mensagens](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consulte o [Serviço barramento orientado mensagens .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Para saber mais sobre o barramento de serviço, consulte os tópicos a seguir.

- [Visão geral de mensagens do barramento de serviço](../service-bus-messaging/service-bus-messaging-overview.md)
- [Fundamentos de barramento de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura de barramento de serviço](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
