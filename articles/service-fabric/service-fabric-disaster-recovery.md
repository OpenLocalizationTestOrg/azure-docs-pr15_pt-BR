<properties
   pageTitle="Recuperação de serviço tecidos Azure | Microsoft Azure"
   description="Azure tecidos de serviço oferece os recursos necessários para lidar com todos os tipos de desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de dados em estrutura de serviço do Azure

Uma parte fundamental da entregar um aplicativo em nuvem de alta disponibilidade é garantir que ele pode sobreviver todos os diferentes tipos de falhas, inclusive aqueles que estão fora do seu controle. Este artigo descreve o layout físico de um cluster de estrutura de serviço do Azure no contexto de desastres potenciais e fornece orientação sobre como lidar com tais desastres para limitar ou eliminar o risco de tempo de inatividade ou perda de dados.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Layout físico da estrutura de serviço clusters no Azure

Para entender o risco causado por diferentes tipos de falhas, é útil saber como clusters são física dispostas no Azure.

Quando você cria um cluster de estrutura de serviço no Azure, são necessárias para escolher uma região onde ele será hospedado. Infraestrutura do Azure depois provisiona os recursos para que o cluster dentro da região, principalmente o número de máquinas virtuais (VMs) solicitada. Vamos examinar mais detalhadamente como e onde essas VMs são provisionadas.

### <a name="fault-domains"></a>Domínios de falha

Por padrão, as VMs no cluster são distribuídas uniformemente grupos lógicos conhecidos como domínios de falha (FDs), que as máquinas com base em possíveis falhas no hardware do host de segmento. Especificamente, se duas VMs residem em dois FDs distintos, você pode ter certeza de que eles não compartilham o mesmo switch de origem ou de rede de energia. Como resultado, uma rede local ou queda de energia que afetam uma VM não afetará os outros, permitindo tecidos de serviço reequilibrar a carga de trabalho da máquina não responde dentro do cluster.

Você pode visualizar o layout do seu cluster em domínios de falhas usando o mapa de cluster fornecido no [Explorador de estrutura de serviço](service-fabric-visualizing-your-cluster.md):

![Nós distribuídos em domínios de falha no serviço tecidos Explorer][sfx-cluster-map]

>[AZURE.NOTE] Outro eixo no mapa de cluster mostra domínios atualização, que logicamente nós com base nas atividades de manutenção planejada de grupo. Serviço tecidos clusters no Azure sempre são dispostas em cinco domínios de atualização.

### <a name="geographic-distribution"></a>Distribuição geográfica

Existem atualmente [26 regiões Azure em todo o mundo][azure-regions], com vários mais anunciado. Uma região individual pode conter um ou mais data centers físicos dependendo demanda e a disponibilidade de locais adequados, entre outros fatores. Observe, contudo, que, mesmo em regiões que contêm vários centros de dados físicos, não há nenhuma garantia que VMs do seu cluster ser distribuídas uniformemente entre esses locais físicos. Na verdade, atualmente, todas as VMs para um determinado cluster são provisionadas dentro de um único site físico.

## <a name="dealing-with-failures"></a>Lidando com falhas

Há vários tipos de falhas que podem afetar o seu cluster, cada uma com sua própria atenuação. Veremos-las em ordem de probabilidade ocorra.

### <a name="individual-machine-failures"></a>Falhas de máquina individual

Conforme mencionado, falhas de máquina individual, a máquina virtual ou no hardware ou no software hospedando em um domínio de falhas, não oferecem nenhum risco por conta própria. Serviço tecidos normalmente será detectar a falha em segundos e responder adequadamente com base no estado do cluster. Por exemplo, se o nó foi hospedando as réplicas principais para uma partição, um novo primário é escolhido de réplicas secundário da partição. Quando Azure traz a máquina falha fazer backup, ele será reingressar no cluster automaticamente e assumir novamente o compartilhamento da carga de trabalho.

### <a name="multiple-concurrent-machine-failures"></a>Várias falhas de máquina simultâneas

Enquanto domínios de falha reduzem significativamente o risco de falhas de máquina simultâneas, sempre há a possibilidade de várias falhas aleatórias trazer para baixo várias máquinas em um cluster simultaneamente.

Em geral, desde que a maioria de nós permanecem disponíveis, cluster continuará a operar, embora em menor capacidade como réplicas com estado obtém incluídas em um conjunto menor de máquinas e menos instâncias de estado estão disponíveis para distribuir a carga.

#### <a name="quorum-loss"></a>Perda de quorum

Se a maioria das réplicas para partição de um estado serviço desce, partição entra em um estado conhecido como "perda de quorum". Neste ponto, o serviço tecidos interrompe permitindo gravações partição para garantir que seu estado permanece consistente e confiável. Na verdade, podemos está escolhendo aceitar um período de indisponibilidade para garantir que os clientes não dizem que seus dados foi salva quando na verdade não era. Observe que, se você tiver optado pelo permitindo leituras de réplicas secundárias para esse serviço estado, você pode continuar executar as operações enquanto estiver nesse estado de leitura. Uma partição permanece em perda de quorum até que um número suficiente de réplicas voltar ou o administrador de cluster força o sistema a mover em usar a [API de reparos-ServiceFabricPartition][repair-partition-ps].

>[AZURE.WARNING] Executar uma ação de reparo enquanto a réplica principal estiver inativo resultará em perda de dados.

Serviços do sistema podem também sofrer perda de quorum com o impacto sendo específico no serviço em questão. Por exemplo, perda de quorum no serviço de nomenclatura afetará a resolução de nomes, enquanto perda de quorum no serviço de Gerenciador de failover bloqueará failovers e criação de novos serviços. Observe que diferentemente para seus próprios serviços, tentar reparar os serviços do sistema *não* recomendado. Em vez disso, é preferível simplesmente aguardar até que as réplicas para baixo retornam.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Minimizar o risco de perda de quorum

Você pode minimizar o risco de perda de quórum aumentando o tamanho de conjunto de réplica de destino para o serviço. É útil pensar o número de réplicas, você precisa em termos do número de nós indisponíveis, que você pode suportar em depois permanecendo disponíveis para gravações, manter em mente aplicativo ou atualizações de cluster podem tornar nós temporariamente indisponível, além de falhas de hardware.

Considere os exemplos a seguir presumindo que você configurou seus serviços para ter uma MinReplicaSetSize de três, o menor número recomendado para serviços de produção. Com um TargetReplicaSetSize de três (um primário e dois secundários), uma falha de hardware durante uma atualização (duas réplicas para baixo) resultará em perda de quorum e seu serviço se tornarão somente leitura. Como alternativa, se você tiver cinco réplicas, você poderá suportar duas falhas durante a atualização (três réplicas para baixo), como as duas réplicas restantes ainda podem formarem um quorum dentro do conjunto de réplica mínimo.

### <a name="data-center-outages-or-destruction"></a>Interrupções de centro de dados ou destruição

Em casos raros, data centers físicos podem ficar temporariamente indisponíveis devido a perda de energia ou conectividade de rede. Nesses casos, clusters tecidos de serviços e aplicativos da mesma forma ficarão indisponíveis mas seus dados serão preservados. Para clusters em execução no Azure, você pode ver atualizações em interrupções na [página status do Azure][azure-status-dashboard].

No evento altamente provável que um física data center inteiro é destruído, qualquer clusters de serviço tecidos hospedados lá serão perdidas, juntamente com seu estado.

Para proteger contra essa possibilidade, ele é muito importante periodicamente [seu estado de backup](service-fabric-reliable-services-backup-restore.md) em uma loja geográfica redundantes e certifique-se de que você tenha validados a capacidade de restaurá-lo. Com que frequência você executa um backup dependerá seu objetivo de ponto de recuperação (RPO). Mesmo se você não tiver implementado totalmente backup e restauração ainda, você deve implementar um manipulador para o `OnDataLoss` evento para que você possa fazer quando ela ocorrer como segue:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Falhas de software e outras fontes de perda de dados

Como uma causa de perda de dados, são mais comuns do que falhas de centro de dados amplamente defeitos de código de serviços, erros operacionais humanos e violações de segurança. No entanto, em todos os casos, a estratégia de recuperação é a mesma: faça backups regulares de todos os serviços com informações de estado e exercício sua capacidade de restaurar esse estado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como simular várias falhas usando o [framework de capacidade de teste](service-fabric-testability-overview.md)
- Leia outros recursos de recuperação e alta disponibilidade. A Microsoft publicou uma grande quantidade de orientações sobre esses tópicos. Embora alguns desses documentos consultarem técnicas específicas para uso em outros produtos, eles contêm muitos práticas recomendadas gerais, que você pode aplicar no contexto tecidos de serviço:
 - [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md)
 - [Executar uma análise de recuperação de desastres](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Recuperação de dados e alta disponibilidade para aplicativos do Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
