<properties
   pageTitle="Gerenciador de recursos de Cluster do serviço tecidos - políticas de posicionamento | Microsoft Azure"
   description="Visão geral de políticas de posicionamento adicionais e regras para serviços de estrutura de serviço"
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

# <a name="placement-policies-for-service-fabric-services"></a>Políticas de posicionamento para serviços de estrutura de serviço
Há muitas regras adicionais diferentes que você pode acabar preocupar se seu cluster de estrutura de serviço está distribuído em uma distância geográficos, diga vários data centers ou regiões Azure, ou se o seu ambiente abrange várias áreas de controle geopolíticas (ou algum outro caso onde você tem limites legal ou política que me preocupar com ou a distância envolvidas têm impacto de latência/desempenho real). Maioria dessas pôde ser configurada por meio de propriedades de nó e restrições de posicionamento, mas alguns são mais complicadas. Para tornar as coisas mais simples fornecemos essas commmands adicionais. Assim como com outras restrições de posicionamento, políticas de posicionamento podem ser configuradas em uma base de instância do serviço por nome.

## <a name="specifying-invalid-domains"></a>Especificando domínios inválidos
A política de posicionamento InvalidDomain permite que você especificar que um determinado domínio de falhas é inválido para essa carga de trabalho. Essa política garante que um determinado serviço nunca é executado em uma área específica, por exemplo por razões políticas geopolíticas ou corporativa. Vários domínios inválidos podem ser especificados por meio de diretivas separadas.

![Exemplo de domínio inválido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificando domínios necessários
A política de posicionamento de domínio necessário exige que todas as réplicas com estado ou instâncias de serviço sem estado do serviço esteja presente no domínio especificado. Vários domínios necessários podem ser especificados por meio de diretivas separadas.

![Exemplo de domínio necessário][Image2]

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Especificar um domínio preferencial para as réplicas principais
O domínio primário preferencial é um controle interessante, pois ele permite a seleção do domínio falhas na qual o principal deve ser colocado se é possível fazer isso. Quando tudo está íntegro principal terminarão neste domínio. Devo o domínio ou falhas a réplica principal ou ser desligado por algum motivo que principal será migrado para outro local. Se esse local não estiver no domínio preferencial, em seguida, quando possível o Gerenciador de recursos de Cluster moverá-volta ao domínio preferencial. Naturalmente esta configuração só faz sentido para serviços de estado. Essa política é mais útil em clusters que estão abranger vários data centers ou regiões Azure. Nessas situações que você está usando todos os locais para redundância, mas prefere que as réplicas primárias sejam colocados em um determinado local para fornecer menor latência de operações que acesse o principal (grava e também por padrão todas as leituras são atendidas por principal).

![Failover e domínios primários preferenciais][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Exigindo réplicas a ser distribuído entre todos os domínios e remessa desabilitação
Outra política que você pode especificar é requerer réplicas a sempre ser distribuído entre os domínios de falhas disponíveis. Isso acontecerá por padrão na maioria dos casos onde o cluster está íntegro, no entanto há casos degenerados onde réplicas para uma determinada partição pode acabar temporariamente incluídas em uma única falha ou de um domínio de atualização. Por exemplo, digamos que embora cluster possui 9 nós na 3 domínios de falha (0, 1 e 2) e o serviço tem 3 réplicas, deu os nós que estavam sendo usados para essas réplicas no falhas domínios 1 e 2 para baixo e devido a problemas de capacidade nenhum dos outros nós nesses domínios eram válidas. Se criar substituições para essas réplicas tecidos de serviço, o Gerenciador de recursos de Cluster teria colocá-los no domínio de falhas 0, mas que cria uma situação onde a restrição de domínio de falha está sendo violada. Ele também aumenta a chance de que o conjunto de réplica inteira poderão ser perdido (se FD 0 eram permananently perdido). (Para obter mais informações sobre restrições e restrição prioridades geralmente, confira [neste tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Se você já viu um aviso de integridade como "balanceador de carga detectou uma violação de restrição esta réplica: tecidos: /<some service name> partição secundária <some partition ID> é violar a restrição: FaultDomain" você já pressionar esta condição ou algo parecido. Geralmente essas situações são temporárias (os nós não manter pressionada longa ou se seriam e precisamos construir substituições lá estão outros nós nos domínios correto de falha que são válidos), mas há algumas cargas de trabalho que preferir seriam trade disponibilidade para o risco de perder todas as suas réplicas. Podemos fazer isso, especificando a diretiva de "RequireDomainDistribution", que garante que não existam duas réplicas da mesma partição nunca são permitidas no mesmo domínio falha ou atualização.

Algumas cargas de trabalho desejam ter o número de destino de réplicas (cópias de estado) em qualquer ocasião (apostando contra falhas de domínio total e saber que eles geralmente podem recuperar o estado local), enquanto outras pessoas preferir levaria o tempo de inatividade anteriores às questões de correção e perda de risco. Como a maioria das cargas de trabalho de produção executado com mais de 3 réplicas, o padrão é não exigem a distribuição de domínio e deixar que balanceamento e failover lidar com casos normalmente mesmo se o que significa que temporariamente um domínio tem várias réplicas compactadas nela.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não foi estendido geograficamente? Verifique se você poderia! Mas não há uma boa razão muito – especialmente as configurações de domínio necessários, inválido e preferencial devem-se evitar a menos que você realmente está executando um cluster geograficamente estendido - não faz sentido para tentar forçar uma determinada carga de trabalho seja executado em um único rack ou preferir alguns segmentos de seu cluster local sobre outra, a menos que haja diferentes tipos de segmentação de hardware ou carga de trabalho em andamento , e nesses casos podem ser tratados por meio de restrições de posicionamento normal.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre as outras opções disponíveis para configurar serviços de check-out o tópico sobre as outras configurações do Gerenciador de recursos de Cluster disponíveis [Saiba como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
