<properties
   pageTitle="Como exibir entidades de estrutura de serviço do Azure agregado integridade | Microsoft Azure"
   description="Descreve como consultar, exibir e avaliar integridade agregado dos entidades tecidos de serviço do Azure, por meio de consultas de saúde e consultas gerais."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="view-service-fabric-health-reports"></a>Exibir relatórios de integridade do serviço tecidos
Azure tecidos de serviço apresenta um [modelo de saúde](service-fabric-health-introduction.md) que compõem entidades de integridade na qual sistema componentes e watchdogs podem relatar condições locais que eles são monitoramento. A [integridade armazenar](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se entidades são íntegros.

Prontos para uso, o cluster é preenchido com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [relatórios de integridade do sistema de uso para solucionar](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Serviço tecidos fornece várias maneiras para obter a integridade agregada das entidades:

- [Serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização

- Consultas de integridade (através do PowerShell, API ou REST)

- Geral consultas que retornam uma lista de entidades que possuem integridade como uma das propriedades (por meio do PowerShell, API ou REST)

Para demonstrar essas opções, vamos usar um cluster local com cinco nós. Ao lado do **tecidos: / sistema** aplicativo (que existe prontos para uso), alguns outros aplicativos são implantados. Um desses aplicativos é **tecidos: / WordCount**. Este aplicativo contém um serviço estado configurado com sete réplicas. Porque há apenas cinco nós, os componentes do sistema mostram um aviso de que a partição está abaixo a contagem de destino.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integridade no Explorador de estrutura de serviço
Serviço tecidos Explorer fornece um modo de exibição visual do cluster. Na imagem abaixo, você pode ver que:

- O aplicativo **tecidos: / WordCount** é vermelho (erro) porque tem um evento de erro relatado pelo **MyWatchdog** para a propriedade **disponibilidade**.

- Um dos seus serviços, **tecidos: / WordCount/WordCountService** amarelo (em aviso). Desde que o serviço está configurado com sete réplicas, eles não podem todos ser colocados, conforme existem apenas cinco nós. Embora ele não é mostrado aqui, partição de serviço é amarela devido o relatório do sistema. A partição amarela aciona o serviço amarelo.

- O cluster é vermelho devido o aplicativo vermelho.

A avaliação usa políticas padrão do manifesto cluster e manifesto do aplicativo. Eles são políticas rígidas e não suportar qualquer falha.

Modo de exibição do cluster com o serviço tecidos Explorer:

![Modo de exibição do cluster com o serviço tecidos Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Leia mais sobre o [Serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Consultas de integridade
Serviço tecidos expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy)com suporte. Eles podem ser acessados através do API (os métodos podem ser encontrados em **FabricClient.HealthManager**), cmdlets do PowerShell e restante. Essas consultas retornam informações de integridade completa sobre a entidade: o estado de integridade agregado, eventos de integridade de entidade, estados de integridade filho (quando aplicável) e não íntegras avaliações quando a entidade não está íntegra.

> [AZURE.NOTE] Uma entidade de integridade é retornada quando ela é totalmente preenchida na loja de integridade. A entidade deve ser ativo (não excluído) e tem um relatório de sistema. Suas entidades pai da cadeia de hierarquia também devem ter relatórios do sistema. Se alguma dessas condições não for atendida, as consultas de integridade retornaram uma exceção que mostra por que a entidade não é retornada.

As consultas de integridade devem passar o identificador de entidade, que depende do tipo de entidade. As consultas aceitam parâmetros de política de integridade opcional. Se nenhuma diretivas de integridade forem especificadas, as [diretivas de integridade](service-fabric-health-introduction.md#health-policies) do manifesto cluster ou aplicativo são usadas para avaliação. As consultas também aceitam filtros para retornar somente filhos parciais ou eventos – aqueles que respeitam os filtros especificados.

> [AZURE.NOTE] Os filtros de saída são aplicados no lado do servidor, para que o tamanho de resposta de mensagem é reduzido. É recomendável que você use os filtros de saída para limitar os dados retornados, em vez de aplica filtros no lado do cliente.

Integridade da entidade contém:

- O estado de integridade agregado da entidade. Calculado pela loja integridade com base em relatórios de integridade de entidade, estados de integridade filho (quando aplicável) e diretivas de integridade. Leia mais sobre [avaliação de integridade de entidade](service-fabric-health-introduction.md#entity-health-evaluation).  

- Os eventos de integridade na entidade.

- A coleção de estados de integridade de todos os filhos para as entidades que podem ter filhos. Os estados de integridade contêm identificadores de entidade e o estado de integridade agregado. Para obter integridade completa de filho, chame a integridade da consulta para o tipo de entidade filho e passe o identificador filho.

- As avaliações não íntegras que aponte para o relatório que disparou o estado da entidade, se a entidade não está íntegra.

## <a name="get-cluster-health"></a>Obter a saúde do cluster
Retorna a integridade da entidade cluster e contém os estados de integridade de aplicativos e nós (filhos do cluster). Entrada:

- [Opcional] A política de integridade de cluster usada para avaliar os nós e os eventos de cluster.

- [Opcional] Aplicativo integridade mapa de política, com as diretivas de integridade usadas para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros de eventos, nós e aplicativos que especifiquem as entradas de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os aplicativos, nós e eventos são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a saúde do cluster, crie uma `FabricClient` e chame o método [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) em seu **HealthManager**.

A chamada a seguir obtém a integridade de cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código a seguir obtém a integridade de cluster usando uma diretiva de integridade de cluster personalizados e filtros para nós e aplicativos. Ele cria [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), que contém as informações de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade de cluster é [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O estado do cluster é cinco nós, o aplicativo de sistema e tecidos: / WordCount configurado conforme descrito.

O cmdlet a seguir obtém integridade cluster usando diretivas de integridade do padrão. O estado de integridade agregado está na aviso, porque a estrutura: / WordCount aplicativo está no aviso. Observe como as avaliações não íntegras fornecem detalhes sobre as condições que disparou a integridade agregada.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

O seguinte cmdlet do PowerShell obtém a integridade do cluster usando uma política de aplicativo personalizado. Ele filtra os resultados para obter somente erro ou aplicativos de aviso e nós. Como resultado, sem nós são retornados, como eles estão todos íntegros. Somente a estrutura: / aplicativo WordCount respeita o filtro de aplicativos. Porque a política personalizada especifica considerar avisos como erros o tecidos: / WordCount aplicativo, o aplicativo é avaliado como em erro, e então é o cluster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>RESTANTE
Você pode obter integridade de cluster com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707669.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707696.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-node-health"></a>Obter integridade de nó
Retorna a integridade de uma entidade de nó e contiver os eventos de integridade relatados no nó. Entrada:

- [Requerido] O nome de nó que identifica o nó.

- [Opcional] As configurações de política de integridade do cluster usadas para avaliar a integridade.

- [Opcional] Filtros para eventos que especifiquem as entradas de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter integridade nó por meio da API, crie um `FabricClient` e chame o método [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) em seu HealthManager.

O código a seguir obtém a integridade de nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código a seguir obtém a integridade de nó para o nome de nó especificado e passa no filtro de eventos e política personalizada [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade de nó é [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .
O cmdlet a seguir obtém a integridade de nó usando diretivas de integridade padrão:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O cmdlet a seguir obtém a integridade de todos os nós no cluster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>RESTANTE
Você pode obter integridade de nó com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707650.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707665.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-application-health"></a>Obter a integridade do aplicativo
Retorna a integridade de uma entidade de aplicativo. Ele contém os estados de integridade do aplicativo implantado e filhos de serviço. Entrada:

- [Requerido] O nome do aplicativo (URI) que identifica o aplicativo.

- [Opcional] A política de integridade de aplicativo usada para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros para eventos, serviços e aplicativos implantados que especificar quais entradas são de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos, serviços e aplicativos implantados são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade do aplicativo, crie um `FabricClient` e chame o método [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) em seu HealthManager.

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI), com filtros e diretivas personalizadas especificadas via [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativo é [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet a seguir retorna a integridade do **tecidos: / WordCount** aplicativo:

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O seguinte cmdlet do PowerShell passa em políticas personalizadas. Ele também filtra filhos e eventos.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>RESTANTE
Você pode obter a integridade do aplicativo com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707681.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707643.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-service-health"></a>Obter a integridade do serviço
Retorna a integridade de uma entidade de serviço. Ele contém os estados de integridade de partição. Entrada:

- [Requerido] O nome de serviço (URI) que identifica o serviço.

- [Opcional] A política de integridade de aplicativo usada para substituir a política de manifesto do aplicativo.

- [Opcional] Filtros para partições que especifiquem as entradas e eventos são de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos e partições são usadas para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade do serviço por meio da API, crie um `FabricClient` e chame o método [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) em seu HealthManager.

O exemplo a seguir obtém a integridade de um serviço com nome de serviço especificado (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código a seguir obtém a integridade do serviço para o nome de serviço especificado (URI), especificando filtros e política personalizada via [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do serviço é [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet a seguir obtém a integridade do serviço usando as diretivas de integridade padrão:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>RESTANTE
Você pode obter a integridade do serviço com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707609.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707646.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-partition-health"></a>Obter integridade de partição
Retorna a integridade de uma entidade de partição. Ele contém os estados de integridade de réplica. Entrada:

- [Requerido] A identificação (GUID) que identifica a partição partição.

- [Opcional] A política de integridade de aplicativo usada para substituir a política de manifesto do aplicativo.

- [Opcional] Filtros para réplicas que especifiquem as entradas e eventos são de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos e réplicas são usadas para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter integridade partição por meio da API, crie um `FabricClient` e chame o método [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) em seu HealthManager. Para especificar parâmetros opcionais, crie [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade de partição é [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet a seguir obtém a integridade para todas as partições do **tecidos: / WordCount/WordCountService** serviço:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTANTE
Você pode obter integridade de partição com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707683.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707680.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-replica-health"></a>Obter integridade de réplica
Retorna a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. Entrada:

- [Requerido] A partição ID GUID () e réplica ID que identifica a réplica.

- [Opcional] Os parâmetros de política de integridade do aplicativo usados para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros para eventos que especifiquem as entradas de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade de réplica por meio da API, crie um `FabricClient` e chame o método [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) em seu HealthManager. Para especificar parâmetros avançados, use [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade de réplica é [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet a seguir obtém a integridade da réplica primária para todas as partições do serviço:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTANTE
Você pode obter integridade de réplica com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707673.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707641.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter o aplicativo implantado integridade
Retorna a integridade de um aplicativo implantado em uma entidade de nó. Ele contém os estados de integridade do serviço implantado pacote. Entrada:

- [Requerido] O nome do aplicativo (URI) e o nó nome (cadeia) que identificam o aplicativo implantado.

- [Opcional] A política de integridade de aplicativo usada para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros de eventos e pacotes de serviço implantado que especifiquem as entradas de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos e pacotes de serviço implantado são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade de um aplicativo implantado em um nó por meio da API, crie um `FabricClient` e chame o método [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) em seu HealthManager. Para especificar parâmetros opcionais, use [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativo implantado é [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Para descobrir onde um aplicativo é implantado, execute [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e examine os filhos de aplicativo implantado.

O cmdlet a seguir obtém a integridade do **tecidos: / WordCount** aplicativo implantado em **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTANTE
Você pode obter integridade de aplicativo implantado com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707644.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707688.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter o pacote de integridade do serviço implantado
Retorna a integridade de uma entidade de pacote de serviço implantado. Entrada:

- [Requerido] O nome do aplicativo (URI), o nome de nó (cadeia) e nome manifesto do serviço (cadeia) que identificam o pacote de serviço implantado.

- [Opcional] A política de integridade de aplicativo usada para substituir a política de manifesto do aplicativo.

- [Opcional] Filtros para eventos que especifiquem as entradas de interesse e devem ser retornados no resultado (por exemplo, somente, erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade de entidade agregada, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade de um pacote de serviço implantado por meio da API, crie um `FabricClient` e chame o método [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) em seu HealthManager. Para especificar parâmetros opcionais, use [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do pacote de serviço implantado é [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Para ver onde um aplicativo é implantado, execute [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e examine os aplicativos implantados. Para ver quais pacotes estão em um aplicativo de serviço, examine os filhos de pacote de serviço implantado na saída do [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

O cmdlet a seguir obtém a integridade do pacote de serviço **WordCountServicePkg** o **tecidos: / WordCount** aplicativo implantado em **_Node_2**. A entidade possui relatórios de **System.Hosting** para bem-sucedida de pacote de serviço e ativação de ponto de entrada e registro de tipo de serviço bem-sucedido.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTANTE
Você pode obter a integridade do pacote de serviço implantado com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/dn707677.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/dn707689.aspx) que inclui diretivas de integridade descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de bloco de integridade
As consultas de bloco de integridade podem retornar filhos de vários níveis cluster (repetidamente), por filtros de entrada. Ele oferece suporte a filtros avançados que permitem flexibilidade muito expressar quais filhos específico a ser retornado, identificado por seu identificador exclusivo ou outro identificador de grupo e/ou estado de integridade. Por padrão, sem filhos são incluídos, em vez de comandos de integridade que incluem sempre filhos de primeiro nível.

As [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries) retornam apenas os filhos de primeiro nível da entidade especificada por filtros necessários. Para obter os filhos dos filhos, você deve chamar APIs de integridade adicional para cada entidade de interesse. Da mesma forma, para obter a integridade de entidades específicas, você deve chamar uma integridade API para cada entidade desejada. A consulta de fragmento filtragem avançada permite que você solicite vários itens de interesse em uma consulta, minimizando o tamanho da mensagem e o número de mensagens.

O valor da consulta bloco é que você pode obter o estado de integridade para mais entidades cluster (possivelmente todos os cluster entidades começando na raiz exigida) em uma chamada. Você pode expressar consulta complexa integridade tais como:

- Retornar somente os aplicativos no erro e para os aplicativos incluem todos os serviços em aviso | erro. Para serviços retornados, incluem todas as partições.

- Retorne somente a integridade dos 4 aplicativos, especificados por seus nomes.

- Retorne somente a integridade dos aplicativos de um tipo de aplicativo desejado.

- Retorne entidades tudo implantadas em um nó. Retorna todos os aplicativos, todos implantaram aplicativos no nó especificado e todos os pacotes de serviço implantado nesse nó.

- Retorne todas as réplicas no erro. Retorna todos os aplicativos, serviços, partições e apenas réplicas em erro.

- Retorne todos os aplicativos. Para um serviço específico, inclua todas as partições.

Atualmente, a consulta de bloco de integridade é exposta somente para a entidade de cluster. Ela retorna um bloco de integridade do cluster, que contém:

- O estado de integridade de cluster agregado.

- A lista de bloco de estado de integridade de nós que respeita filtros de entrada.

- A lista de bloco de estado de integridade de aplicativos que respeita filtros de entrada. Cada bloco de estado de integridade de aplicativo contém uma lista de bloco com todos os serviços que respeitam filtros de entrada e uma lista de bloco com todos os aplicativos implantados que respeita os filtros. O mesmo para os filhos de serviços e aplicativos implantados. Dessa forma, todas as entidades no cluster podem ser potencialmente retornadas se solicitado, de maneira hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de bloco de integridade do cluster
Retorna a integridade da entidade cluster e contiver os blocos de estado de integridade hierárquica de filhos necessários. Entrada:

- [Opcional] A política de integridade de cluster usada para avaliar os nós e os eventos de cluster.

- [Opcional] Aplicativo integridade mapa de política, com as diretivas de integridade usadas para substituir as políticas de manifesto do aplicativo.

- [Opcional] Filtros para nós e aplicativos que especifiquem as entradas de interesse e devem ser retornados no resultado. Os filtros são específicos para um entidade/grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um filtro geral e/ou filtros para identificadores específicos para entidades fino retornados pela consulta. Se estiver vazio, os filhos não são retornados por padrão.
Leia mais sobre os filtros no [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) e [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). A recursiva do aplicativo filtros pode especificar filtros avançados para filhos.

O bloco de resultados inclui os filhos que respeitam os filtros.

Atualmente, a consulta de fragmento não retornar avaliações não íntegras ou eventos de entidade. Informações adicionais podem ser obtidas usando a consulta de integridade de cluster existente.

### <a name="api"></a>API
Para obter o fragmento de integridade do cluster, crie uma `FabricClient` e chame o método [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) em seu **HealthManager**. Você pode passar [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) para descrever as diretivas de integridade e filtros avançados.

O código a seguir obtém o fragmento de integridade do cluster com filtros avançados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade de cluster é [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Primeiro, conectar-se ao cluster usando o cmdlet [Conectar-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

O código a seguir obtém nós somente se estiverem em erro, com exceção de um nó específico, que sempre deve ser retornado.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

O cmdlet a seguir obtém fragmento de cluster com os filtros de aplicativo.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

O cmdlet a seguir retorna todas as entidades implantadas em um nó.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>RESTANTE
Você pode acessar o bloco de integridade de cluster com uma [solicitação de obter](https://msdn.microsoft.com/library/azure/mt656722.aspx) ou uma [solicitação POST](https://msdn.microsoft.com/library/azure/mt656721.aspx) que inclui diretivas de integridade e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
Consultas gerais retornam uma lista de entidades tecidos de serviço de um tipo especificado. Eles são expostos pela API (via os métodos em **FabricClient.QueryManager**), cmdlets do PowerShell e, restante. Essas consultas agregam subconsultas de vários componentes. Um deles é a [integridade armazenar](service-fabric-health-introduction.md#health-store), que preenche o estado de integridade agregado para cada resultado de consulta.  

> [AZURE.NOTE] Consultas gerais retornam o estado de integridade agregado da entidade e não contêm dados de integridade avançada. Se uma entidade não for eficaz, você pode acompanhar com consultas de integridade para obter todas as suas informações de saúde, incluindo eventos, estados de integridade de filho e não íntegras avaliações.

Se consultas gerais retornam um estado de integridade desconhecido para uma entidade, é possível que o repositório de integridade não tem dados completa sobre a entidade. Também é possível que uma subconsulta para o repositório de integridade não foi bem-sucedida (por exemplo, ocorreu um erro de comunicação, ou o armazenamento de integridade foi limitado). Acompanhamento com uma consulta de integridade da entidade. Se a subconsulta encontrou erros temporárias, como problemas de rede, essa consulta acompanhamento poderá ter êxito. Ele pode também lhe mais detalhes do repositório integridade sobre por que não seja exposta a entidade.

As consultas que contêm **HealthState** para entidades são:

- Lista de nós: retorna os nós de lista no cluster (paginado).
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Lista de aplicativos: Retorna a lista de aplicativos no cluster (paginado).
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Lista de serviços: Retorna a lista de serviços em um aplicativo (paginado).
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Lista de partição: Retorna a lista de partições em um serviço (paginado).
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Lista de réplica: Retorna a lista de réplicas em uma partição (paginada).
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Implantado lista de aplicativos: Retorna a lista de aplicativos implantados em um nó.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Implantado lista de pacote de serviço: Retorna a lista de pacotes de serviço em um aplicativo implantado.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Algumas das consultas retornam resultados de páginas. O retorno dessas consultas é uma lista derivada de [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Se os resultados não atender a uma mensagem, somente uma página é retornada e um ContinuationToken que controla onde parou enumeração. Você deve continuar chamar a mesma consulta e passar no token de continuação da consulta anterior para obter resultados próximos.

### <a name="examples"></a>Exemplos

O código a seguir obtém os aplicativos não íntegros do cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet a seguir obtém os detalhes do aplicativo para a estrutura: / WordCount aplicativo. Observe que o estado de integridade é em aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

O cmdlet a seguir obtém os serviços com um estado de integridade de aviso:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Atualizações de cluster e de aplicativo
Durante uma atualização monitorar do cluster e do aplicativo, serviço tecidos verifica integridade para garantir que tudo permaneça íntegro. Se uma entidade é não íntegra como avaliadas usando diretivas de integridade configurado, a atualização aplica as políticas de atualização específicas para determinar a próxima ação. A atualização pode estar pausada para permitir a interação do usuário (como corrigir condições de erro ou alterando diretivas) ou ele pode automaticamente reverter para a versão válida anterior.

Durante uma atualização de *cluster* , você pode obter o status de atualização de cluster. O status da atualização inclui avaliações sem integridade, que apontam para o que é não íntegro no cluster. Se a atualização é revertida devido a problemas de integridade, o status da atualização lembra os motivos não íntegros últimos. Essas informações podem ajudar os administradores a investigar o que deu errado após a atualização revertida ou interrompido.

Da mesma forma, durante uma atualização do *aplicativo* , qualquer avaliações não íntegras estão contidas no status de atualização do aplicativo.

A seguir mostra o status de atualização do aplicativo para uma estrutura modificada: / WordCount aplicativo. Uma monitoração relatado um erro em uma das suas réplicas. A atualização é sem interrupção porque as verificações de integridade não são respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre a [atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Use avaliações da integridade para solucionar problemas
Sempre que houver um problema com o cluster ou um aplicativo, examine a integridade cluster ou aplicativo para identificar o que está errado. As avaliações não íntegras fornecem detalhes sobre o que disparou o estado sem integridade atual. Se for necessário, você pode fazer busca detalhada em entidades filho não íntegra para identificar a causa raiz.

> [AZURE.NOTE] As avaliações não íntegras mostram que o primeiro motivo a entidade é avaliado como estado de integridade atual. Pode haver vários outros eventos que disparam esse estado, mas elas não são refletidas nas avaliações. Para obter mais informações, fazer busca detalhada em entidades integridade descobrir todos os relatórios não íntegros no cluster.

## <a name="next-steps"></a>Próximas etapas
[Usar relatórios de integridade do sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de integridade de serviço tecidos personalizados](service-fabric-report-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md)
