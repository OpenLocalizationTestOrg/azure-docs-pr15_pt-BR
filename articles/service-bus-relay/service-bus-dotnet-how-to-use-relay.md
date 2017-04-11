<properties
    pageTitle="Como usar a retransmissão de barramento de serviço com .NET | Microsoft Azure"
    description="Saiba como usar o serviço de retransmissão barramento de serviço do Azure para conectar dois aplicativos hospedados em locais diferentes."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Como usar o serviço de retransmissão barramento de serviço do Azure

Este artigo descreve como usar o serviço de retransmissão barramento de serviço. Os exemplos são gravados em c# e usam a API do Windows Communication Foundation (WCF) com extensões contidas no conjunto barramento de serviço. Para obter mais informações sobre a retransmissão de barramento de serviço, consulte Visão geral do [sistema de mensagens de barramento de serviço enviadas](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>O que é a retransmissão de barramento de serviço?

O serviço [de barramento de serviço de *retransmissão* ](service-bus-relay-overview.md) permite que você crie aplicativos híbridos que executar em um data center Azure e seu próprio ambiente de empresa local. A retransmissão de barramento de serviço facilita isso habilitando com segurança expor os serviços do Windows Communication Foundation (WCF) que residem em uma rede corporativos para a nuvem pública, sem precisar abrir um conexão de firewall ou mudanças intrusivas em uma infraestrutura de rede corporativa.

![Conceitos de retransmissão](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

A retransmissão de barramento de serviço permite que você hospedar serviços do WCF em seu ambiente existente do enterprise. Você pode delegar aguardando entrada sessões e solicitações para esses serviços WCF para o serviço de barramento de serviço em execução dentro do Azure. Isso permite expor esses serviços para código do aplicativo em execução no Azure ou ambientes de parceiro extranet ou trabalhadores móveis. Barramento de serviço permite com segurança controlar quem pode acessar esses serviços em um nível refinado. Ele fornece uma maneira eficiente e segura para expor dados de suas soluções existentes do enterprise e as funcionalidades do aplicativo e tirar proveito da nuvem.

Este artigo mostra como usar a retransmissão de barramento de serviço para criar um serviço web do WCF, exposto usando uma associação de canal TCP, que implementa uma conversação segura entre duas partes.

## <a name="create-a-service-namespace"></a>Criar um namespace de serviço

Para começar a usar a retransmissão de barramento de serviço no Azure, você deve primeiro criar um namespace. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo.

Para criar um namespace de serviço:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote de serviço barramento NuGet

O [pacote de serviço barramento NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil para obter a API do barramento de serviço e configurar o aplicativo com todas as dependências de barramento de serviço. Para instalar o pacote NuGet em seu projeto, faça o seguinte:

1.  No Solution Explorer, clique com botão direito **referências**, clique em **Gerenciar pacotes do NuGet**.
2.  Pesquise "Barramento de serviço" e selecione o item de **Barramento de serviço do Microsoft Azure** . Clique em **instalar** para concluir a instalação, feche a caixa de diálogo a seguir:

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Usar barramento de serviço para expor e consumir um serviço da web SOAP com TCP

Para expor um serviço web de WCF SOAP existente para consumo externo, você deve fazer alterações a ligações de serviço e endereços. Isso pode exigir alterações em seu arquivo de configuração ou pode exigir alterações de código, dependendo de como você configurar e configurou seus serviços WCF. Observe que o WCF permite que você tem vários pontos de extremidade de rede sobre o mesmo serviço, portanto, você pode manter os pontos de extremidade internos existentes ao adicionar pontos de extremidade de barramento de serviço para acesso externo ao mesmo tempo.

Nesta tarefa, você cria um serviço WCF simples e adicionar um ouvinte de barramento de serviço a ele. Este exercício pressupõe familiaridade com o Visual Studio e, portanto, não percorrer todos os detalhes da criação de um projeto. Em vez disso, ele se concentra no código.

Antes de iniciar essas etapas, conclua o procedimento a seguir para configurar seu ambiente:

1.  No Visual Studio, crie um aplicativo de console que contém dois projetos, "Cliente" e "Serviço", dentro da solução.
2.  Adicione o pacote do Microsoft Azure serviço barramento NuGet para ambos os projetos. Este pacote adiciona todas as referências de assembly necessário para seus projetos.

### <a name="how-to-create-the-service"></a>Como criar o serviço

Primeiro, crie o próprio serviço. Qualquer serviço WCF consiste em pelo menos três partes distintas:

-   Definição de um contrato que descreve quais mensagens são trocadas e quais operações são a ser chamado.
-   Implementação de contrato disse.
-   Host que hospeda o serviço WCF e expõe vários pontos de extremidade.

Os exemplos de código nesta seção abordam cada um desses componentes.

O contrato define uma única operação, `AddNumbers`, que adiciona dois números e retorna o resultado. O `IProblemSolverChannel` interface permite que o cliente para gerenciar o ciclo de vida do proxy com mais facilidade. Criação de tal interface é considerada uma prática recomendada. É uma boa ideia para colocar essa definição de contrato em um arquivo separado, para que você pode fazer referência a esse arquivo de projetos "Cliente" e "Serviço", mas você também pode copiar o código para ambos os projetos.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Com o contrato no lugar, a implementação é simples.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurar um host de serviço programaticamente

Com o contrato e implementação no lugar, agora você pode hospedar o serviço. Hospedagem ocorre dentro de um objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , que cuida do gerenciamento de instâncias do serviço e hospeda os pontos de extremidade que escutam mensagens. O código a seguir configura o serviço com um ponto de extremidade local regular e um ponto de extremidade do serviço barramento para ilustrar a aparência, lado a lado de pontos de extremidade internas e externas. Substitua a cadeia de caracteres *namespace* com seu nome de namespace e *yourKey* com a chave SAS obtido na etapa anterior configuração.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

No exemplo, você criar dois pontos de extremidade que estão na mesma implementação de contrato. Uma é local e um é projetado por meio de barramento de serviço. As principais diferenças entre elas são as ligações; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) para locais e [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) para o ponto de extremidade do barramento de serviço e os endereços. O ponto de extremidade local tem um endereço de rede local com uma porta distinto. O ponto de extremidade do serviço barramento tem um endereço de ponto de extremidade composto da cadeia de caracteres `sb`, seu nome de espaço para nome e o caminho "solver." Isso resulta no URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identificar o ponto de extremidade do serviço como um ponto de extremidade do serviço barramento TCP com um nome DNS externo totalmente qualificado. Se você colocar o código substituindo os espaços reservados para o `Main` função do aplicativo de **serviço** , você terá um serviço funcional. Se quiser que o seu serviço para escutar exclusivamente em barramento de serviço, remova a declaração de ponto de extremidade local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurar um host de serviço no arquivo App

Você também pode configurar o host usando o arquivo App. O serviço de hospedagem código nesse caso aparece no próximo exemplo.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

As definições de ponto de extremidade mova para o arquivo App. O pacote NuGet já adicionou um intervalo de definições para o arquivo App, que são as extensões de configuração necessárias para barramento de serviço. O exemplo a seguir, que é o equivalente exato do código anterior, deverá aparecer diretamente abaixo do elemento **ServiceModel** . Este exemplo de código pressupõe que namespace do seu projeto c# é chamado de **serviço**.
Substitua os espaços reservados a sua chave SAS e namespace do barramento de serviço do serviço.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Após fazer essas alterações, o serviço é iniciado como fazia antes, mas com dois pontos de extremidade ao vivo: um local e um listening na nuvem.

### <a name="create-the-client"></a>Crie o cliente

#### <a name="configure-a-client-programmatically"></a>Configurar um cliente programaticamente

Para consumir o serviço, você pode construir um cliente do WCF usando um objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Barramento de serviço usa um modelo de segurança baseada em token implementado usando SAS. A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. O exemplo a seguir usa o método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para lidar com a aquisição do token SAS apropriado. O nome e a chave são aqueles obtidos a partir do portal, conforme descrito na seção anterior.

Primeiro, referência ou copie o `IProblemSolver` código do serviço de contratos em seu projeto de cliente.

Em seguida, substitua o código no `Main` método do cliente, novamente substituindo o texto de espaço reservado por seu namespace barramento de serviço e a chave SAS.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Agora você pode construir o cliente e o serviço, executá-los (executar o serviço pela primeira vez) e o cliente chama o serviço e imprime **9**. Você pode executar o cliente e servidor em máquinas diferentes, mesmo entre redes e comunicação ainda funcionarão. O código do cliente também pode executar na nuvem ou localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurar um cliente no arquivo App

O código a seguir mostra como configurar o cliente usando o arquivo App.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

As definições de ponto de extremidade mova para o arquivo App. O exemplo a seguir, que é a mesma que o código listado anteriormente, deverá aparecer diretamente abaixo do elemento **ServiceModel** . Aqui, como antes, substitua os espaços reservados com sua chave SAS e namespace barramento de serviço.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas para o serviço de retransmissão barramento de serviço, siga estes links para saber mais.

- [Barramento de serviço enviadas visão geral sobre mensagens](service-bus-relay-overview.md)
- [Azure barramento de serviço visão geral da arquitetura](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Baixe amostras de barramento de serviço do [Azure amostras][] ou consulte [Visão geral dos exemplos de barramento de serviço][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Exemplos do Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Visão geral dos exemplos de barramento de serviço]: ../service-bus-messaging/service-bus-samples.md