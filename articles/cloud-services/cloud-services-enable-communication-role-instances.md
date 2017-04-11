<properties 
pageTitle="Comunicação para funções nos serviços de nuvem | Microsoft Azure" 
description="Instâncias de função nos serviços de nuvem podem ter pontos de extremidade (http, https, tcp, udp) definidos para eles que se comunicar com o lado externo ou entre outras instâncias de função." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Ativar a comunicação por instâncias de função no azure

Funções de serviço de nuvem se comunicar através de conexões internas e externas. Conexões externas são chamados de **pontos de extremidade de entrada** enquanto conexões internas são chamadas de **pontos de extremidade internos**. Este tópico descreve como modificar a [definição de serviço](cloud-services-model-and-package.md#csdef) para criar pontos de extremidade.


## <a name="input-endpoint"></a>Ponto de extremidade de entrada
O ponto de extremidade de entrada é usado quando você deseja expor uma porta para fora. Especifique o tipo de protocolo e a porta do ponto de extremidade que aplica-se para as portas internas e externas para o ponto de extremidade. Se desejar, você pode especificar uma porta interna diferente para o ponto de extremidade com o atributo [Porta_local](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

O ponto de extremidade de entrada pode usar os seguintes protocolos: **http, https, tcp e udp**.

Para criar um ponto de extremidade de entrada, adicione o elemento filho de **InputEndpoint** para o elemento de **pontos de extremidade** da função de uma web ou um colega.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto de extremidade de entrada do instância
Pontos de extremidade de entrada do instância são semelhantes a entrada pontos de extremidade mas permite mapear portas público específicas para cada instância de função individuais usando o encaminhamento de porta no balanceador de carga. Você pode especificar uma única porta de público ou um intervalo de portas.

O ponto de extremidade de entrada de instância só pode usar **tcp** ou **udp** como o protocolo.

Para criar um ponto de extremidade de entrada de instância, adicione o elemento filho de **InstanceInputEndpoint** para o elemento de **pontos de extremidade** da função de uma web ou um colega.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Ponto de extremidade interno
Pontos de extremidade internos estão disponíveis para comunicação de instância-a instância. A porta é opcional e se omitido, uma porta dinâmica será atribuída ao ponto de extremidade. Um intervalo de portas pode ser usado. Não há um limite de cinco pontos de extremidade internos por função.

O ponto de extremidade interno pode usar os seguintes protocolos: **http, tcp e udp, qualquer**.

Para criar um ponto de extremidade de entrada interno, adicione o elemento filho de **InternalEndpoint** para o elemento de **pontos de extremidade** da função de uma web ou um colega.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Você também pode usar um intervalo de portas.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Funções de trabalho versus funções da Web

Há uma diferença secundária com pontos de extremidade ao trabalhar com o operador e funções da web. A função web deve ter no mínimo um ponto de extremidade de entrada único usando o protocolo **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Usando o SDK do .NET para acessar um ponto extremo
Biblioteca de gerenciadas do Azure fornece métodos para instâncias de função para se comunicar em tempo de execução. Do código em execução dentro de uma instância de função, você pode recuperar informações sobre a existência de outras instâncias de função e seus pontos de extremidade, bem como as informações sobre a instância de função atual.

> [AZURE.NOTE] Você só pode recuperar informações sobre instâncias de função que executam em seu serviço de nuvem e que definem pelo menos um ponto de extremidade interno. Você não pode obter dados sobre instâncias de função em execução em outro serviço.

Você pode usar a propriedade de [instâncias](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) para recuperar instâncias de uma função. Use o [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) para retornar uma referência à instância atual de função e, em seguida, use a propriedade de [função](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) para retornar uma referência à função em si.

Quando você se conectar a uma instância da função programaticamente através do .NET SDK, é relativamente fácil acessar as informações de ponto de extremidade. Por exemplo, depois que você já estiver conectado a um ambiente de função específica, você pode obter a porta de um ponto de extremidade específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A propriedade de **instâncias** retorna uma coleção de objetos **RoleInstance** . Essa coleção sempre contém a instância atual. Se a função não definir um ponto de extremidade interno, a coleção inclui a instância atual, mas nenhuma outra instância. O número de instâncias de função na coleção sempre será 1 no caso onde nenhum ponto de extremidade interno é definido para a função. Se a função define um ponto de extremidade interno, suas instâncias são detectáveis em tempo de execução e o número de instâncias na coleção corresponderão ao número de instâncias especificadas para a função no arquivo de configuração do serviço.

> [AZURE.NOTE] A biblioteca do Azure gerenciado não oferece uma maneira de determinar a integridade de outras instâncias de função, mas você mesmo pode implementar tais avaliações de integridade, se o serviço precisa essa funcionalidade. Você pode usar o [Diagnóstico do Azure](cloud-services-dotnet-diagnostics.md) para obter informações sobre como executar instâncias de função.

Para determinar o número da porta para um ponto de extremidade interno em uma instância de função, você pode usar a propriedade [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) para retornar um objeto de dicionário que contém os nomes de ponto de extremidade e seus endereços IP correspondentes e portas. A propriedade [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) retorna o endereço IP e porta para um ponto de extremidade especificado. A propriedade **PublicIPEndpoint** retorna a porta para um ponto de extremidade de balanceamento de carga. A parte do endereço IP da propriedade **PublicIPEndpoint** não é usada.

Aqui está um exemplo que itera instâncias de função.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Aqui está um exemplo de uma função de trabalho que obtém o ponto de extremidade exposto por meio da definição do serviço e inicia aguardando conexões.

> [AZURE.WARNING] Este código só funcionará para um serviço implantado. Ao executar no emulador de computação do Azure, elementos de configuração de serviço que criar pontos de extremidade de porta direto (**InstanceInputEndpoint** elementos) são ignorados.

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regras de tráfego de rede para controlar a comunicação de função
Depois de definir pontos de extremidade internos, você pode adicionar regras de tráfego de rede (baseadas nos pontos de extremidade que você criou) para controlar como instâncias de função possam se comunicar com os outros. O diagrama a seguir mostra alguns cenários comuns para controlar a comunicação de função:

![Cenários de regras de tráfego de rede] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Cenários de regras de tráfego de rede")

O exemplo de código a seguir mostra definições de função para as funções mostradas no diagrama anterior. Cada definição de função inclui pelo menos um ponto de extremidade interno definido:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Restrição de comunicação entre funções pode ocorrer com pontos de extremidade internos de ambos fixo e portas atribuídas automaticamente.

Por padrão, após a definição de um ponto de extremidade interno, comunicação pode fluir de qualquer função para o ponto de extremidade interno de uma função sem restrições. Para restringir a comunicação, é necessário adicionar um elemento de **NetworkTrafficRules** ao elemento **ServiceDefinition** no arquivo de definição de serviço.

### <a name="scenario-1"></a>Cenário 1
Só permitir o tráfego de rede de **WebRole1** para **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Cenário 2
Somente permite que o tráfego de rede de **WebRole1** **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Cenário 3
Somente permite o tráfego de rede do **WebRole1** **WorkerRole1**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Cenário 4
Somente permite que o tráfego de rede de **WebRole1** **WorkerRole1**, **WebRole1** para **WorkerRole2**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Uma referência de esquema XML para os elementos usados acima pode ser encontrada [aqui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre o [modelo](cloud-services-model-and-package.md)do serviço na nuvem.