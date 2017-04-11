<properties
   pageTitle="Dimensionar um cluster de estrutura de serviço ou reduzir | Microsoft Azure"
   description="Dimensione um cluster de estrutura de serviço ou reduzir para corresponder ao demanda definindo regras dimensionar automaticamente para cada conjunto de escala de tipo/máquina virtual de nó. Adicionar ou remover nós para um cluster de estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Dimensionar um cluster de estrutura de serviço ou reduzir usando as regras de dimensionar automaticamente

Os conjuntos de escala de máquina virtual são um recurso de computação Azure que você pode usar para implantar e gerenciar um conjunto de máquinas virtuais como um conjunto. Cada tipo de nó que é definido em um cluster de estrutura de serviço está configurado como um conjunto de escala de máquina virtual separado. Cada tipo de nó, em seguida, pode ser dimensionado em ou check-out de maneira independente, ter diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente. Leia mais sobre ele no documento [nodetypes tecidos de serviço](service-fabric-cluster-nodetypes.md) . Como a estrutura de serviço tipos de nós no seu cluster são feitos de escala de máquina virtual define no back-end, você precisa configurar regras de dimensionar automaticamente para cada conjunto de escala de tipo/máquina virtual de nó.

>[AZURE.NOTE] Sua assinatura deve ter suficiente cores para adicionar as novos VMs que compõem este cluster. Não há nenhuma validação de modelo atualmente, assim, você obtém uma falha de tempo de implantação, se qualquer um dos limites de cota são atingido.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Escolha a escala de tipo/máquina virtual de nó definida para uma escala

Atualmente, não é possível especificar as regras de dimensionar automaticamente para conjuntos de escala de máquina virtual usando o portal, então vamos usar Azure PowerShell (1.0 +) para listar os tipos de nó e adicionar regras de dimensionar automaticamente a elas.

Para obter a lista de conjunto de escala de máquina virtual que compõem seu cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Definir regras de dimensionar automaticamente para o conjunto de escala de tipo/máquina virtual de nó

Se o seu cluster tiver vários tipos de nós, repita para cada escala de tipos/máquina virtual de nó define que você deseja dimensionar (ou reduzir). Leve em conta o número de nós que você precisa ter antes de configurar a escala automática. O número mínimo de nós que você deve ter para o tipo de nó primário é orientado por nível de confiabilidade que você escolheu. Leia mais sobre [níveis de confiabilidade](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Dimensionar para baixo o nó primário tipo menor do que a fazer número mínima cluster instável ou colocá-lo para baixo. Isso pode resultar em perda de dados para seus aplicativos e serviços do sistema.

Atualmente o recurso de dimensionar automaticamente não é orientado pelas cargas que seus aplicativos de serviço tecidos podem ser relatório. Portanto neste momento a dimensionar automaticamente que chegar puramente é orientada pelo desempenho contadores que são emitidos por cada da máquina virtual dimensionar as ocorrências do conjunto.  

Siga estas instruções [para configurar dimensionar automaticamente para cada conjunto de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Em uma escala para baixo cenário, a menos que o tipo de nó tem um nível de durabilidade de ouro ou prata você precisa chamar o [cmdlet remover ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) com o nome de nó apropriado.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Adicionar manualmente VMs a um conjunto de escala de tipo/máquina virtual de nó

Siga as amostras/instruções na [Galeria de modelos de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada Nodetype. 

>[AZURE.NOTE] Adição de VMs leva tempo, para que não espera que as adições seja instantânea. Portanto planeja adicionar capacidade bem em tempo, para permitir mais de 10 minutos antes da capacidade de máquina virtual estará disponível para as réplicas / instâncias obter colocado de serviço.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Remover manualmente VMs do conjunto de escala de tipo/máquina virtual de nó principal

>[AZURE.NOTE] Os serviços do sistema do serviço tecidos executar no nó principal, digite seu cluster. Para que nunca devem desligar ou dimensionar para baixo o número de instâncias em que tipos de nó menor que o nível de confiabilidade garante. Consulte [os detalhes sobre níveis de confiabilidade aqui](service-fabric-cluster-capacity.md). 

Você precisa executar a seguintes etapas uma máquina virtual instância por vez. Isso permite que os serviços do sistema (e seus serviços com informações de estado) ser desligar normalmente na instância de máquina virtual que você está removendo e novas réplicas criadas em outros nós.

1. Execute [ServiceFabricNode desativar](https://msdn.microsoft.com/library/mt125852.aspx) com intenção 'RemoveNode' para desabilitar o nó você vai remover (a instância alta nesse tipo de nó).

2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para certificar-se de que o nó realmente tem fizeram a transição para desativado. Caso contrário, espere até que o nó está desativado. Você não consegue apressar esta etapa.

2. Siga as amostras/instruções na [Galeria de modelos de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por outra em que Nodetype. A instância removida é a instância de máquina virtual mais alta. 

3. Repita as etapas 1 a 3 conforme necessário, mas nunca dimensionar para baixo o número de instâncias os tipos de nó principal menor do que o que vale o nível de confiabilidade. Consulte [os detalhes sobre níveis de confiabilidade aqui](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Remover manualmente VMs do conjunto de escala de tipo/máquina virtual de nó não principal

>[AZURE.NOTE] Para um serviço de estado, você precisa de um determinado número de nós sempre até ser manter a disponibilidade e preservar o estado de seu serviço. No mínimo, é necessário o número de nós igual à contagem de conjunto de réplica de destino do partição/serviço. 

É necessário o execute a seguintes etapas uma máquina virtual instância por vez. Isso permite que os serviços do sistema (e seus serviços com informações de estado) ser desligar normalmente na instância de máquina virtual que você está removendo e novas réplicas criadas onde mais.

1. Execute [ServiceFabricNode desativar](https://msdn.microsoft.com/library/mt125852.aspx) com intenção 'RemoveNode' para desabilitar o nó você vai remover (a instância alta nesse tipo de nó).

2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para certificar-se de que o nó realmente tem fizeram a transição para desativado. Se não espere até que o nó é desabilitado. Você não consegue apressar esta etapa.

2. Siga as amostras/instruções na [Galeria de modelos de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por outra em que Nodetype. Isso removerá agora a instância de máquina virtual mais alta. 

3. Repita as etapas 1 a 3 conforme necessário, mas nunca dimensionar para baixo o número de instâncias os tipos de nó principal menor do que o que vale o nível de confiabilidade. Consulte [os detalhes sobre níveis de confiabilidade aqui](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que você pode observar no Explorador de estrutura de serviço

Quando você dimensionar um cluster de serviço tecidos Explorer refletirão o número de nós (escala de máquina virtual define instâncias) que fazem parte do cluster.  No entanto, quando você dimensionar um cluster para baixo, você verá a instância de nó removido/máquina virtual exibida em um estado não íntegro, a menos que você chamar [ServiceFabricNodeState remover cmd](https://msdn.microsoft.com/library/mt125993.aspx) com o nome de nó apropriado.   

Aqui está uma explicação para esse comportamento.

Os nós listada no serviço tecidos Explorer são um reflexo das quais os serviços do sistema tecidos de serviço (FM especificamente) conhece o número de nós cluster tinha/tem. Quando você dimensionar a escala de máquina virtual definida para baixo, a máquina virtual foi excluída, mas o serviço de sistema FM ainda considera que o nó (que foi mapeado para a máquina virtual que foi excluída) retornarão. Portanto serviço tecidos Explorer continua a exibir nó (embora o estado de integridade pode ser erro ou desconhecido).

Para certificar-se de que um nó é removido quando uma máquina virtual é removida, você tem duas opções:

1) Escolha um nível de durabilidade de ouro ou prata (disponível em breve) para os tipos de nó no cluster, que oferece a você a integração de infraestrutura. Que depois removerá automaticamente os nós de estado de serviços (FM) nosso sistema quando você dimensionar para baixo.
Consulte [os detalhes sobre níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2) Depois que a instância de máquina virtual ter sido dimensionada para baixo, você precisa chamar o [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Serviço tecidos clusters exigem um determinado número de nós para correr o tempo todo para manter a disponibilidade e preservar o estado - denominado "manter quorum". Portanto, é geralmente não seguro para desligar a todas as máquinas no cluster, a menos que primeiro você executou um [backup completo do seu estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Próximas etapas
Leia o seguinte para também Saiba mais sobre o planejamento de capacidade de cluster, atualizando um cluster e partição serviços:

- [Planejar sua capacidade de cluster](service-fabric-cluster-capacity.md)
- [Atualizações de cluster](service-fabric-cluster-upgrade.md)
- [Serviços de estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
