<properties
    pageTitle="Usando tutorial do restante do barramento de serviço enviadas mensagens | Microsoft Azure"
    description="Crie um aplicativo de host de retransmissão barramento de serviço simples que expõe uma interface baseada em REST."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Tutorial do resto de retransmissão de barramento de serviço

Este tutorial descreve como criar um aplicativo de host de barramento de serviço simples que expõe uma interface baseada em REST. RESTO permite que um cliente da web, como um navegador da web acessar as APIs de barramento de serviço por meio de solicitações HTTP.

Neste tutorial usa o restante do Windows Communication Foundation (WCF) modelo de programação para construir um serviço REST no barramento de serviço. Para obter mais informações, consulte o [Modelo de programação do WCF restante](https://msdn.microsoft.com/library/bb412169.aspx) e [projetar e implementar serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

## <a name="step-1-create-a-service-namespace"></a>Etapa 1: Criar um namespace de serviço

A primeira etapa é para criar um namespace e para obter uma chave de assinatura de acesso compartilhado (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto barramento de serviço. Uma chave SAS é gerada automaticamente pelo sistema quando um namespace de serviço é criado. A combinação do namespace do serviço e a chave SAS fornece as credenciais para o barramento de serviço autenticar o acesso a um aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Etapa 2: Definir um contrato de serviço WCF baseados em REST para usar com o barramento de serviço

Como com outros serviços de barramento de serviço, quando você cria um serviço de estilo REST, você deve definir o contrato. O contrato especifica que operações suporta o host. Uma operação de serviço pode ser considerada como um método de serviço web. Contratos são criados definindo uma interface C++, c# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específico. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve ser aplicado a cada interface e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve ser aplicado a cada operação. Se um método em uma interface que possui o [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), esse método não é exposto. O código usado para essas tarefas é mostrado no exemplo a seguir o procedimento.

A principal diferença entre um contrato de serviço barramento básico e um contrato de estilo REST é a adição de uma propriedade [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Essa propriedade permite mapear um método na sua interface para um método no outro lado da interface. Nesse caso, usaremos [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) para vincular um método GET HTTP. Isso permite barramento de serviço recuperar e interpretar comandos enviados para a interface com precisão.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para criar um contrato de serviço barramento com uma interface

1. Abra o Visual Studio como administrador: clique com botão direito o programa no menu **Iniciar** e clique em **Executar como administrador**.

2. Crie um novo projeto de aplicativo de console. Clique no menu **arquivo** , selecione **novo**e selecione o **projeto**. Na caixa de diálogo **Novo projeto** , clique em **Visual c#**, selecione o modelo de **Aplicativo de Console** e nomeie- **ImageListener**. Use o **local**padrão. Clique em **Okey** para criar o projeto.

3. Para um projeto c#, Visual Studio cria um `Program.cs` arquivo. Essa classe contém um vazio `Main()` método, necessário para um projeto de aplicativo de console construir corretamente.

4. Adicione referências para barramento de serviço e **System.ServiceModel.dll** ao projeto instalando o pacote de serviço barramento NuGet. Este pacote adiciona automaticamente referências para as bibliotecas de barramento de serviço, bem como o WCF **ServiceModel**. No Solution Explorer, clique com botão direito no projeto **ImageListener** e clique em **Gerenciar pacotes do NuGet**. Clique na guia **Procurar** , procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de uso.

5. Você deve adicionar explicitamente uma referência a **System** ao projeto:

    a. No Solution Explorer, clique com botão direito na pasta de **referências** na pasta do projeto e clique em **Adicionar referência**.

    b. Na caixa de diálogo **Adicionar referência** , clique na guia de **Framework** no lado esquerdo e na caixa de **pesquisa** , digite **System.ServiceModel.Web**. Selecione a caixa de seleção **System.ServiceModel.Web** , clique em **Okey**.

6. Adicione o seguinte `using` instruções na parte superior do arquivo Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o namespace que permite programação acesso aos recursos básicos do WCF. Barramento de serviço usa muitos dos objetos e atributos do WCF para definir contratos de serviço. Você usará esse namespace na maioria dos seus aplicativos de retransmissão barramento de serviço. Da mesma forma, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) ajuda a definir o canal, que é o objeto através do qual você se comunica barramento de serviço e o navegador da web de cliente. Finalmente, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contém os tipos que permitem criar aplicativos baseados na web.

7. Renomear a `ImageListener` namespace para **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Diretamente após a chave de abertura da declaração do namespace, definir uma nova interface denominada **IImageContract** e aplique o atributo **ServiceContractAttribute** para a interface com um valor de `http://samples.microsoft.com/ServiceModel/Relay/`. O valor de namespace difere do namespace que você usa em todo o escopo do seu código. O valor de namespace é usado como um identificador exclusivo para este contrato e deve ter informações de versão. Para obter mais informações, consulte [Controle de versão de serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Especifica o namespace explicitamente impede que o valor de namespace padrão sendo adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dentro o `IImageContract` interface, declarar um método para a operação única a `IImageContract` contrato expõe na interface e aplicar o `OperationContractAttribute` atributo para o método que você deseja expor como parte do contrato de serviço barramento público.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. No atributo **OperationContract** , adicione o valor de **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Isso permite barramento de serviço para rotear solicitações HTTP GET para `GetImage`e converter os valores de retorno `GetImage` em uma resposta de HTTP GETRESPONSE. Mais tarde no tutorial, você usará um navegador da web para acessar esse método e para exibir a imagem no navegador.

11. Logo após o `IImageContract` definição, declarar um canal que herda de ambas as `IImageContract` e `IClientChannel` interfaces.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Um canal é o objeto WCF através do qual o serviço e o cliente passam informações entre si. Posteriormente, você irá criar o canal em seu aplicativo de host. Barramento de serviço usa esse canal para passar as solicitações GET HTTP do navegador para sua implementação de **GetImage** . Barramento de serviço também usa o canal para fazer o valor de retorno **GetImage** e converterá em um GETRESPONSE de HTTP para o navegador do cliente.

12. No menu **Build** , clique em **Criar solução** para confirmar a precisão do seu trabalho até o momento.

### <a name="example"></a>Exemplo

O código a seguir mostra uma interface básica que define um contrato de serviço barramento.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Etapa 3: Implementar um contrato de serviço WCF baseados em REST usar barramento de serviço

Criando um serviço de barramento de serviço do estilo REST requer que você primeiro criar o contrato, que é definido usando uma interface. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe denominada **ImageService** que implementa a interface de **IImageContract** definidas pelo usuário. Após implementar o contrato, você configurar a interface usando um arquivo App. O arquivo de configuração contém informações necessárias para o aplicativo, como o nome do serviço e o nome do contrato e o tipo de protocolo que é usado para se comunicar com barramento de serviço. O código usado para essas tarefas é fornecido no exemplo a seguir o procedimento.

Como com as etapas anteriores, há pouca diferença entre a implementação de um contrato de estilo REST e um contrato de serviço barramento básico.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementar um contrato de barramento de serviço do estilo REST

1. Crie uma nova classe denominada **ImageService** diretamente após a definição da interface **IImageContract** . A classe **ImageService** implementa a interface **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Semelhante a outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, a implementação para este tutorial, aparece no mesmo arquivo como a definição de interface e `Main()` método.

2. Aplica o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à classe **IImageService** para indicar que a classe é uma implementação de um contrato do WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Conforme mencionado anteriormente, esse namespace não é um tradicional. Em vez disso, ele é parte da arquitetura do WCF que identifica o contrato. Para obter mais informações, consulte o tópico de [Nomes de contrato de dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação do WCF.

3. Adicione uma imagem de arquivo. jpg ao seu projeto.  

    Esta é uma imagem que exibe o serviço no Pesquisador de recebimento. Clique com botão direito seu projeto e, em seguida, clique em **Adicionar**. Clique em **Item existente**. Use a caixa de diálogo **Adicionar Item existente** para navegar até um arquivo. jpg apropriado e clique em **Adicionar**.

    Ao adicionar o arquivo, certifique-se de que **Todos os arquivos** está selecionada na lista suspensa ao lado do **nome de arquivo:** campo. O restante deste tutorial pressupõe que o nome da imagem é "Image. jpg". Se você tiver um arquivo diferente, será necessário renomear a imagem ou alterar seu código para compensar.

4. Para certificar-se de que a execução do serviço pode encontrar o arquivo de imagem, no **Solution Explorer** , clique com botão direito no arquivo de imagem e clique em **Propriedades**. No painel **Propriedades** , defina **Copiar para diretório de saída** para **Copiar se mais recente**.

5. Adicionar uma referência para o conjunto de **System.Drawing.dll** ao projeto e também adicione o seguinte associado `using` instruções.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Na classe **ImageService** , adicione o seguinte construtor que carrega o bitmap e se prepara para enviá-lo para o navegador do cliente.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Diretamente após o código anterior, adicione o seguinte método **GetImage** na classe **ImageService** para retornar uma mensagem HTTP que contém a imagem.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Essa implementação usa **MemoryStream** para recuperar a imagem e prepará-lo para streaming para o navegador. Inicia a posição do fluxo em zero, declara o conteúdo de fluxo como jpeg e fluxos as informações.

8. No menu **Build** , clique em **Criar solução**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração para executar o serviço da web em barramento de serviço

1. No **Solution Explorer**, clique duas vezes em **App** para abri-lo no editor do Visual Studio.

    O arquivo **App** é semelhante a um arquivo de configuração do WCF e inclui o nome do serviço, ponto de extremidade (ou seja, o local barramento de serviço expõe para clientes e hosts para se comunicar com os outros) e vinculação (o tipo de protocolo que é usado para se comunicar). A principal diferença aqui é que o ponto de extremidade do serviço configurado se refere a uma associação [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) , que não faz parte do .NET Framework.

2. O `<system.serviceModel>` XML é um elemento do WCF que define um ou mais serviços. Aqui, ele é usado para definir o nome do serviço e o ponto de extremidade. Na parte inferior da `<system.serviceModel>` elemento (mas ainda dentro `<system.serviceModel>`), adicione um `<bindings>` elemento que tem o seguinte conteúdo. Isso define as ligações usadas no aplicativo. Você pode definir várias ligações, mas para este tutorial, você está definindo a apenas um.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Esta etapa define uma associação de barramento de serviço [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) com **relayClientAuthenticationType** definido como **Nenhum**. Essa configuração indica que um ponto de extremidade usando essa ligação não exige uma credencial de cliente.

3. Após a `<bindings>` elemento, adicione uma `<services>` elemento. Semelhante às ligações, você pode definir vários serviços em um único arquivo de configuração. No entanto, para este tutorial, você define apenas uma.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Esta etapa configura um serviço que usa o padrão definido anteriormente **webHttpRelayBinding**. Ele também usa **sbTokenProvider**o padrão, que é definida na próxima etapa.

4. Após a `<services>` elemento, criar uma `<behaviors>` elemento com o seguinte conteúdo, substituindo "SAS_KEY" com a chave de *Assinatura de acesso compartilhado* (SAS) obtido anteriormente a partir do [portal do Azure][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Ainda na App, no `<appSettings>` elemento, substituir a conexão todo valor sequência de caracteres com a cadeia de conexão obtido anteriormente a partir do portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. No menu **Build** , clique em **Criar solução** para criar a solução inteira.

### <a name="example"></a>Exemplo

O código a seguir mostra a implementação de contrato e serviço para um serviço baseado em REST que é executado no barramento de serviço usando a ligação **WebHttpRelayBinding** .

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo a seguir mostra o arquivo App associado ao serviço.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Etapa 4: Hospedar o serviço WCF baseados em REST para usar barramento de serviço

Essa etapa descreve como executar um serviço da web usando um aplicativo de console em barramento de serviço. Uma listagem completa do código escrito nesta etapa é fornecida no exemplo a seguir o procedimento.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. No `Main()` declaração de função, crie uma variável para armazenar o namespace do seu projeto de barramento de serviço. Certifique-se de substituir `yourNamespace` com o nome do namespace de serviço que você criou anteriormente.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Barramento de serviço usa o nome de seu namespace para criar um URI exclusivo.

2. Criar um `Uri` instância para o endereço base do serviço baseado em namespace.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Criar e configurar o host serviço web

- Crie o host de serviço web, usando o endereço URI criado anteriormente nesta seção.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Host do serviço é o objeto do WCF que cria o aplicativo de host. Este exemplo passa a ela o tipo de host que você deseja criar (um **ImageService**) e também o endereço no qual você deseja expor o aplicativo de host.

### <a name="to-run-the-web-service-host"></a>Para executar o host de serviço web

1. Abra o serviço.

    ```
    host.Open();
    ```
    O serviço está sendo executado.

2. Exiba uma mensagem indicando que o serviço está sendo executado e como parar o serviço.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Quando terminar, feche o host de serviço.

    ```
    host.Close();
    ```

## <a name="example"></a>Exemplo

O exemplo a seguir inclui o contrato de serviço e a implementação de etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console. Compile o código a seguir em um arquivo executável denominado ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilar o código

Depois de criar a solução, faça o seguinte para executar o aplicativo:

1. Pressione **F5**ou navegue até o local do arquivo executável (ImageListener\bin\Debug\ImageListener.exe), para executar o serviço. Manter o aplicativo em execução, como ele é necessário para executar a próxima etapa.

2. Copie e cole o endereço do prompt de comando em um navegador para ver a imagem.

3. Quando terminar, pressione **Enter** na janela do prompt de comando para fechar o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Agora que você tenha criado um aplicativo que usa o serviço de retransmissão barramento de serviço, consulte os artigos a seguir para saber mais sobre retransmissão de mensagens:

- [Azure barramento de serviço visão geral da arquitetura](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Como usar o serviço de retransmissão de barramento](service-bus-dotnet-how-to-use-relay.md)

[Portal do Azure]: https://portal.azure.com