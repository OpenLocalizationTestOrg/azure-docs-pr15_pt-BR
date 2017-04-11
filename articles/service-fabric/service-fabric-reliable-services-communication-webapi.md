<properties
   pageTitle="Comunicação com a API Web do ASP.NET de serviço | Microsoft Azure"
   description="Saiba como implementar a comunicação de serviço passo a passo usando a API Web do ASP.NET com OWIN hospedagem interna da API de serviços confiável."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Introdução: serviços de Web API do serviço tecidos com OWIN hospedagem interna

Azure tecidos de serviço coloca a potência em suas mãos quando estiver decidindo como você deseja que seus serviços para se comunicar com usuários e uns com os outros. Este tutorial se concentra na implementação usando API Web do ASP.NET com Interface aberta da Web para .NET (OWIN) hospedagem interna no confiável API serviço estrutura de serviços de comunicação de serviço. Podemos falaremos profundamente de comunicação conectável API serviços confiáveis. Também usaremos API da Web em um exemplo passo a passo para mostrar a você como configurar um ouvinte de comunicação personalizados.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introdução à Web API em estrutura de serviço

API da Web do ASP.NET é uma estrutura popular e avançada para a criação de HTTP APIs na parte superior do .NET Framework. Se você não ainda estiver familiarizado com a estrutura, consulte [Introdução ao ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para saber mais.

Web API em estrutura de serviço é a mesma API Web do ASP.NET você conhece e adora. A diferença é como você *host* um aplicativo Web API. Você não estar usando os serviços de informações da Internet (IIS) da Microsoft. Para entender melhor a diferença, vamos dividi-la em duas partes:

 1. O aplicativo Web API (incluindo modelos e controladores)
 2. O host (o servidor web, geralmente IIS)

Um aplicativo Web API propriamente dito não altera. Ele não é diferente de aplicativos de Web API, que você pode ter escrito no passado e você poderá simplesmente passar a maior parte do seu código de aplicativo. Mas se você tiver sido hospedando no IIS, onde você hospedar o aplicativo pode ser um pouco diferente do que você está acostumado a. Antes de entrar para a parte de hospedagem, vamos começar com algo mais familiar: o aplicativo Web API.


## <a name="create-the-application"></a>Criar o aplicativo

Comece criando um novo aplicativo de serviço tecidos com um único serviço de estado no Visual Studio 2015:

![Criar um novo aplicativo de serviço tecidos](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Um modelo do Visual Studio para um serviço de estado usando Web API está disponível para você. Neste tutorial, vai criamos um projeto de Web API do zero que resulta em obtido se você selecionou este modelo.

Selecione um projeto de serviço sem estado em branco para aprender a criar um projeto de Web API do zero, ou você pode iniciar com o modelo de Web API de serviço sem estado e simplesmente acompanhar.  

![Criar um único serviço de estado](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

A primeira etapa é retirar em alguns pacotes NuGet para API da Web. O pacote que queremos usar é Microsoft.AspNet.WebApi.OwinSelfHost. Este pacote inclui todos os pacotes de Web API necessários e os pacotes de *host* . Isso será importante mais tarde.

![Criar Web API usando o NuGet Package Manager](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Depois que os pacotes tiverem sido instalados, você pode começar a construção da estrutura de projeto de Web API básica. Se você usou o API da Web, a estrutura do projeto deve ser bastante familiar. Comece adicionando um `Controllers` diretório e um controlador de valores simples:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Em seguida, adicione uma classe de inicialização no projeto root para registrar o roteamento, formatadores e qualquer outra configuração. Isso também é onde API Web se conecta ao *host*, que será revisto novamente mais tarde. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

É isso para a parte do aplicativo. Neste ponto, podemos configurou apenas o layout básico da projeto API da Web. Até agora, ela não deve aparência muito diferente a partir de projetos de API Web que você pode ter escrito no passado ou o modelo de API Web básico. A lógica de negócios vai para os modelos e controladores como de costume.

Agora, o que fazemos sobre hospedagem para que estamos realmente pode executá-lo?

## <a name="service-hosting"></a>Serviço de hospedagem

Em estrutura de serviço, o seu serviço é executado em um *processo de host de serviço*, um arquivo executável que executa o seu código de serviço. Quando você escreve um serviço usando a API de serviços confiável, o seu projeto de serviço compila apenas para um arquivo executável que registra seu tipo de serviço e executa o seu código. Isso é verdadeiro na maioria dos casos, quando você escreve um serviço em estrutura de serviço no .NET. Quando você abre Program.cs do projeto de serviço sem estado, você verá:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se que parece anormalmente o ponto de entrada para um aplicativo de console, que é porque ele é.

Mais detalhes sobre o processo de host de serviço e registro de serviço estão além do escopo deste artigo. Mas é importante saber agora *que o código do seu serviço é executado em seu próprio processo*.

## <a name="self-host-web-api-with-an-owin-host"></a>Auto hospedar Web API com um host OWIN

Considerando que o código do seu aplicativo de Web API estiver hospedado em seu próprio processo, como associá-lo até um servidor web? Digite [OWIN](http://owin.org/). OWIN é simplesmente um contrato entre aplicativos web do .NET e servidores web. Normalmente quando ASP.NET (até 5 MVC) é usado, o aplicativo da web está associado ao IIS por meio de System. Web. No entanto, API Web implementa OWIN, portanto você pode escrever um aplicativo web que é desassociado do servidor web que hospeda-lo. Por isso, você pode usar um servidor de web OWIN *hospedados internamente* que você pode iniciar no seu próprio processo. Isso se ajusta perfeitamente com o modelo de hospedagem de serviço tecidos que acabei de descrever.

Neste artigo, usaremos Katana como o host OWIN para o aplicativo Web API. Katana é uma implementação de host OWIN de código-fonte aberto criada em [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) e [HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)do Windows.

> [AZURE.NOTE] Para saber mais sobre Katana, vá para o [site de Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para uma rápida visão geral de como usar Katana hospedar internamente API da Web, consulte [Usar OWIN para Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurar o servidor da web

A API de serviços confiável fornece um ponto de entrada de comunicação onde você pode conectar pilhas de comunicação que permitem aos usuários e clientes conectar ao serviço:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

O servidor da web (e qualquer outra pilha de comunicação que você usar no futuro, como WebSocket) devem usar a interface de ICommunicationListener para integrar corretamente com o sistema. Os motivos para isso tornará mais evidente nas etapas a seguir.

Primeiro, crie uma classe chamada OwinCommunicationListener que implementa ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

A interface de ICommunicationListener fornece três métodos para gerenciar um ouvinte de comunicação para seu serviço:

 - *OpenAsync*. Comece a escutar solicitações.
 - *CloseAsync*. Parar de escutar solicitações, todas as solicitações em andamento e término desligar normalmente.
 - *Anular*. Cancelar tudo e parar imediatamente.

Para começar, adicione membros de classe privada para coisas que o ouvinte precisará funcionar. Esses serão inicializados por meio do construtor e usados mais tarde, quando você configura o ouvinte URL.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Implementar OpenAsync

Para configurar o servidor da web, você precisará de duas partes de informações:

 - *Prefixo de caminho de URL de um*. Embora seja opcional, é bom para você configurou isso agora para que você possa com segurança hospede vários serviços da web em seu aplicativo.
 - *Uma porta*.

Antes de você obtiver uma porta do servidor web, é importante entender que o serviço tecidos fornece uma camada de aplicativo que atua como um buffer entre seu aplicativo e o sistema operacional subjacente que é executado. Assim, serviço tecidos fornece uma maneira para configurar *pontos de extremidade* para seus serviços. Serviço tecidos garante que os pontos de extremidade estejam disponíveis para seu serviço para usar. Dessa forma, você não precisa configurá-los no ambiente de sistema operacional subjacente. Você pode facilmente hospedar seu aplicativo de serviço tecidos em diferentes ambientes sem precisar fazer alterações em seu aplicativo. (Por exemplo, você pode hospedar o mesmo aplicativo no Azure ou no seu próprio data center).

Defina um ponto de extremidade HTTP PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Esta etapa é importante porque o processo de host do serviço é executado sob credenciais restritas (serviço de rede no Windows). Isso significa que seu serviço não tenha acesso ao configurar um ponto de extremidade HTTP em sua própria. Usando a configuração de ponto de extremidade, serviço tecidos sabe configurar a lista de controle de acesso correta (ACL) para a URL que o serviço irá escutar. Estrutura de serviço também fornece um local padrão para configurar pontos de extremidade.


De volta ao OwinCommunicationListener.cs, você pode começar a implementação OpenAsync. Isso é onde você inicia o servidor web. Primeiro, obter as informações de ponto de extremidade e criar a URL que o serviço irá escutar. A URL serão diferente dependendo se o ouvinte é usado em um serviço de estado ou um serviço de estado. Para um serviço de estado, o ouvinte precisa criar um endereço exclusivo para cada réplica de serviço estado-ouve. Para serviços sem estado, o endereço pode ser muito mais simples. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Observe que "http://+" é usada aqui. Isso é para certificar-se de que o servidor web é listening em todos os endereços disponíveis, incluindo host local, FQDN e o IP de máquina.

A implementação de OpenAsync é um dos motivos mais importantes por que o servidor da web (ou qualquer pilha de comunicação) é implementada como um ICommunicationListener, em vez de apenas ativá-abre diretamente da `RunAsync()` no serviço. O valor de retorno de OpenAsync é o endereço que o servidor web está escutando. Quando esse endereço é retornado ao sistema, ele registra o endereço com o serviço. Serviço tecidos fornece uma API que permite que clientes e outros serviços pedir este endereço por nome do serviço. Isso é importante porque o endereço do serviço não é estático. Serviços são movidos em torno do cluster para fins de balanceamento e disponibilidade de recursos. Este é o mecanismo que permite aos clientes resolver o endereço de ouvinte para um serviço.

Com isso em mente, OpenAsync inicia o servidor web e retorna o endereço que seja, atendendo. Observe que ele ouve em "http://+", mas antes de OpenAsync retorna o endereço, o "+" é substituído com o IP ou o FQDN do nó ele está ativado. O endereço que será retornado por esse método é o que está registrado com o sistema. Também é o que os clientes e outros serviços ver quando eles pedirem para o endereço de um serviço. Para os clientes corretamente conectá-la, eles precisam de um IP ou FQDN real no endereço.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Observe que isto faz referência a classe de inicialização que foi passada para o OwinCommunicationListener no construtor. Esta instância de inicialização é usada pelo servidor web para inicializar o aplicativo Web API.

O `ServiceEventSource.Current.Message()` linha aparecerá na janela de eventos de diagnóstico mais tarde, quando você executar o aplicativo para confirmar que o servidor da web foi iniciado com êxito.

## <a name="implement-closeasync-and-abort"></a>Implemente CloseAsync e anular

Finalmente, implemente CloseAsync e anular para parar o servidor da web. O servidor da web pode ser interrompido eliminando a alça de servidor que foi criada durante OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Neste exemplo de implementação, CloseAsync e anular simplesmente interrompe o servidor da web. Você pode optar por executar um desligamento mais normalmente coordenado do servidor web em CloseAsync. Por exemplo, o desligamento poderia esperar solicitações em trânsito para ser concluída antes do retorno.

## <a name="start-the-web-server"></a>Iniciar o servidor web

Agora você está pronto para criar e retornar uma instância de OwinCommunicationListener para iniciar o servidor da web. De volta à classe de serviço (WebService.cs), substituir o `CreateServiceInstanceListeners()` método:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Isso é onde o *aplicativo* do Web API e o OWIN *host* finalmente se encontram. O host (OwinCommunicationListener) é fornecido como uma instância do *aplicativo* (Web API) por meio da classe de inicialização. Serviço tecidos, em seguida, gerencia o ciclo de vida. Esse mesmo padrão normalmente pode ser seguido com qualquer pilha de comunicação.

## <a name="put-it-all-together"></a>Reunir tudo

Neste exemplo, você não precisa fazer nada na `RunAsync()` método, para que essa substituição simplesmente pode ser removida.

A implementação do serviço final deve ser muito simple. Ele só precisa criar o ouvinte de comunicação:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

O completo `OwinCommunicationListener` classe:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Agora que você tem colocar todas as peças no lugar, seu projeto deve parecer com um aplicativo Web API típico com pontos de entrada confiável API de serviços e um host OWIN:


![Web API com pontos de entrada confiável API de serviços e host OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Executar e se conectar por meio de um navegador da web

Se você ainda não tiver feito isso, [Configure o seu ambiente de desenvolvimento](service-fabric-get-started.md).


Agora você pode criar e implantar o serviço. Pressione **F5** no Visual Studio para criar e implantar o aplicativo. Na janela de eventos de diagnóstico, você verá uma mensagem que indica que o servidor da web aberto no http://localhost:8281 /.


![Janela do Visual Studio eventos de diagnóstico](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Se a porta já foi aberta por outro processo em seu computador, você poderá ver um erro aqui. Isso indica que o ouvinte não pôde ser aberto. Se for esse o caso, tente usar uma porta diferente para a configuração de ponto de extremidade na ServiceManifest.xml.


Depois que o serviço está em execução, abra um navegador e navegue até [api/http://localhost:8281/valores](http://localhost:8281/api/values) para testá-lo.

## <a name="scale-it-out"></a>Dimensioná-lo

Dimensionamento aplicativos web sem estado geralmente significa adicionando mais máquinas e girando os web apps-las. Mecanismo de coordenação do serviço tecidos pode fazer isso por você sempre que novos nós são adicionados a um cluster. Quando você cria instâncias de um serviço independente, você pode especificar o número de instâncias que você deseja criar. Serviço tecidos colocará esse número de instâncias em nós do cluster. E torna-se de não criar mais de uma instância em qualquer um nó. Você também pode instruir o serviço tecidos sempre criar uma instância em cada nó especificando **-1** para a contagem de instância. Isso garante que sempre que você adiciona nós de dimensionar seu cluster, uma instância do seu serviço sem estado será criada em nós de novo. Esse valor é uma propriedade da instância do serviço, para que ela é definida quando você cria uma instância do serviço. Você pode fazer isso através do PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Você também pode fazer isso quando você define um serviço padrão em um projeto do Visual Studio sem estado de serviço:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para obter mais informações sobre como criar o aplicativo e instâncias de serviço, consulte [implantar um aplicativo](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Próximas etapas

[Depurar seu aplicativo de serviço tecidos usando Visual Studio](service-fabric-debugging-your-application.md)
