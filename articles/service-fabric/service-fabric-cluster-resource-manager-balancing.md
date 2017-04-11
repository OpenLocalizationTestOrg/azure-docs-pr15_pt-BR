<properties
   pageTitle="Seu Cluster com o Azure tecidos Cluster recurso Gerenciador de serviços de balanceamento | Microsoft Azure"
   description="Uma introdução ao balanceamento seu cluster com o Gerenciador de recursos de Cluster tecidos do serviço."
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

# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento seu cluster de estrutura de serviço
O Gerenciador de recursos de Cluster do serviço tecidos permite relatório dinâmico de carga, reagir a alterações no cluster, corrigindo violações de restrição e redistribuição cluster se necessário. Mas quantas vezes ela faz estes itens, e o que aciona? Há vários controles relacionados a isso.

O primeiro conjunto de controles ao redor de balanceamento são um conjunto de timers. Esses temporizadores regem com que frequência o Gerenciador de recursos de Cluster examina o estado do cluster para saber o que precisa ser resolvido. Há três categorias diferentes de trabalho, cada uma com sua próprias timer correspondente. Eles são:

1.  Posicionamento – deste estágio lida com colocando qualquer réplicas com estado ou sem estado instâncias que estão ausentes. Isso abrange novos serviços e a manipulação de réplicas com estado ou sem estado instâncias que falharam e precisem ser recriadas. Excluindo e soltando réplicas ou instâncias também é tratado aqui.
2.  Verificações de restrição – deste estágio verifica e corrige violações as restrições de posicionamento diferentes (regras) dentro do sistema. Exemplos de regras são coisas como garantindo que nós não estão acima da capacidade e que as restrições de posicionamento de um serviço (mais informações sobre essas posterior) sejam atendidas.
3.  Balanceamento – deste estágio verifica ver se redistribuição proativo é necessário com base no nível de saldo para métricas diferentes configurado desejado e então tenta localizar uma disposição no cluster que é mais equilibrado.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurando o Gerenciador de recursos de Cluster etapas e temporizadores
Cada um desses tipos diferentes de correções o Gerenciador de recursos de Cluster pode fazer é controlada por um timer diferentes que controla sua frequência. Por exemplo, se desejar somente lidar com colocando novas cargas de trabalho do serviço no cluster cada hora (ao lote-los para cima), mas deseja balanceamento regulares verifica cada alguns segundos, você pode configurar esse comportamento. Quando é acionado cada temporizador, a tarefa é agendada. Por padrão, o Gerenciador de recursos examina seu estado e aplica atualizações (lotes todas as alterações que ocorreram desde a última verificação, como perceba que um nó está inativo) cada 1/dia 10 de um segundo, define o posicionamento e a restrição marque sinalizadores cada segundo e o balanceamento sinalizar a cada 5 segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Hoje é somente realizar uma destas ações ao mesmo tempo, sequencialmente (é por isso que chamamos essas configurações como "intervalos mínimos")). Isso é para que, por exemplo, nós já tiver respondido a qualquer solicitações pendentes para criar novas réplicas antes de continuarmos ao balanceamento de cluster. Como você pode ver os intervalos de tempo padrão especificados, podemos pode examinar e verificar se há alguma coisa que precisamos fazer muito frequentemente, indicando que o conjunto de alterações que vamos fazer no final de cada etapa normalmente é menor: nós não está examinando por meio de horas de alterações no cluster e tentar corrigi-los ao mesmo tempo, estamos tentando tratar coisas mais ou menos conforme eles ocorrem, mas com alguns lotes quando muitas coisas que acontece na mesmo tempo. Isso torna o recurso de serviço tecidos manager muito responder às ações que ocorrem em cluster.

Enquanto a maioria dessas tarefas são simples (se há violações de restrição, fix-las, se houver serviços seja criado, criá-las), o Gerenciador de recursos de Cluster também precisa de algumas informações adicionais para determinar se o cluster imbalanced. Para que temos duas outras partes da configuração: *Limites de balanceamento* e *Limites de atividade*.

## <a name="balancing-thresholds"></a>Limites de balanceamento
Um limite de balanceamento é o controle principal para o disparo redistribuição proativo (Lembre-se de que o timer é apenas para com que frequência o Gerenciador de recursos de Cluster deve verificar - não significa que nada acontecerá). O limite de balanceamento define como imbalanced cluster precisa ser de uma métrica específico na ordem para o Gerenciador de recursos do Cluster considerar-imbalanced e gatilho equilibrando.

Balanceamento limites são definidos em uma base por métrica como parte da definição de cluster. Para obter mais informações sobre métricas de check-out [neste artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

O limite de balanceamento de uma métrica é uma proporção. Se a quantidade de carga no nó mais carregado dividido pelo valor de carga no nó menos carregado exceder esse número, e em seguida, o cluster é considerado imbalanced e balanceamento será acionada na próxima vez que o Gerenciador de recursos de Cluster verifica (por padrão, nunca 5 segundos, como controlados por MinLoadBalancingInterval, mostrada acima).

![Exemplo de limite de balanceamento][Image1]

Neste exemplo simples, cada serviço está consumindo uma unidade de alguns métrica. No exemplo superior, a carga máxima em um nó é 5 e o mínimo é 2. Digamos que o limite de balanceamento para essa métrica é 3. Portanto, no exemplo superior, o cluster é considerado balanceadas e nenhum balanceamento será acionado quando o Gerenciador de recursos de Cluster verifica (como a proporção no cluster é 5/2 = 2,5 e é menor que o limite especificado balanceamento de 3).

No exemplo inferior, a carga máxima em um nó é 10, enquanto o mínimo é 2, resultando em uma taxa de 5. Isso coloca o cluster excede o limite de balanceamento projetado de 3 para métrica. Como resultado, uma execução rebalancing global será agendada na próxima vez em que o timer balanceamento aciona. Observe que só porque uma pesquisa balanceamento é desconexão acidental não significa nada moverá - às vezes, o cluster é imbalanced, mas não pode ser melhorada a situação - mas em uma situação como essa (pelo menos por padrão) alguns a carga certamente será distribuída para Node3. Observe que desde que não estamos usando uma abordagem greedy alguns carga poderia também ser distribuída para o Nó2 desde que resultaria em minimização das diferenças entre nós gerais, mas seria Esperamos que a maioria da carga seria fluxo para Node3.

![Balanceamento ações de limite de exemplo][Image2]

Observe que obtendo abaixo do limite de balanceamento não é um objetivo explícito – limites de balanceamento são apenas um *disparador* que informa que o Gerenciador de recursos de Cluster que pareça ao cluster para determinar quais melhorias que ele pode fazer, se houver.

## <a name="activity-thresholds"></a>Limites de atividade
Às vezes, embora nós são relativamente imbalanced, a quantidade *total* da carga no cluster é baixa. Isso pode ser devido a hora do dia ou porque o cluster está novos e apenas obtendo inicializar. Nos dois casos, talvez você não queira perder tempo balanceamento cluster porque há realmente muito pouco a ser obtido – você apenas ser passar recursos de rede e computação para mover os itens, sem fazer qualquer diferença absoluta. Como queremos evitar isso, há outro controle dentro do Gerenciador de recursos, conhecida como limites de atividade, que permite que você especificar algum limite inferior absoluto para atividade – se nenhum nó tiver pelo menos esta quantidade carga depois balanceamento não será acionada mesmo que o limite de balanceamento é atendido.

Como exemplo digamos que temos relatórios com os seguintes totais de consumo em nós. Digamos que estamos reter nosso limite balanceamento de 3 para essa métrica, mas agora também temos um limite de atividade de 1536 também. No primeiro caso, enquanto o cluster é imbalanced pelo limite de balanceamento nenhum nó atende esse limite mínimo de atividade, portanto, podemos sair coisas sozinho. No exemplo inferior, Node1 é maneira excede o limite de atividade, portanto balanceamento será executada (desde que o limite de balanceamento e o limite de atividade para a métrica são excedida)

![Exemplo de limite de atividade][Image3]

Assim como limites de balanceamento, atividade limites são definidos por métrica via a definição de cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Observe que os limites de balanceamento e atividade são ambos vinculados à métrica - balanceamento serão acionados apenas se limites balanceamento e atividade são excedidos para a mesma métrica. Portanto, se nós exceder o limite de balanceamento de memória e o limite de atividade CPU, balanceamento não acionará desde os limites restantes (o limite de balanceamento CPU) e limite de atividade para memória não são excedidos.

## <a name="balancing-services-together"></a>Serviços de balanceamento juntos
Algo que é interessante observar é que se o cluster é imbalanced ou não é uma decisão de todo o cluster, mas a maneira como podemos corrigi-lo está movendo réplicas de serviço individual e instâncias ao redor. Isso faz sentido, certo? Se memória é empilhada em um nó, várias réplicas ou instâncias poderiam ser contribui com ele, portanto poderia exigir movendo qualquer uma das réplicas com estado ou sem estado instâncias que usam a métrica afetada, imbalanced.

Ocasionalmente, embora um cliente entrará em contato conosco para cima ou arquivo um tíquete dizendo que um serviço que não foi imbalanced foi movido. Como ele pode acontecer a um serviço se desloca mesmo se todas as métricas do serviço foram balanceadas, até mesmo perfeitamente afirmativo, no momento do outro desequilíbrio? Vamos ver!

Veja por exemplo quatro serviços, Service1, gratuito2, ao Cliente3 e Serviço4. Service1 relatórios em relação às métricas Metric1 e Metric2, gratuito2 contra Metric2 e Mmetric3, ao Cliente3 contra Metric3 e Metric4 e Serviço4 contra alguns Metric99 métrica. Certamente você pode ver onde vamos aqui. Nós temos uma cadeia! Sob a perspectiva do Gerenciador de recursos de Cluster, podemos realmente não tiver quatro serviços independentes, temos um monte de serviços que estão relacionadas (Service1 gratuito2 e ao Cliente3) e que está desativada por conta própria.

![Serviços de balanceamento juntos][Image4]

Portanto, é possível que um desequilíbrio em Metric1 pode causar réplicas ou instâncias pertencentes ao Cliente3 para mover-se. Geralmente esses movimentos limitam-se bastante, mas pode ser maior dependendo exatamente como imbalanced Metric1 você tem e quais alterações foram necessárias no cluster para corrigi-lo. Também podemos dizer com certeza que um desequilíbrio em métricas 1, 2 ou 3 nunca fará com que os movimentos no Serviço4 – não deve haver nenhum ponto desde movendo réplicas ou instâncias pertencentes a Serviço4 em torno de podem fazem absolutamente nada para afetar o saldo de métricas de 1, 2 ou 3.

O Gerenciador de recursos de Cluster automaticamente descobre quais serviços estão relacionados, desde que serviços podem ter sido adicionados, removidas, ou tinham sua alteração de configuração métrica – por exemplo, entre duas séries de balanceamento gratuito2 o podem ter sido reconfigurados para remover Metric2. Isso quebra a corrente entre Service1 e gratuito2. Agora, em vez de dois grupos de serviços, você tem três:

![Serviços de balanceamento juntos][Image5]

## <a name="next-steps"></a>Próximas etapas
- Métricas são como o Gerenciador de recursos de Cluster do serviço tecidos gerencia consumo e a capacidade do cluster. Para saber mais sobre eles e como configurá-los check-out [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- Custo de movimento é uma maneira de sinalização para o Gerenciador de recursos de Cluster que determinados serviços são mais caros mover-se que outros. Para saber mais sobre o custo de movimento, consulte [Este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
- O Gerenciador de recursos de Cluster tem várias limitações que você pode configurar para reduzir a velocidade rotatividade no cluster. Eles não são normalmente necessários, mas se você precisar delas você pode aprender sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
