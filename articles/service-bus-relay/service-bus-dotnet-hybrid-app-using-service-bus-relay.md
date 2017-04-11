<properties
    pageTitle="Aplicativo de no local/nuvem híbrido (.NET) | Microsoft Azure"
    description="Aprenda a criar um aplicativo de no local/nuvem híbrida do .NET usando a retransmissão de barramento de serviço do Azure."
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
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Aplicativo de no local/nuvem híbrida .NET usando retransmissão de barramento de serviço do Azure

## <a name="introduction"></a>Introdução

Este artigo descreve como criar um aplicativo em nuvem híbrida com o Microsoft Azure e Visual Studio. O tutorial supõe que você tenha sem experiência anterior usando o Azure. Em menos de 30 minutos, você terá um aplicativo que usa vários recursos Azure para cima e em execução na nuvem.

Você aprenderá:

-   Como criar ou adaptar um serviço web existente para consumo por uma solução da web.
-   Como usar o serviço de retransmissão de barramento de serviço do Azure para compartilhar dados entre um aplicativo do Azure e um serviço da web hospedado em outro lugar.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Como a retransmissão de barramento de serviço ajuda com as soluções de híbrido

Soluções de negócios são geralmente compostas de uma combinação de código personalizado escrito para lidar com necessidades de negócios novas e exclusivas e funcionalidade existente fornecida pelo soluções e sistemas que já estejam no lugar.

Arquitetos de solução estão começando a usar na nuvem para fácil manipulação de requisitos de escala e custos operacionais. Isso, elas encontrar que gostaria de aproveitar como blocos de construção para suas soluções estão dentro do firewall corporativo e sair fáceis de ativos de serviço existentes alcancem para acessar a solução de nuvem. Muitos serviços internos não são criados ou hospedados de forma que elas possam ser facilmente expostas na borda da rede corporativa.

A retransmissão de barramento de serviço foi projetada para o caso de uso de demorando existente serviços web do Windows Communication Foundation (WCF) e tornar desses serviços com segurança acessíveis para soluções que residem fora do perímetro corporativo sem mudanças intrusivas na infraestrutura de rede corporativa. Esses serviços de retransmissão de barramento de serviço ainda estão hospedados dentro de seu ambiente existente, mas eles delegarem aguardando entrada sessões e as solicitações para o barramento de serviço hospedado na nuvem. Barramento de serviço também protege desses serviços contra acesso não autorizado usando a autenticação de [Assinatura de acesso compartilhado](../service-bus-messaging/service-bus-sas-overview.md) (SAS).

## <a name="solution-scenario"></a>Cenário de solução

Neste tutorial, você irá criar um site da Web ASP.NET que permite que você veja uma lista de produtos na página de inventário de produto.

![][0]

O tutorial supõe que você tiver informações de produto em um sistema local existente e usa a retransmissão de barramento de serviço para chegar no sistema. Isso é simulado por um serviço web que é executado em um aplicativo de console simples e é feito por um conjunto de memória de produtos. Você poderá executar esse aplicativo de console em seu próprio computador e implantar a função web no Azure. Fazendo isso, você verá como a função de web em execução no Azure data center realmente chamará seu computador, mesmo que seu computador certamente residirão atrás de pelo menos um firewall e uma camada de conversão (NAT) do endereço de rede.

A seguir é uma captura de tela da página inicial do aplicativo da web concluída.

![][1]

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar a desenvolver aplicativos do Azure, obtenha as ferramentas e configure seu ambiente de desenvolvimento.

1.  Instale o SDK do Azure para .NET na página [obter ferramentas e SDK][] .

2.  Clique em **instalar o SDK** para a versão do Visual Studio que você está usando. As etapas deste tutorial usam Visual Studio 2015.

4.  Quando solicitado a executar ou salvar o instalador, clique em **Executar**.

5.  No **Web Platform Installer**, clique em **instalar** e prossiga com a instalação.

6.  Quando a instalação estiver concluída, você terá tudo o que é necessário para começar a desenvolver o aplicativo. O SDK inclui ferramentas que permitem que você facilmente desenvolver aplicativos do Azure no Visual Studio. Se você não tiver o Visual Studio instalado, o SDK também instala o livre Visual Studio Express.

## <a name="create-a-namespace"></a>Criar um namespace

Para começar a usar os recursos de barramento de serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor local

Primeiro, você irá criar um sistema de catálogo de produtos de local (simulação). Ele será bastante simple; Você pode ver isto como que representa um sistema de catálogo de produtos local real com uma superfície de serviço completo que estamos tentando integrar.

Este projeto é um aplicativo de console do Visual Studio e usa o [pacote do NuGet de barramento de serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas de barramento de serviço e configurações.

### <a name="create-the-project"></a>Criar o projeto

1.  Usando privilégios de administrador, inicie o Microsoft Visual Studio. Para iniciar o Visual Studio com privilégios de administrador, clique com botão direito no ícone do programa **Visual Studio** e clique em **Executar como administrador**.

2.  No Visual Studio, no menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.

3.  Em **Modelos instalados**, **Visual c#**, clique em **Aplicativo de Console**. Na caixa **nome** , digite o nome **ProductsServer**:

    ![][11]

4.  Clique em **Okey** para criar o projeto **ProductsServer** .

7.  Se você já tiver instalado o NuGet package manager para Visual Studio, pule para a próxima etapa. Caso contrário, visite [NuGet][] e clique em [Instalar NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os prompts para instalar o NuGet package manager e, em seguida, inicie novamente o Visual Studio.

7.  No Solution Explorer, clique com botão direito no projeto **ProductsServer** , clique em **Gerenciar pacotes do NuGet**.

8.  Clique na guia **Procurar** , procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de uso.

    ![][13]

    Observe que os conjuntos de cliente necessário agora referenciados.

9.  Adicione uma nova classe para seu contrato de produto. No Solution Explorer, clique com botão direito no projeto **ProductsServer** e clique em **Adicionar**e clique em **classe**.

10. Na caixa **nome** , digite o nome **ProductsContract.cs**. Clique em **Adicionar**.

11. No **ProductsContract.cs**, substitua a definição do namespace com o seguinte código, que define o contrato do serviço.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. No Program.cs, substitua a definição do namespace com o seguinte código, que adiciona o serviço de perfil e o host para ele.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. No Solution Explorer, clique duas vezes no arquivo de **App** para abri-lo no editor do Visual Studio. Na parte inferior da ** &lt;sistema. ServiceModel&gt; ** elemento (mas ainda dentro &lt;sistema. ServiceModel&gt;), adicione o seguinte código XML. Certifique-se de substituir *yourServiceNamespace* com o nome do seu namespace e *yourKey* com a chave SAS que você recuperou anteriormente a partir do portal:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Ainda na App, no ** &lt;appSettings&gt; ** elemento, substituir a conexão valor sequência de caracteres com a cadeia de conexão obtido anteriormente a partir do portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Pressione **Ctrl + Shift + B** ou no menu **Build** , clique em **Criar solução** para criar o aplicativo e verificar a precisão do seu trabalho até o momento.

## <a name="create-an-aspnet-application"></a>Criar um aplicativo ASP.NET

Nesta seção, você criará um aplicativo ASP.NET simples que exibe dados recuperados a partir do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projeto

1.  Certifique-se de que o Visual Studio está sendo executado com privilégios de administrador.

2.  No Visual Studio, no menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.

3.  Em **Modelos instalados**, **Visual c#**, clique em **Aplicativo Web ASP.NET**. Nome do projeto **ProductsPortal**. Clique em **Okey**.

    ![][15]

4.  Na lista **Selecionar um modelo** , clique em **MVC**. 

6.  Marque a caixa do **Host na nuvem**.

    ![][16]

5. Clique no botão de **Autenticação de alteração** . Na caixa de diálogo **Alterar autenticação** , clique em **Sem autenticação**e clique em **Okey**. Para este tutorial, você está implantando um aplicativo que não precisa de um logon do usuário.

    ![][18]

6.  Na seção **Microsoft Azure** da caixa de diálogo **Novo projeto ASP.NET** , certifique-se de que **hospeda na nuvem** está selecionada e se o **Serviço de aplicativo** está selecionado na lista suspensa.

    ![][19]

7. Clique em **Okey**. 

8. Agora você deve configurar recursos Azure para um novo aplicativo web. Siga todas as etapas na seção [Configurar Azure recursos para um novo aplicativo web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Em seguida, retorne neste tutorial e vá para a próxima etapa.

5.  No Solution Explorer, clique com botão direito **modelos** e, em seguida, clique em **Adicionar**, clique em **classe**. Na caixa **nome** , digite o nome **Product.cs**. Clique em **Adicionar**.

    ![][17]

### <a name="modify-the-web-application"></a>Modificar o aplicativo web

1.  No arquivo Product.cs no Visual Studio, substitua a definição de namespace existente com o seguinte código.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  No Solution Explorer, expanda a pasta de **controladores** , clique duas vezes no arquivo **HomeController.cs** para abri-lo no Visual Studio.

3. No **HomeController.cs**, substitua a definição de namespace existente com o seguinte código.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  No Solution Explorer, expanda a pasta Views\Shared, clique duas vezes **cshtml** para abri-lo no editor do Visual Studio.

5.  Altere todas as ocorrências do **Meu aplicativo do ASP.NET** para **produtos do LITWARE**.

6. Remova os links de **página inicial**, **sobre**e **contato** . No exemplo a seguir, exclua o código realçado.

    ![][41]

7.  No Solution Explorer, expanda a pasta Views\Home, clique duas vezes **cshtml** para abri-lo no editor do Visual Studio.
    Substitua todo o conteúdo do arquivo com o seguinte código.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Para verificar a precisão do seu trabalho até aqui, você pode pressionar **Ctrl + Shift + B** para construir o projeto.


### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo para verificar se ele funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. Clique com botão direito no nome do projeto no Solution Explorer e selecione **Definir como projeto de inicialização**.
2.  No Visual Studio, pressione F5.
3.  Seu aplicativo deverá aparecer, executado em um navegador.

    ![][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

A próxima etapa é ligar o servidor de produtos do local com o aplicativo ASP.NET.

1.  Se ainda não estiver aberto, no Visual Studio reabra o projeto de **ProductsPortal** que você criou na seção [criar um aplicativo ASP.NET](#create-an-aspnet-application) .

2.  Semelhante à etapa na seção "Criar um servidor local ativado", adicione o pacote do NuGet as referências de projeto. No Solution Explorer, clique com botão direito no projeto **ProductsPortal** , clique em **Gerenciar pacotes do NuGet**.

3.  Pesquise "Barramento de serviço" e selecione o item de **Barramento de serviço do Microsoft Azure** . Em seguida, concluir a instalação e feche esta caixa de diálogo.

4.  No Solution Explorer, clique com botão direito no projeto **ProductsPortal** , clique em **Adicionar**, em seguida, o **Item existente**.

5.  Navegue até o arquivo de **ProductsContract.cs** do projeto console **ProductsServer** . Clique para realçar ProductsContract.cs. Clique na seta para baixo ao lado de **Adicionar**e clique em **Adicionar como Link**.

    ![][24]

6.  Agora, abra o arquivo **HomeController.cs** no editor do Visual Studio e substitua a definição do namespace com o seguinte código. Certifique-se de substituir *yourServiceNamespace* com o nome do seu serviço namespace e *yourKey* sua chave SAS. Isso permitirá que o cliente para chamar o serviço no local, retornando o resultado da chamada.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  No Solution Explorer, clique com botão direito a solução de **ProductsPortal** (Verifique se a solução, não o projeto de atalho). Clique em **Adicionar**, clique em **Projeto existente**.

8.  Navegue para o projeto **ProductsServer** , clique duas vezes no arquivo de solução de **ProductsServer.csproj** para adicioná-lo.

9.  **ProductsServer** deve executar para exibir os dados em **ProductsPortal**. No Solution Explorer, clique com botão direito a solução **ProductsPortal** e clique em **Propriedades**. A caixa de diálogo de **Páginas de propriedade** é exibida.

10. No lado esquerdo, clique em **Projeto de inicialização**. No lado direito, clique em **vários projetos de inicialização**. Certifique-se de que **ProductsServer** e **ProductsPortal** são exibidas, nessa ordem, com **Iniciar** definido como a ação para ambos.

      ![][25]

11. Ainda na caixa de diálogo **Propriedades** , clique em **Dependências de projeto** no lado esquerdo.

12. Na lista de **projetos** , clique em **ProductsServer**. Certifique-se de que **ProductsPortal** é **não** selecionada.

14. Na lista de **projetos** , clique em **ProductsPortal**. Certifique-se de que **ProductsServer** está selecionado. 

    ![][26]

15. Clique em **Okey** na caixa de diálogo **Páginas de propriedade** .

## <a name="run-the-project-locally"></a>Executar o projeto localmente

Para testar o aplicativo localmente, no Visual Studio, pressione **F5**. O servidor local (**ProductsServer**) deve começar primeiro e, em seguida, o aplicativo **ProductsPortal** deve começar em uma janela do navegador. Desta vez, você verá que o inventário de produto lista dados recuperados do sistema de local do serviço de produto.

![][10]

Pressione **Atualizar** a página **ProductsPortal** . Sempre que você atualiza a página, verá o aplicativo de servidor exibir uma mensagem quando `GetProducts()` de **ProductsServer** é chamado.

Feche os dois aplicativos antes de prosseguir para a próxima etapa.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implantar o projeto ProductsPortal um aplicativo web do Azure

A próxima etapa é converter a frontend **ProductsPortal** em um aplicativo web Azure. Primeiro, implante o projeto **ProductsPortal** , seguindo todas as etapas na seção [implantar o projeto da web para o Azure web app](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Após a conclusão da implantação, retorne neste tutorial e vá para a próxima etapa.

> [AZURE.NOTE] Você poderá ver uma mensagem de erro na janela do navegador quando o projeto de web **ProductsPortal** é iniciado automaticamente após a implantação. Isso é esperado e ocorre porque o aplicativo de **ProductsServer** não está em execução ainda.

Copie a URL do aplicativo web implantado, pois você precisará a URL na próxima etapa. Você também pode obter essa URL da janela de atividade de serviço de aplicativo do Azure no Visual Studio:

![][9] 

### <a name="set-productsportal-as-web-app"></a>Definir ProductsPortal como web app

Antes de executar o aplicativo na nuvem, você deve garantir que **ProductsPortal** é iniciado de dentro do Visual Studio como um aplicativo web.

1. No Visual Studio, clique com botão direito no projeto **ProjectsPortal** e clique em **Propriedades**.

3. Na coluna à esquerda, clique em **Web**.

5. Na seção **Iniciar ação** , clique no botão **Iniciar URL** e, na caixa de texto, digite a URL para o aplicativo web implantado anteriormente; Por exemplo, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. No menu **arquivo** no Visual Studio, clique em **Salvar tudo**.

7. No menu Build no Visual Studio, clique em **Recriar solução**.

## <a name="run-the-application"></a>Executar o aplicativo

2.  Pressione F5 para criar e executar o aplicativo. O servidor local (o aplicativo de console **ProductsServer** ) deve começar primeiro e, em seguida, o aplicativo de **ProductsPortal** deve começar em uma janela do navegador, conforme mostrado na captura de tela a seguir. Observe novamente que o inventário do produto lista dados recuperados do sistema de local do serviço de produto e exibe esses dados no aplicativo da web. Verifique a URL para certificar-se de que **ProductsPortal** está em execução na nuvem, como um aplicativo web Azure. 

    ![][1]

    > [AZURE.IMPORTANT] O aplicativo de console **ProductsServer** deve ser capaz de atender aos dados para o aplicativo de **ProductsPortal** e em execução. Se o navegador exibirá um erro, aguarde alguns segundos mais para **ProductsServer** carregar e exibir a seguinte mensagem. Pressione **Atualizar** no navegador.

    ![][37]

3. Voltar no navegador, pressione **Atualizar** na página **ProductsPortal** . Sempre que você atualiza a página, verá o aplicativo de servidor exibir uma mensagem quando `GetProducts()` de **ProductsServer** é chamado.

    ![][38]

## <a name="next-steps"></a>Próximas etapas  

Para saber mais sobre barramento de serviço, consulte os seguintes recursos:  

* [Barramento de serviço Azure][sbwacom]  
* [Como usar filas de barramento de serviço][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenha ferramentas e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

