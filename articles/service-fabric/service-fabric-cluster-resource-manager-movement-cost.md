<properties
   pageTitle="Custo de movimento do Gerenciador de recursos de Cluster do serviço tecidos: | Microsoft Azure"
   description="Visão geral de custo de movimentação para serviços de estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Custo de movimentação de serviço de influência opções do Gerenciador de recursos de Cluster
Um fator importante levar em consideração quando você está tentando determinar quais alterações para fazer um cluster e a pontuação de uma solução é o custo total de atingir essa solução.

Movendo serviço instâncias ou réplicas custos CPU tempo rede largura de banda e no mínimo. Para serviços de estado, ele também os custos a quantidade de espaço em disco que você precisa criar uma cópia do estado antes de desligar réplicas antigas. Claramente você deseja minimizar o custo de qualquer solução que o Gerenciador de recursos de Cluster do Azure serviço tecidos acompanha para cima. Mas também não desejar ignorar soluções que faria melhorar significativamente a alocação de recursos do cluster.

Gerenciador de recursos de cluster tem duas maneiras de computação custos e limitando-las, mesmo enquanto tenta gerenciar o cluster de acordo com suas metas de outros. O primeiro é que, ao Gerenciador de recursos de Cluster é planejar um novo layout para o cluster, ele conta cada mudança que faria. Em um caso simples, se você receber duas soluções com sobre o mesmo saldo geral (pontuação) no final e capture aquele com o menor custo (número total de movimentações).

Isso funciona bem. Mas como com padrão ou cargas estáticas, é provável em qualquer sistema complexo que move todos é igual. Alguns são provavelmente será muito mais econômico.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Alterando uma réplica mover custo e fatores a considerar
Como com relatórios de carga (outro recurso do Gerenciador de recursos de Cluster), você dê ao serviço uma maneira de relatar auto caro como o serviço é mover em um momento específico.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost tem quatro níveis: Zero, baixa, média e alta. Estas são relativo uns dos outros, exceto para Zero. Zero significa que movendo uma réplica é gratuita e não deve contar em relação a pontuação da solução. A configuração de seu custo mover como alto é *não* uma garantia de que a réplica não mover, apenas que ele não ser movido a menos que haja um bom motivo para.

![Mover o custo como um fator selecionando réplicas para movimentação][Image1]

MoveCost ajuda você a encontrar as soluções que causam o mínimo de interrupções geral e são mais fáceis de alcançar enquanto ainda chegar no saldo equivalente. Noção de um serviço de custo pode ser relativo muitas coisas. Os fatores mais comuns para calcular o custo de mover são:

- O valor de estado ou os dados que o serviço foi mover.
- O custo de desconexão de clientes. O custo de mover uma réplica primária normalmente é maior que o custo de mover uma réplica secundária.
- O custo de interromper uma operação em andamento. Nível de armazenamento de algumas operações os dados ou operações executadas em resposta a uma chamada de cliente são dispendiosas. Após um certo ponto, você não quiser pará-las se você não tiver a. Portanto da duração da operação, você aumentar o custo para reduzir a probabilidade de que a réplica de serviço ou instância moverá. Quando a operação for concluída, você pode colocá-lo voltar ao normal.

## <a name="next-steps"></a>Próximas etapas
- Gerenciador de recursos do serviço tecidos Cluster usa métricas para gerenciar a capacidade do cluster e consumo. Para saber mais sobre como configurá-los e métricas, confira [consumo de recursos de gerenciamento e carga em estrutura de serviço com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber mais sobre como o Gerenciador de recursos de Cluster gerencia e equilibra carga no cluster, confira [balanceamento seu cluster de estrutura de serviço](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
