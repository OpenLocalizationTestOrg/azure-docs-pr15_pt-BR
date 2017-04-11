<properties
   pageTitle="Solucionar problemas com relatórios de integridade do sistema | Microsoft Azure"
   description="Descreve os relatórios de integridade enviados por componentes de estrutura de serviço do Azure e seu uso para cluster de solução de problemas ou problemas com aplicativos."
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

# <a name="use-system-health-reports-to-troubleshoot"></a>Usar relatórios de integridade do sistema para solucionar problemas

Azure serviço tecidos componentes relatório prontos para uso em todas as entidades no cluster. A [integridade armazenar](service-fabric-health-introduction.md#health-store) cria e exclui entidades com base nos relatórios do sistema. Ele também organiza-las em uma hierarquia que captura interações de entidade.

> [AZURE.NOTE] Para compreender conceitos relacionados a integridade, leia mais no [modelo de integridade do serviço tecidos](service-fabric-health-introduction.md).

Relatórios de integridade do sistema fornecem visibilidade cluster e funcionalidade do aplicativo e problemas de sinalizador por meio de integridade. Para aplicativos e serviços, relatórios de integridade do sistema, verifique se entidades são implementadas e estão funcionando corretamente da perspectiva do serviço tecidos. Os relatórios não fornecem qualquer monitoramento de integridade da lógica de negócios do serviço ou a detecção de processos travados. Serviços de usuário podem enriquecer os dados de integridade com informações específicas à sua lógica.

> [AZURE.NOTE] Relatórios de integridade watchdogs são visíveis somente *depois que* os componentes do sistema criam uma entidade. Quando uma entidade é excluída, o armazenamento de integridade exclui automaticamente todos os relatórios de integridade associados a ele. O mesmo acontece quando uma nova instância da entidade é criada (por exemplo, uma nova instância de réplica do serviço é criada). Todos os relatórios associados a instância antiga são excluídos e eliminados do repositório.

O componente do sistema relata são identificadas pela fonte, que começa com o "**sistema.**" prefixo. Watchdogs não podem usar o mesmo prefixo para suas fontes, como relatórios com parâmetros inválidos são rejeitados.
Vamos examinar alguns relatórios de sistema para entender o que aciona-los e como corrigir os possíveis problemas que eles representam.

> [AZURE.NOTE] Serviço tecidos continua a adicionar relatórios em condições de interesse que melhorar a visibilidade sobre o que está acontecendo no cluster e aplicativo.

## <a name="cluster-system-health-reports"></a>Relatórios de integridade do sistema de cluster
A entidade de integridade do cluster é criada automaticamente na loja de integridade. Se tudo está funcionando corretamente, ele não tem um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de ambiente
**System.Federation** relata um erro quando detecta uma perda de ambiente. O relatório é de nós individuais, e a ID de nó está incluída no nome da propriedade. Se um ambiente é perdido em ligar para o serviço tecidos inteira, normalmente você pode esperar dois eventos (ambos os lados do relatório espaço). Se mais vizinhanças forem perdidas, há mais eventos.

O relatório Especifica o tempo limite de concessão global como o tempo de vida. O relatório é enviado novamente a cada meia da duração TTL desde que a condição permanece ativa. O evento automaticamente é removido ao expirar. Remova quando comportamento expirado garante que o relatório é limpos do repositório integridade corretamente, mesmo se o nó relatórios está inoperante.

- **SourceId**: System.Federation
- **Propriedade**: começa com o **ambiente** e inclui informações de nó
- **Próximas etapas**: investigar por que o ambiente é perdido (por exemplo, verifique a comunicação entre nós de cluster).

## <a name="node-system-health-reports"></a>Relatórios de integridade do sistema de nó
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre nós de cluster. Cada nó deve ter um relatório de System.FM mostrando seu estado. As entidades de nó são removidas quando o estado de nó é removido (consulte [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nó para cima/baixo
System.FM relatórios como Okey quando o nó ingressa anel (ele está em execução). Ele relata um erro quando o nó entregas anel (é para baixo, seja para atualizar ou simplesmente porque ele falhou). A hierarquia de integridade criada pela loja integridade age em entidades implantadas em correlação com relatórios de nó System.FM. Ele considera o nó virtual pai de todas as entidades implantadas. As entidades implantadas nesse nó são expostas por meio de consultas se o nó for relatado como ativo por System.FM, com a mesma instância como a instância associada as entidades. Quando System.FM relata que o nó está inoperante ou reiniciado (uma nova instância), o armazenamento de integridade limpa automaticamente as entidades implantadas que podem existir somente o nó para baixo ou na instância anterior do nó.

- **SourceId**: System.FM
- **Propriedade**: estado
- **Próximas etapas**: se o nó for pressionada para uma atualização, ele deve voltar depois que ela foi atualizada. Nesse caso, o estado de integridade deve ser mudado para Okey. Se o nó não voltar ou falhar, o problema deve mais investigação.

O exemplo a seguir mostra o evento System.FM com um estado de integridade de Okey para nó para cima:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Expiração de certificado
**System.FabricNode** relatórios um aviso quando certificados usados pelo nó estão prestes a expirar. Há três certificados por nó: **Certificate_cluster**, **Certificate_server**e **Certificate_default_client**. Quando a expiração é pelo menos duas semanas, o estado de integridade de relatório é Okey. Quando a expiração está em duas semanas, o tipo de relatório é um aviso. TTL desses eventos é infinito, e eles são removidos quando um nó deixa o cluster.

- **SourceId**: System.FabricNode
- **Propriedade**: começa com o **certificado** e contém mais informações sobre o tipo de certificado
- **Próximas etapas**: atualizar os certificados se eles estiverem prestes a expirar.

### <a name="load-capacity-violation"></a>Violação de capacidade de carga
O balanceador de carga do serviço tecidos relatórios um aviso caso ele detecte uma violação de capacidade de nó.

 - **SourceId**: System.PLB
 - **Propriedade**: começa com **capacidade**
 - **Próximas etapas**: verificar fornecido métricas e exibir a capacidade atual no nó.

## <a name="application-system-health-reports"></a>Relatórios de integridade do sistema de aplicativo
**System.CM**, que representa o serviço do Gerenciador de Cluster, é a autoridade que gerencia informações sobre um aplicativo.

### <a name="state"></a>Estado
System.CM relatórios como Okey quando o aplicativo foi criado ou atualizado. Ele informa o repositório de integridade quando o aplicativo foi excluído, para que ele pode ser removido da loja.

- **SourceId**: System.CM
- **Propriedade**: estado
- **Próximas etapas**: se o aplicativo tiver sido criado, ele deve incluir o relatório de integridade do Gerenciador de Cluster. Caso contrário, verifique o estado do aplicativo por meio de uma consulta (por exemplo, o cmdlet do PowerShell * *Get-ServiceFabricApplication-ApplicationName *applicationName** *).

O exemplo a seguir mostra o evento de estado a **tecidos: / WordCount** aplicativo:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Relatórios de integridade do sistema de serviço
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre os serviços.

### <a name="state"></a>Estado
System.FM relatórios como Okey quando o serviço foi criado. Exclui a entidade do repositório integridade quando o serviço foi excluído.

- **SourceId**: System.FM
- **Propriedade**: estado

O exemplo a seguir mostra o evento de estado no serviço **tecidos: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violação de réplicas não alocados
**System.PLB** reporta um aviso se não consegue encontrar um posicionamento de uma ou mais réplicas de serviço. O relatório é removido ao expirar.

- **SourceId**: System.FM
- **Propriedade**: estado
- **Próximas etapas**: verificar as restrições de serviço e o estado atual do posicionamento.

O exemplo a seguir mostra uma violação para um serviço configurado com 7 réplicas de destino em um cluster com 5 nós:

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Relatórios de integridade do sistema de partição
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre partições de serviço.

### <a name="state"></a>Estado
System.FM relatórios como Okey quando a partição foi criada e está íntegra. Exclui a entidade do repositório integridade quando a partição é excluída.

Se a partição estiver abaixo a contagem de réplica mínimo, ele relata um erro. Se a partição não é abaixo a contagem de réplica mínima, mas ele está abaixo a contagem de réplica de destino, ele informa um aviso. Se a partição for em perda de quorum, System.FM relata um erro.

Outros eventos importantes incluem um aviso quando a reconfiguração demora mais que o esperado e quando a compilação demora mais do que o esperado. O esperado horas para a criação e a reconfiguração estão configuráveis com base em cenários de serviço. Por exemplo, se um serviço tem um terabytes de estado, como o banco de dados SQL, a compilação leva mais de para um serviço com uma pequena quantidade de estado.

- **SourceId**: System.FM
- **Propriedade**: estado
- **Próximas etapas**: se o estado de integridade não for Okey, é possível que algumas réplicas não foram criadas, abertas ou promovidas a principal ou secundário corretamente. Em muitos casos, a causa é um bug de serviço na implementação abrir ou alterar a função.

O exemplo a seguir mostra uma partição íntegra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

O exemplo a seguir mostra a integridade de uma partição que está abaixo contagem de réplica de destino. A próxima etapa é obter a descrição de partição, que mostra como ela está configurada: **MinReplicaSetSize** é dois e **TargetReplicaSetSize** sete. Em seguida, obter o número de nós no cluster: cinco. Portanto nesse caso, duas réplicas não podem ser colocadas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violação de restrição de réplica
**System.PLB** relatórios um aviso caso ele detecte uma violação de restrição de réplica e não é possível colocar réplicas da partição.

- **SourceId**: System.PLB
- **Propriedade**: começa com **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Relatórios de integridade do sistema de réplica
**System.RA**, que representa o componente do agente de reconfiguração, é a autoridade para o estado de réplica.

### <a name="state"></a>Estado
**System.RA** relatórios como Okey quando a réplica foi criada.

- **SourceId**: System.RA
- **Propriedade**: estado

O exemplo a seguir mostra uma réplica íntegra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Status de réplica aberto
A descrição deste relatório de integridade contém a hora de início (Universal Coordenado) quando a chamada API foi invocada.

Relatórios de **System.RA** um aviso se a réplica abrir demora mais do que o período configurado (padrão: 30 minutos). Se a API afeta a disponibilidade do serviço, o relatório é emitido muito mais rápido (um intervalo configurável, com um padrão de 30 segundos). O tempo medido inclui o tempo gasto para a abertura de replicator e abrir o serviço. A propriedade muda para Okey se abrir for concluída.

- **SourceId**: System.RA
- **Propriedade**: **ReplicaOpenStatus**
- **Próximas etapas**: se o estado de integridade não for Okey, investigar por que a réplica abrir demora mais do que o esperado.

### <a name="slow-service-api-call"></a>Reduzir a chamada de API do serviço
Relatório de **System.RAP** e **System.Replicator** um aviso se uma chamada para o código de serviço do usuário demora mais do que o tempo configurado. O aviso é desmarcado quando a chamada for concluída.

- **SourceId**: System.RAP ou System.Replicator
- **Propriedade**: O nome da API lenta. A descrição fornece mais detalhes sobre a hora que a API foi pendente.
- **Próximas etapas**: investigar por que a chamada demora mais do que o esperado.

O exemplo a seguir mostra uma partição em perda de quorum e as etapas de investigação concluídas para descobrir o porquê. Uma das réplicas tem um estado de integridade de aviso, assim você se sua integridade. Ela mostra que a operação de serviço demora mais do que o esperado, um evento relatado pelo System.RAP. Depois que essas informações for recebidas, a próxima etapa é examinar o código de serviço e investigar lá. Nesse caso, a implementação de **RunAsync** do serviço estado gera uma exceção não tratada. As réplicas são reciclagem, talvez você não veja duplicações no estado de aviso. Você pode repetir obtendo o estado de integridade e procure por quaisquer diferenças na ID de réplica. Em alguns casos, as tentativas podem dar dicas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Quando você inicia o aplicativo com defeito sob o depurador, as janelas de eventos de diagnóstico mostram a exceção de RunAsync:

![Eventos de diagnóstico Visual Studio 2015: falha de RunAsync em tecidos: / HelloWorldStatefulApplication.][1]

Eventos de diagnóstico Visual Studio 2015: falha de RunAsync em **tecidos: / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Fila de replicação completa
**System.Replicator** reporta um aviso se a fila de replicação está cheio. No sistema principal, isso geralmente acontece porque uma ou mais réplicas secundárias são lentas a reconhecer operações. No secundário, isso geralmente acontece quando o serviço está lento para aplicar as operações. O aviso é desmarcado quando a fila não está mais completa.

- **SourceId**: System.Replicator
- **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, dependendo da função de réplica

### <a name="slow-naming-operations"></a>Operações de nomenclatura lentas

**System.NamingService** relatórios integridade na sua réplica primária quando uma operação de nomenclatura demora mais do que aceitável. Exemplos de operações de nomenclatura são [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) ou [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Mais métodos podem ser encontrados em FabricClient, por exemplo em [métodos de gerenciamento de serviço](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) ou [métodos de gerenciamento de propriedade](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite aos usuários gerenciarem propriedades e nomes de serviço. É uma estrutura de serviço particionado persistentes serviço. Uma das partições representa o proprietário de autoridade, que contém metadados sobre todos os nomes de serviço tecidos e serviços. Os nomes de serviço tecidos são mapeados para partições diferentes, chamadas partições de proprietário de nome, portanto, o serviço é extensível. Leia mais sobre o [serviço de nomenclatura](service-fabric-architecture.md).

Quando uma operação de nomenclatura leva mais tempo do que o esperado, a operação é sinalizada com um relatório de aviso na *réplica principal da partição de serviço de nomenclatura que serve a operação*. Se a operação for concluída com êxito, o aviso está desmarcado. Se a conclusão da operação com um erro, o relatório de integridade inclui detalhes sobre o erro.

- **SourceId**: System.NamingService
- **Propriedade**: começa com prefixo **Duration_** e identifica a operação lenta e o nome da estrutura de serviço no qual a operação é aplicada. Por exemplo, se criar serviço em tecidos nome: / MyApp/meuserviço demore muito, a propriedade é Duration_AOCreateService.fabric:/MyApp/MyService. AO aponta para a função da partição de nomenclatura para esse nome e a operação.
- **Próximas etapas**: seleção por que a operação de nomenclatura falhará. Cada operação pode ter causas diferentes. Por exemplo, excluir serviço pode estar presas em um nó porque o host do aplicativo fica falhando em um nó devido a um erro de usuário no código de serviço.

O exemplo a seguir mostra uma operação de serviço de criar. A operação demorou mais do que a duração configurada. AO tentativas e envia trabalho para não. NÃO concluída a última operação com o tempo limite. Nesse caso, a mesma réplica é primária para AO e sem funções.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Relatórios de integridade do sistema de DeployedApplication
**System.Hosting** é a autoridade em entidades implantadas.

### <a name="activation"></a>Ativação
System.Hosting relatórios como Okey quando um aplicativo foi ativado com êxito no nó. Caso contrário, ele relata um erro.

- **SourceId**: System.Hosting
- **Propriedade**: ativação, incluindo a versão de implementação
- **Próximas etapas**: se o aplicativo estiver com problemas, investigar por que a ativação falha.

O exemplo a seguir mostra a ativação bem-sucedida:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Baixar
**System.Hosting** relata um erro se o download do pacote de aplicativo falhar.

- **SourceId**: System.Hosting
- **Propriedade**: * *Download:*RolloutVersion***
- **Próximas etapas**: investigar por falha no download no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de integridade do sistema de DeployedServicePackage
**System.Hosting** é a autoridade em entidades implantadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting relatórios como Okey se a ativação de pacote de serviço no nó for bem-sucedida. Caso contrário, ele relata um erro.

- **SourceId**: System.Hosting
- **Propriedade**: ativação
- **Próximas etapas**: investigar por que a ativação falha.

### <a name="code-package-activation"></a>Ativação de pacote de código
**System.Hosting** relatórios como Okey para cada pacote de código se a ativação for bem-sucedida. Se houver falha na ativação, ele informa um aviso conforme configurado. Se **CodePackage** Falha na ativação ou termina com um erro maior do que o configurado **CodePackageHealthErrorThreshold**, hospeda relatórios um erro. Se um pacote de serviço contém vários pacotes de código, um relatório de ativação é gerado para cada um deles.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **CodePackageActivation** e contém o nome do pacote de código e o ponto de entrada como * *CodePackageActivation:*CodePackageName*:*SetupEntryPoint/ponto de entrada* ** (por exemplo, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Registro de tipo de serviço
**System.Hosting** relatórios como Okey se o tipo de serviço foi registrado com êxito. Ele reporta um erro se o registro não foi feito no tempo (como configurados usando **ServiceTypeRegistrationTimeout**). Se o tipo de serviço foi cancelado do nó, isso ocorre porque o tempo de execução foi fechado. Um aviso de hospedagem de relatórios.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço (por exemplo, **ServiceTypeRegistration:FileStoreServiceType**)

O exemplo a seguir mostra um pacote de serviço implantado íntegra:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Baixar
**System.Hosting** relata um erro se o download do pacote de serviço falhar.

- **SourceId**: System.Hosting
- **Propriedade**: * *Download:*RolloutVersion***
- **Próximas etapas**: investigar por falha no download no nó.

### <a name="upgrade-validation"></a>Validação de atualização
**System.Hosting** relata um erro se falhas de validação durante a atualização ou se a atualização falhar no nó.

- **SourceId**: System.Hosting
- **Propriedade**: usa o prefixo **FabricUpgradeValidation** e contém a versão de atualização
- **Descrição**: aponta para o erro encontrado

## <a name="next-steps"></a>Próximas etapas
[Exibir relatórios de integridade do serviço tecidos](service-fabric-view-entities-aggregated-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md)
