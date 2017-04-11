<properties
   pageTitle="A limitação no Gerenciador de recursos de cluster serviço tecidos | Microsoft Azure"
   description="Aprenda a configurar as limitações fornecidas pelo Gerenciador de recursos de serviço tecidos Cluster."
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>A limitação o comportamento do Gerenciador de recursos de Cluster tecidos do serviço
Mesmo se você configurou o Gerenciador de recursos de Cluster corretamente, o cluster pode obter interrompido. Por exemplo poderia ser nó ou falha de domínio falhas simultâneas - o que acontecerá se que ocorreu durante uma atualização? O Gerenciador de recursos tentará da melhor corrigir tudo, mas em tempos assim convém considerar uma barreira para que o próprio cluster tem uma chance de coloque (os nós que vão aparecer novamente fazer, as condições da rede reparar próprios, corrigidos bits são implantadas). Para ajudar com esses tipos de situações, o Gerenciador de recursos de Cluster do serviço tecidos incluem várias limitações. Observe que essas limitações são bastante interrupções e geralmente não devem ser usadas, a menos que houve alguns cuidado matemática feita em torno a quantidade de trabalho paralela que realmente pode ser realizada em cluster, bem como um frequente precisa responder estes classifica de (quer dizer) eventos de reconfiguração macroscopic planejado (conhecido como: "Muito ruim dias").

Em geral, recomendamos evitando dias muito ruim por meio de outras opções (como atualizações de código normal e evitando excesso de agendamentos cluster começar com) em vez de otimização seu cluster para impedir que ele usando recursos quando ele está tentando corrigir próprio). As limitações têm valores padrão que descobrimos por meio de experiência seja padrões de okey, mas você provavelmente deve dar uma olhada e ajustá-los à sua carga real esperada. Enquanto não estiver muito restringindo ou o carregamento que cluster é uma prática recomendada que você pode determinar que há casos que (até que você pode corrigi-las) onde você precisa ter algumas limitações no lugar, mesmo que significa que o cluster demorará mais tempo se estabilizar.

##<a name="configuring-the-throttles"></a>Configurando as limitações
São as limitações que são incluídas por padrão:

-   GlobalMovementThrottleThreshold – Isso controla o número total de movimentos no cluster por algum tempo (definido como o GlobalMovementThrottleCountingInterval, o valor em segundos)
-   MovementPerPartitionThrottleThreshold – Isso controla o número total de movimentos para qualquer partição de serviço por algum tempo (o MovementPerPartitionThrottleCountingInterval, valor em segundos)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Lembre-se de que na maioria das vezes que observamos clientes usar essas limitações foi porque estavam já em um ambiente de recurso restringido (como largura de banda de rede limitada para nós individuais ou discos que não foram até os requisitos de réplica paralela cria qual foram sendo colocado nelas) que queria que tal operações não tenham êxito ou seria lenta mesmo assim.  Nessas situações clientes foram confortável sabendo que eles potencialmente estavam estendendo a quantidade de tempo levaria cluster alcançar um estado estável, incluindo sabendo que eles podem acabar funcionando com confiabilidade geral inferior enquanto eles foram limitados.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como o Gerenciador de recursos de Cluster gerencia e equilibra carga no cluster, confira o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gerenciador de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles fazer check-out neste artigo [descrevendo um cluster de estrutura de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
