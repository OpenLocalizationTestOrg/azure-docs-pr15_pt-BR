<properties
   pageTitle="Ação de capacidade de teste | Microsoft Azure"
   description="Este artigo fala sobre as ações de capacidade de teste encontradas em estrutura de serviço do Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Ações de capacidade de teste
Para simular uma infraestrutura confiável, estrutura de serviço do Azure fornece, desenvolvedor, com maneiras de simular a várias falhas reais e transições de estado. Elas são expostas como ações de capacidade de teste. As ações são APIs de baixo nível que causam uma inclusão de falhas específica, transição de estado ou validação. Combinando essas ações, você pode escrever cenários de teste abrangente dos seus serviços.

Serviço tecidos fornece que alguns cenários comuns de teste composta por essas ações. É altamente recomendável que você utiliza esses cenários internos, que são escolhidos cuidadosamente para testar as transições de estado comuns e casos de falha. No entanto, ações podem ser usadas para criar cenários de teste personalizada quando quiser adicionar cobertura cenários que não estão cobertos pelos cenários internos ainda ou que são personalizados adaptadas para seu aplicativo.

C# implementações das ações são encontradas no assembly System.Fabric.dll. O módulo do PowerShell do sistema tecidos encontra-se no assembly Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação do tempo de execução, o módulo do ServiceFabric PowerShell é instalado para permitir a facilidade de uso.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Normal versus ações de falhas amigável
Ações de capacidade de teste são classificadas em duas classificações principais:

* Falhas amigável: essas falhas simular falhas como reiniciar de máquina e causa uma falha de processo. Nesses casos de falhas, o contexto de execução do processo para abruptamente. Isso significa que nenhuma limpeza do estado pode ser executada antes que o aplicativo for iniciado novamente.

* Falhas normais: essas falhas simular ações normais como réplica move e cancelamentos disparados por balanceamento de carga. Nesses casos, o serviço recebe uma notificação de fechamento e pode limpar o estado antes de sair.

Para melhor validação de qualidade, execute a carga de trabalho do serviço e de negócios enquanto induzindo várias falhas normais e amigável. Falhas da mesma exercitam cenários em que o processo do serviço abruptamente sai no meio de fluxos de trabalho. Isso testa o caminho de recuperação depois que a réplica de serviço for restaurada pelo serviço tecidos. Isso ajudará a consistência de dados e se o estado de serviço é mantido corretamente após falhas de teste. O outro conjunto de teste de falhas (as falhas normais) que o serviço reage corretamente para réplicas sendo movidas pelo serviço tecidos. Isso testa manipulação de cancelamento no método RunAsync. O serviço precisa verificar se o token de cancelamento sendo definir, salvar corretamente seu estado e sair do método RunAsync.

## <a name="testability-actions-list"></a>Lista de ações de capacidade de teste

| Ação | Descrição | API gerenciada | Cmdlet do PowerShell | Falhas normal amigável |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Remove todos os o estado de teste do cluster no caso de um desligamento incorreto do driver de teste. | CleanTestStateAsync | Remover ServiceFabricTestState | Não aplicável |
| InvokeDataLoss | Induz perda de dados em uma partição de serviço. | InvokeDataLossAsync | ServiceFabricPartitionDataLoss invocar | Normal |
| InvokeQuorumLoss | Coloca uma partição de serviço de estado determinado em perda de quorum. | InvokeQuorumLossAsync | ServiceFabricQuorumLoss invocar | Normal |
| Mover principal | Move a réplica primária especificada de um serviço de estado para o nó de cluster especificado. | MovePrimaryAsync | Mover-ServiceFabricPrimaryReplica | Normal |
| Mover secundário | Move a réplica secundária atual de um serviço de estado de um nó de cluster diferente. | MoveSecondaryAsync | Mover-ServiceFabricSecondaryReplica | Normal |
| RemoveReplica | Simula uma falha de réplica removendo uma réplica de um cluster. Isso fechará a réplica e passará a função 'Nenhum', remover todos os seus estados do cluster. | RemoveReplicaAsync | Remover ServiceFabricReplica | Normal |
| RestartDeployedCodePackage | Simula uma falha de processo de pacote de código reiniciando um pacote de código implantado em um nó em um cluster. Isso será anulada o processo de pacote de código, que irá reiniciar todas as réplicas de serviço de usuário hospedadas daquele processo. | RestartDeployedCodePackageAsync | Reiniciar-ServiceFabricDeployedCodePackage | Da mesma |
| RestartNode | Simula uma falha de nó de cluster do serviço tecidos reiniciando um nó. | RestartNodeAsync | Reiniciar-ServiceFabricNode | Da mesma |
| RestartPartition | Simula um cenário de escurecimento data center escurecimento ou cluster reiniciando réplica algumas ou todas de uma partição. | RestartPartitionAsync | Reiniciar-ServiceFabricPartition | Normal |
| RestartReplica | Simula uma falha de réplica reiniciar uma réplica persistente em um cluster, fechando a réplica e depois reabri-la. | RestartReplicaAsync | Reiniciar-ServiceFabricReplica | Normal |
| StartNode | Inicia um nó em um cluster que já foi interrompido. | StartNodeAsync | Início-ServiceFabricNode | Não aplicável |
| StopNode | Simula uma falha de nó interrompendo um nó em um cluster. O nó permanecerá para baixo até StartNode é chamado. | StopNodeAsync | Parar ServiceFabricNode | Da mesma |
| ValidateApplication | Valida a disponibilidade e a integridade de todos os serviços de estrutura de serviço dentro de um aplicativo, geralmente após induzindo algumas falhas no sistema. | ValidateApplicationAsync | Teste-ServiceFabricApplication | Não aplicável |
| ValidateService | Valida a disponibilidade e a integridade de um serviço de serviço tecidos, geralmente após induzindo algumas falhas no sistema. | ValidateServiceAsync | Teste-ServiceFabricService | Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executar uma ação de capacidade de teste usando o PowerShell

Este tutorial mostra como executar uma ação de capacidade de teste usando o PowerShell. Você aprenderá como executar uma ação de capacidade de teste contra um cluster (caixa) local ou um cluster Azure. Microsoft.Fabric.Powershell.dll – o módulo do PowerShell de tecidos do serviço – é instalado automaticamente quando você instala o Microsoft serviço tecidos MSI. O módulo é carregado automaticamente quando você abre um prompt do PowerShell.

Tutoriais segmentos:

- [Executar uma ação em um cluster de uma caixa](#run-an-action-against-a-one-box-cluster)
- [Executar uma ação em um cluster do Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação em um cluster de uma caixa

Para executar uma ação de capacidade de teste contra um cluster local, primeiro conectar ao cluster e abra o prompt do PowerShell no modo de administrador. Vejamos a ação **ServiceFabricNode reiniciar** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Veja a ação **Reiniciar ServiceFabricNode** está sendo executada em um nó chamado "Node1". O modo de conclusão Especifica que ele não deve verificar se a ação de reiniciar nó realmente obteve êxito. Especificando o modo de conclusão como "Verificar" fará com que ela verificar se a ação de reiniciar realmente foi bem-sucedida. Em vez de especificar diretamente o nó por seu nome, você pode especificá-lo por meio de uma chave de partição e o tipo de réplica, da seguinte maneira:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reiniciar-ServiceFabricNode** deve ser usado para reiniciar um nó de serviço tecidos em um cluster. Isso interromperá o processo de Fabric.exe, que será reiniciado todas as sistema serviço e usuário serviço réplicas hospedadas no nó. Usando essa API para testar seu serviço ajuda a descobrir bugs ao longo de caminhos de recuperação de failover. Ele ajuda a simular falhas de nó no cluster.

A captura de tela a seguir mostra o comando de capacidade de teste de **Reinicialização ServiceFabricNode** em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída de **Get-ServiceFabricNode** primeiro (um cmdlet do módulo do PowerShell do serviço tecidos) mostra que o cluster local tem cinco nós: Node.1 para Node.5. Depois que a ação de capacidade de teste (cmdlet) **Reiniciar-ServiceFabricNode** é executada no nó, chamado Node.4, vemos que o tempo de atividade do nó tiver sido redefinido.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação em um cluster do Azure

A execução de uma ação de capacidade de teste (usando o PowerShell) em relação a um cluster Azure é semelhante a executar a ação em relação a um cluster local. A única diferença é que, antes de executar a ação, em vez de se conectar ao cluster local, você precisa se conectar ao cluster Azure primeiro.

## <a name="running-a-testability-action-using-c35"></a>Executar uma ação de capacidade de teste usando C & #35;

Para executar uma ação de capacidade de teste usando c#, primeiro você precisa se conectar ao cluster usando FabricClient. Então obter os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser usados para executar a mesma ação.
Uma maneira para executá-la olhando a ação RestartServiceFabricNode, é usando as informações de nó (nó nome e ID de instância de nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

- **CompleteMode** Especifica que o modo não deve verificar se a ação de reiniciar realmente obteve êxito. Especificando o modo de conclusão como "Verificar" fará com que ela verificar se a ação de reiniciar realmente foi bem-sucedida.  
- **OperationTimeout** define a quantidade de tempo para a operação termine antes de uma TimeoutException exceção.
- **CancellationToken** permite uma chamada pendente a ser cancelada.

Em vez de especificar diretamente o nó por seu nome, você pode especificá-lo por meio de uma chave de partição e o tipo de réplica.

Para obter mais informações, consulte [PartitionSelector e ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector é um auxiliar exposto na capacidade de teste e é usado para selecionar uma partição específica na qual deseja executar qualquer uma das ações a capacidade de teste. Ele pode ser usado para selecionar uma partição específica se o ID de partição é conhecido antecipadamente. Ou, você pode fornecer a chave da partição e a operação resolverá o ID de partição internamente. Você também tem a opção de selecionar uma partição aleatória.

Para usar esse auxiliar, crie o objeto PartitionSelector e selecione a partição usando um dos métodos selecione *. Em seguida, passe no objeto PartitionSelector à API que precisa dele. Se nenhuma opção for selecionada, o padrão é uma partição aleatória.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector é um auxiliar exposto na capacidade de teste e é usado para ajudar a selecionar uma réplica na qual deseja executar qualquer uma das ações a capacidade de teste. Ele pode ser usado para selecionar uma réplica específica, se a ID de réplica é conhecida antecipadamente. Além disso, você tem a opção de selecionar uma réplica principal ou um secundário aleatório. ReplicaSelector deriva PartitionSelector, portanto é necessário selecionar a réplica e a partição na qual você deseja executar a operação de capacidade de teste.

Para usar esse auxiliar, crie um objeto de ReplicaSelector e defina a maneira que você deseja selecionar a réplica e a partição. Você pode passá-la na API que requê-lo. Se nenhuma opção for selecionada, o padrão é uma réplica aleatória e partição aleatória.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Próximas etapas

- [Cenários de capacidade de teste](service-fabric-testability-scenarios.md)
- Como testar seu serviço
   - [Simular falhas durante cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
   - [Falhas de serviço para comunicação](service-fabric-testability-scenarios-service-communication.md)
