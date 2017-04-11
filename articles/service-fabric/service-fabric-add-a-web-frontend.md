<properties
   pageTitle="Criar um web front-end para seu aplicativo usando ASP.NET Core | Microsoft Azure"
   description="Expor seu aplicativo de serviço tecidos na Web usando um projeto de API da Web do ASP.NET Core e a comunicação entre serviço via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Construir um serviço front-end web para seu aplicativo usando Core do ASP.NET

Por padrão, os serviços de estrutura de serviço do Azure não fornecem uma interface pública na Web. Para expor a funcionalidade do seu aplicativo para clientes HTTP, você precisará criar um projeto da web para agir como um ponto de entrada e, em seguida, comunicar dali aos seus serviços individuais.

Neste tutorial, abordaremos saber onde estamos parou no tutorial [criar seu primeiro aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) e adicionar um serviço web na frente do serviço de contador com estado. Se você ainda não fez isso, você deve voltar e percorrer esse tutorial primeiro.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Adicionar um serviço de núcleo de ASP.NET ao seu aplicativo

Núcleo do ASP.NET é uma estrutura de desenvolvimento de web leve, entre plataformas que você pode usar para criar web moderna interface do usuário e APIs da web. Vamos adicionar um projeto de API Web do ASP.NET ao nosso aplicativo existente.

>[AZURE.NOTE] Para concluir este tutorial, você precisará [instalar .NET Core 1.0][dotnetcore-install].

1. No Solution Explorer, clique com botão direito **Serviços** dentro do projeto de aplicativo e escolha **Adicionar > novo serviço de tecidos**.

    ![Adicionando um novo serviço para um aplicativo existente][vs-add-new-service]

2. Na página **criar um serviço** , escolha **ASP.NET Core** e dê um nome.

    ![Escolher serviço web ASP.NET na caixa de diálogo Novo serviço][vs-new-service-dialog]

3. A próxima página fornece um conjunto de núcleo de ASP.NET modelos de projeto. Observe que esses são os mesmos modelos que você vê se você criou um projeto ASP.NET Core fora de um aplicativo de serviço tecidos. Para este tutorial, será escolhemos **API da Web**. No entanto, você pode aplicar os mesmos conceitos para criar um aplicativo web completo.

    ![Escolhendo o tipo de projeto do ASP.NET][vs-new-aspnet-project-dialog]

    Depois de criado o projeto de API da Web, você terá dois serviços em seu aplicativo. Ao prosseguir criar seu aplicativo, você irá adicionar mais serviços exatamente da mesma maneira. Cada pode ser independentemente versão e atualizado.

>[AZURE.TIP] Para saber mais sobre a criação ASP.NET Core services, consulte a [Documentação do ASP.NET principal](https://docs.asp.net).

## <a name="run-the-application"></a>Executar o aplicativo

Para obter uma sensação de que já fizemos, vamos implantar o novo aplicativo e dê uma olhada o comportamento padrão que o modelo de API da Web do ASP.NET Core fornece.

1. Pressione F5 no Visual Studio para depurar o aplicativo.

2. Quando implantação estiver concluída, o Visual Studio iniciará o navegador na raiz do serviço API Web do ASP.NET – algo como http://localhost:33003. O número da porta é atribuído aleatoriamente e pode ser diferente no seu computador. O modelo de API da Web do ASP.NET Core não oferece o comportamento padrão para a raiz, então você obterá um erro no navegador.

3. Adicionar `/api/values` até o local no navegador. Isso irá chamar o `Get` método na ValuesController no modelo de API da Web. Ele retornará a resposta padrão que é fornecida pelo modelo – uma matriz JSON que contém duas cadeias de caracteres:

    ![Valores padrão retornados do modelo de API da Web do ASP.NET Core][browser-aspnet-template-values]

    Ao final do tutorial, podemos será substituíram esses valores padrão com o valor de contador mais recente do nosso serviço de estado.


## <a name="connect-the-services"></a>Conectar os serviços

Serviço tecidos fornece flexibilidade total em como você se comunica com serviços confiáveis. Dentro de um único aplicativo, você pode ter serviços acessíveis via TCP, outros serviços que são acessíveis por meio de uma API REST de HTTP e ainda outros serviços que são acessíveis via sockets da web. Para informações detalhadas sobre as opções disponíveis e as compensações envolvidas, veja [se comunicando com os serviços](service-fabric-connect-and-communicate-with-services.md). Neste tutorial, podemos será siga uma das abordagens mais simples e use o `ServiceProxy` / `ServiceRemotingListener` classes que são fornecidas no SDK.

No `ServiceProxy` abordagem (modelada em chamadas de procedimento remoto ou RPCs), você define uma interface para agir como o contrato público para o serviço. Em seguida, use interface para gerar uma classe proxy para interagir com o serviço.


### <a name="create-the-interface"></a>Criar a interface

Vamos começar criando a interface para agir como o contrato entre o serviço de estado e seus clientes, incluindo o projeto ASP.NET Core.

1. No Solution Explorer, clique com botão direito sua solução e escolha **Adicionar** > **Novo projeto**.

2. Escolha a entrada **Visual c#** no painel de navegação à esquerda e selecione o modelo de **Biblioteca de classes** . Certifique-se de que a versão .NET Framework é definida como **4.5.2**.

    ![Criando um projeto de interface de seu serviço de estado][vs-add-class-library-project]

3. Em ordem para uma interface ser usada por `ServiceProxy`, ele deve ser derivado da interface IO serviço. Esta interface está incluída em um dos pacotes de serviço tecidos NuGet. Para adicionar o pacote, clique com botão direito seu novo projeto de biblioteca de classe e escolha **Gerenciar pacotes do NuGet**.

4. Pesquise o pacote de **Microsoft.ServiceFabric.Services** e instalá-lo.

    ![Adicionando o pacote NuGet de serviços][vs-services-nuget-package]

5. Na biblioteca de classes, crie uma interface com um único método, `GetCountAsync`, e estender a interface do IO serviço.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implementar a interface em seu serviço de estado

Agora que definimos a interface, precisamos implementá-lo no serviço de estado.

1. No seu serviço estado, adicione uma referência para o projeto de biblioteca de classe que contém a interface.

    ![Adicionando uma referência para o projeto de biblioteca de classes no serviço de estado][vs-add-class-library-reference]

2. Localize a classe que herda `StatefulService`, tais como `MyStatefulService`, e o estenda para implementar o `ICounter` interface.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Agora, implementar o método único que está definido no `ICounter` interface, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Expor o serviço de estado usando um ouvinte do serviço remoto

Com o `ICounter` interface implementada, a etapa final ao ativar o serviço de estado ser chamada de outros serviços é abrir um canal de comunicação. Para serviços de estado, o serviço tecidos oferece um método substituível chamado `CreateServiceReplicaListeners`. Com esse método, você pode especificar um ou mais ouvintes de comunicação, com base no tipo de comunicação que você deseja habilitar o serviço.

>[AZURE.NOTE] O método equivalente para abrir um canal de comunicação para serviços sem estado é chamado `CreateServiceInstanceListeners`.

Nesse caso, estamos substituirá a existente `CreateServiceReplicaListeners` método e forneça uma instância de `ServiceRemotingListener`, que cria um ponto de extremidade RPC que é acessível de clientes através do `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Usar a classe ServiceProxy para interagir com o serviço

Nosso serviço estado agora está pronto para receber o tráfego de outros serviços. Portanto tudo o que permanece está adicionando o código para se comunicar com ele do serviço da web ASP.NET.

1. No seu projeto ASP.NET, adicione uma referência para a biblioteca de classe que contém o `ICounter` interface.

2. No menu **Build** , abra o **Gerenciador de configuração**. Você verá algo parecido com isto:

    ![Biblioteca de classes de mostrando de Gerenciador de configuração como AnyCPU][vs-configuration-manager]

    Observe que o projeto de biblioteca de classes, **MyStatefulService.Interface**, está configurado para construir para qualquer CPU. Para funcionar corretamente com o serviço tecidos, ele deve ser explicitamente direcionado x64. Clique na lista suspensa de plataforma e escolha **novo**e criar um x64 configuração plataforma.

    ![Criar nova plataforma de biblioteca de classe][vs-create-platform]

3. Adicione o pacote de Microsoft.ServiceFabric.Services ao projeto ASP.NET, exatamente como você fez para o projeto de biblioteca de classe anterior. Isso fornecerá a `ServiceProxy` classe.

4. Na pasta **controladores** , abra o `ValuesController` classe. Observe que o `Get` método atualmente apenas retornará uma matriz de cadeia de caracteres codificada de "valor1" e "valor2" – que corresponde ao que viu anteriormente no navegador. Substitua essa implementação com o seguinte código:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    A primeira linha de código é a chave. Para criar o proxy de ICounter para o serviço de estado, você precisa fornecer duas partes de informações: um ID de partição e o nome do serviço.

    Você pode usar a partição aos serviços de estado de escala por dividindo seu estado em diferentes níveis, com base em uma chave que você define, como uma ID de cliente ou CEP. Em nosso aplicativo simples, o serviço de estado tem apenas uma partição, para que a chave não faz diferença. Qualquer tecla que você fornecer conduzirá a mesma partição. Para saber mais sobre os serviços de partição, veja [como partição serviços confiáveis do serviço tecidos](service-fabric-concepts-partitioning.md).

    O nome do serviço é um URI da estrutura formulário: /&lt;application_name&gt;/&lt;service_name&gt;.

    Com essas duas partes de informações, serviço tecidos pode identificar exclusivamente máquina que solicitações devem ser enviadas para. O `ServiceProxy` classe também perfeitamente trata casos em falha de máquina que hospeda a partição de serviço com informações de estado e outra máquina deve ser promovida para tomar seu lugar. Essa abstração facilita a criação de código do cliente para lidar com outros serviços significativamente mais simples.

    Depois que temos o proxy, podemos simplesmente invocar o `GetCountAsync` método e retornar seu resultado.

5. Pressione F5 novamente para executar o aplicativo modificado. Como antes, Visual Studio iniciará automaticamente o navegador na raiz do projeto da web. Adicionar o caminho de "api/valores" e você verá o valor atual do contador retornado.

    ![O valor de estado contador exibido no navegador][browser-aspnet-counter-value]

    Atualize o navegador periodicamente para ver o valor do contador a atualização.


>[AZURE.WARNING] O servidor da web ASP.NET Core fornecido no modelo, conhecido como Kestrel, é [não suportadas atualmente para lidar com o tráfego de internet direto](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Cenários de produção, considere os pontos de extremidade do ASP.NET Core atrás de [Gerenciamento de API] de hospedagem[ api-management-landing-page] ou outro gateway voltado para a internet. Observe que o serviço tecidos não é suportado para implantação dentro do IIS.


## <a name="what-about-actors"></a>O que são atores?

Este tutorial voltadas adicionando um web front-end comunicadas com um serviço de estado. No entanto, você pode seguir um modelo muito semelhante ao falar com atores. Na verdade, é um pouco mais simples.

Quando você cria um projeto de ator, o Visual Studio gera automaticamente um projeto de interface para você. Você pode usar a interface para gerar um proxy de ator no projeto da web para se comunicar com o ator. O canal de comunicação é fornecido automaticamente. Então você não precisa fazer nada que equivale ao estabelecer um `ServiceRemotingListener` como feito para o serviço de estado neste tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Como funcionam os serviços da web em seu cluster local

Em geral, você pode implantar exatamente o mesmo aplicativo de serviço tecidos para um cluster de vários máquina implantado em seu cluster local e ter altamente certeza de que ele funcione conforme o esperado. Isso ocorre porque seu cluster local é simplesmente uma configuração de cinco nós que é recolhida para um único computador.

Quando se trata de serviços da web, no entanto, há um nuance chave. Quando seu cluster fica atrás um balanceador de carga, como faz no Azure, você deve garantir que seus serviços da web sejam implantados em cada máquina desde o balanceador de carga será simplesmente alternada tráfego entre as máquinas. Você pode fazer isso definindo o `InstanceCount` no serviço para o valor especial de "-1".

Por outro lado, quando você executa um serviço web localmente, você precisa garantir que apenas uma instância do serviço está em execução. Caso contrário, você será executado em conflitos de vários processos que são listening no mesmo caminho e porta. Como resultado, a contagem de instâncias de serviço web deve ser definida "1" para implantações locais.

Para saber como configurar valores diferentes para ambiente diferente, consulte [Gerenciando parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar um cluster no Azure para implantar seu aplicativo na nuvem](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre a comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md)
- [Saiba mais sobre partição serviços com informações de estado](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
