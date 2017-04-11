<properties
   pageTitle="Gerenciador de recursos de Cluster do serviço tecidos - grupos de aplicativos | Microsoft Azure"
   description="Visão geral dos recursos do grupo de aplicativos no Gerenciador de recursos de serviço tecidos Cluster"
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

# <a name="introduction-to-application-groups"></a>Introdução aos grupos do aplicativo
Gerenciador de recursos de Cluster do serviço tecidos normalmente gerencia recursos de cluster distribuindo a carga (representada por meio de métricas) uniformemente em todo o cluster. Serviço tecidos também gerencia a capacidade de nós no cluster e o cluster como um todo através a noção da capacidade. Isso funciona bem com muitos tipos diferentes de cargas de trabalho, mas padrões que usem pesado instâncias do aplicativo de estrutura de serviço diferente, às vezes, trazer requisitos adicionais. Alguns requisitos adicionais são normalmente:

- Capacidade de reservar capacidade para os serviços de uma instância aplicativo em algum número de nós
- Capacidade de limitar o número total de nós permitidos para executar em um determinado conjunto de serviços dentro de um aplicativo
- Definindo capacidades na própria instância do aplicativo para limitar o número ou o consumo de recursos total dos serviços dentro dele

Para atender a esses requisitos, podemos desenvolveu suporte para o que chamamos de grupos de aplicativos.

## <a name="managing-application-capacity"></a>Gerenciando capacidade de aplicativo
Capacidade de aplicativo pode ser usada para limitar o número de nós ocupadas por um aplicativo, bem como a carga de métrica total do que instâncias dos aplicativos nós individuais. Ele também pode ser usado para reservar recursos no cluster para o aplicativo.

Capacidade de aplicativo pode ser configurado para novos aplicativos quando eles são criados; Ele também pode ser atualizado para os aplicativos existentes que foram criados sem especificar a capacidade de aplicativo.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitar o número máximo de nós
Caso de uso mais simples para capacidade de aplicativo é quando uma instanciação de aplicativo precisa estar limitado a um determinado número máximo de nós. Se nenhuma capacidade de aplicativo for especificada, o Gerenciador de recursos de Cluster do serviço tecidos criará uma instância réplicas de acordo com regras normais (balanceamento ou desfragmentação), que geralmente significa que seus serviços vai ser distribuídos em todos os nós disponíveis no cluster, ou se desfragmentação está ativada algum número aleatório, mas menor de nós.

A imagem a seguir mostra o potencial posicionamento de uma instância de aplicativo sem o número máximo de nós definida e, em seguida, mesmo aplicativo com um número máximo de nós definida. Observe que não há nenhuma garantia feita sobre quais réplicas ou instâncias dos quais serviços serão obter colocados juntos.

![Definir o número máximo de nós de instância do aplicativo][Image1]

No exemplo à esquerda, o aplicativo não têm capacidade de aplicativo definir e ela tem três serviços. CRM fez uma decisão lógica espaçar todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster. No exemplo à direita, vemos o mesmo aplicativo que é restrito em três nós e onde o serviço tecidos CRM atingiu o melhor equilíbrio para as réplicas dos serviços do aplicativo.

O parâmetro que controla esse comportamento é chamado MaximumNodes. Este parâmetro pode ser definido durante a criação de um aplicativo ou atualizado para uma instância de aplicativo que já estava em execução, no qual caso serviço tecidos CRM irá restringir as réplicas dos serviços do aplicativo para o número máximo definido de nós.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Capacidade, carga e métricas de aplicativos
Grupos de aplicativos também permitem que você defina métricas associadas a uma instância de determinado aplicativo, bem como a capacidade do aplicativo em relação a essas métricas. Por exemplo você pode definir que como muitos serviços como deseja que podem ser criados na

Para cada métrica, existem 2 valores que podem ser definidas para descrever a capacidade para essa instância do aplicativo:

-   Total de aplicativo capacidade – representa a capacidade total do aplicativo de uma determinada métrica. Serviço tecidos CRM tentará limitar a soma dos carregamentos de métrica de serviços do aplicativo para o valor especificado; Além disso, se os serviços do aplicativo já estão consumindo carga até esse limite, Gerenciador de recursos do serviço tecidos Cluster não permitir a criação de novos serviços ou partições que faria com que ultrapasse esse limite de carga total.
-   Capacidade de nó máxima – Especifica a carga total máxima para réplicas dos serviços dos aplicativos em um único nó. Se a carga total no nó fala sobre essa capacidade, serviço tecidos CRM tentará mover réplicas para outros nós para que a restrição de capacidade seja respeitada.

## <a name="reserving-capacity"></a>A reserva de capacidade
Outro uso comum de grupos de aplicativo é garantir que os recursos dentro do cluster são reservados para uma instância de determinado aplicativo, mesmo se a instância do aplicativo ainda não tem os serviços dentro dela, ou mesmo se eles não estão consumindo os recursos ainda. Vamos dar uma olhada em como que funcionariam.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Especifica um número mínimo de nós e reserva de recursos
Reserva de recursos para uma instância de aplicativo requer especificando alguns parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- MinimumNodes - como Especifica um número máximo de destino de nós que podem ser executados os serviços dentro de um aplicativo, você pode também especificar o número mínimo de nós que deve ser executado em um aplicativo. Essa configuração efetivamente define o número de nós que os recursos devem ser reservados no mínimo, garantindo capacidade dentro do cluster como parte da criação de instância do aplicativo.
- NodeReservationCapacity - o NodeReservationCapacity pode ser definida para cada métrica dentro do aplicativo. Isso define a quantidade de carga métrica reservada para o aplicativo em qualquer nó onde são colocadas nenhuma das réplicas ou instâncias dos serviços dentro dela.

Vamos dar uma olhada em um exemplo de reserva de capacidade:

![Instâncias do aplicativo definindo capacidade reservada][Image2]

No exemplo à esquerda, aplicativos não têm qualquer capacidade de aplicativo definidos. Gerenciador de recursos do serviço tecidos Cluster será saldo do aplicativo réplicas de serviços de filho e instâncias juntamente com os de outros serviços (fora do aplicativo) para garantir o saldo no cluster.

No exemplo à direita, digamos que o aplicativo foi criado com um MinimumNodes definido como 2, MaximumNodes definido como 3 e um aplicativo métrica definida com uma reserva de 20, capacidade máxima em um nó de 50 e a capacidade de aplicativo total de 100, serviço tecidos será reservar capacidade em dois nós para o aplicativo de azul e não permitirá outras réplicas no cluster para consumir essa capacidade. Essa capacidade de aplicativo reservadas será considerada consumidas e contado com a capacidade de cluster restante.

Quando um aplicativo é criado com reserva, o Gerenciador de recursos de Cluster será reservar capacidade igual a MinimumNodes * NodeReservationCapacity em cluster, mas ele não reservar capacidade em nós específicos até que as réplicas dos serviços do aplicativo são criadas e colocadas. Isso permite flexibilidade, desde que nós são escolhidos para novas réplicas somente quando eles são criados. Capacidade é reservada em um nó específico quando pelo menos uma réplica é colocada nele.

## <a name="obtaining-the-application-load-information"></a>Obter as informações de carga de aplicativo
Para cada aplicativo que tenha capacidade de aplicativo definido você pode obter as informações sobre a carga de agregação relatada pelo réplicas dos seus serviços. Serviço tecidos fornece consultas PowerShell e API gerenciada para essa finalidade.

Por exemplo, a carga pode ser recuperada usando o seguinte cmdlet do PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

A saída dessa consulta conterá as informações básicas sobre a capacidade de aplicativo que foi especificada para o aplicativo, como nós de mínimo e máximo. Também será informações sobre o número de nós que o aplicativo está usando no momento. Portanto, para cada métrica de carga haverá informações sobre:
- : Métrica nome da métrica.
-   Capacidade de reserva: Cluster a capacidade reservada no cluster para este aplicativo.
-   Carga do aplicativo: Carga Total de réplicas de filho do aplicativo.
-   Capacidade de aplicativo: Máximo permitido valor da carga do aplicativo.

## <a name="removing-application-capacity"></a>Removendo a capacidade de aplicativo
Depois que os parâmetros de capacidade de aplicativo estão definidos para um aplicativo, eles podem ser removidos usando cmdlets APIs do aplicativo de atualização ou PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando removerá todos os parâmetros de capacidade de aplicativo do aplicativo e Gerenciador de recursos do serviço tecidos Cluster iniciará tratando este aplicativo como qualquer outro aplicativo no cluster que não tem esses parâmetros definidos. O efeito do comando é imediato e Gerenciador de recursos de Cluster excluirá todos os parâmetros de capacidade de aplicativo para este aplicativo; especificar-los novamente, você precisa atualizar aplicativo APIs a ser chamado com os parâmetros apropriados.

## <a name="restrictions-on-application-capacity"></a>Restrições na capacidade de aplicativo
Há várias restrições em parâmetros de capacidade de aplicativo que devem ser respeitados. No caso de erros de validação, a criação ou a atualização do aplicativo será rejeitada com um erro.
Todos os parâmetros de inteiro devem ser números não negativos.
Além disso, para parâmetros individuais restrições são da seguinte maneira:

-   MinimumNodes nunca deve ser maior que MaximumNodes.
-   Se capacidades de uma métrica de carga estiverem definidas, eles devem siga estas regras:
  - Capacidade de reserva de nó não deve ser maior do que a capacidade de nó máxima. Por exemplo, você não pode limitar a capacidade de métrica "CPU" no nó para 2 unidades e tentar reservar 3 unidades em cada nó.
  - Se MaximumNodes for especificado, então o produto de MaximumNodes e a capacidade de nó máxima deve não ser maior que capacidade Total do aplicativo. Por exemplo, se você definir a capacidade de nó máxima de carga métrica "CPU" para 8 e você definir o máximo de nós para 10, em seguida, capacidade de aplicativo Total deve ser maior que 80 para esta métrica de carga.

As restrições são impostas durante a criação de aplicativos (no lado do cliente) e durante a atualização de aplicativo (no lado do servidor). Durante a criação, este é um exemplo de uma violação de limpar os requisitos desde MaximumNodes < MinimumNodes e o comando falhará no cliente antes que a solicitação é enviada até mesmo para cluster de estrutura de serviço:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Um exemplo de atualização inválido é da seguinte maneira. Se podemos tirar um aplicativo existente e atualizar nós máximos para algum valor, a atualização passará:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Depois disso, podemos tentar atualizar nós mínimos:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

O cliente não tem contexto suficiente sobre o aplicativo para que ele permitirá a atualização passar para o cluster de estrutura de serviço. No entanto, no cluster, serviço tecidos irá validar o novo parâmetro junto com os parâmetros existentes e falhará a operação de atualização porque nós de um inimigo do valor mínimo é maior que o valor máximo de nós. Nesse caso, parâmetros de capacidade de aplicativo permanecerá inalterados.

Essas restrições são colocadas em lugar na ordem para Cluster Gerenciador de recursos sejam capazes de oferecer o melhor posicionamento para réplicas dos serviços de aplicativos.

## <a name="how-not-to-use-application-capacity"></a>Como não usar a capacidade de aplicativo

-   Não use a capacidade de aplicativo para restringir o aplicativo a um subconjunto específico de nós: Embora tecidos de serviço garantirá que nós máximo seja respeitado para cada aplicativo que tem a capacidade de aplicativo especificada, os usuários não podem decidir quais nós ele será instanciado em. Isso pode ser obtido usando restrições de posicionamento para serviços.
-   Não use a capacidade do aplicativo para garantir que dois serviços do mesmo aplicativo sempre serão colocados junto com os outros. Isso pode ser obtido usando a relação de afinidade entre serviços e afinidade pode ser limitada somente para os serviços que realmente devem ser colocados juntas.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre as outras opções disponíveis para configurar serviços de check-out o tópico sobre as outras configurações do Gerenciador de recursos de Cluster disponíveis [Saiba como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para saber mais sobre como o Gerenciador de recursos de Cluster gerencia e equilibra carga no cluster, confira o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Iniciar desde o começo e [Obtenha uma introdução para o Gerenciador de recursos de Cluster tecidos do serviço](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como métricas funcionam em geral, ler sobre [Métricas de carga de tecidos do serviço](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles fazer check-out neste artigo [descrevendo um cluster de estrutura de serviço](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
