<properties
   pageTitle="Desfragmentação de métricas em estrutura de serviço Azure | Microsoft Azure"
   description="Uma visão geral do uso desfragmentação ou remessa como uma estratégia de métricas em estrutura de serviço"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de carga em estrutura de serviço e métricas
O Gerenciador de recursos de Cluster do serviço tecidos principalmente trata balanceamento em termos de distribuir a carga – lembrando-se de que todos os nós no cluster igualmente sejam utilizados. Isso é geralmente o layout mais seguro e mais inteligente em termos de sobreviventes falhas desde que ele se certifica de que qualquer falha determinada não retirar a porcentagem alguns grande de uma determinada carga de trabalho. O Gerenciador de recursos de Cluster do serviço tecidos suporta uma estratégia diferente, que é desfragmentação. Desfragmentação geralmente significa que em vez de tentar distribuir a utilização de uma métrica em cluster, deve realmente tentamos consolidá-lo. Este é um privilégio inversão de nossa estratégia de normal – em vez de otimização do cluster com base em minimizando o desvio padrão médio de carga métrica de uma determinada métrica, vamos começar otimizando para aumentos de desvio. Mas, por que você desejaria essa estratégia?

Bem, se você tiver espaçar a carga uniformemente entre os nós no cluster que você já consumidas backup alguns dos recursos que os nós tem a oferecer. Normalmente esse não é um problema, mas, às vezes, algumas cargas de trabalho criam serviços que são muito grandes e consumam a maior parte de um nó – digamos 75% a 95% de recursos de um nó seria acabem dedicada a uma instância do serviço única ou réplica. Isso não é um problema, o Gerenciador de recursos de Cluster irá detectar na hora de criação do serviço que ele precisa reorganizar cluster para abrir espaço para este grande carga de trabalho e definir sobre fazendo acontecer, mas enquanto isso essa carga de trabalho tem de esperar para ser agendado no cluster.

Considerando que o agendamento de novas cargas de trabalho geralmente é pelo menos um pouco latência confidencial, se nós não fizer nada diferente podemos às vezes direita remover por esses SLAs se houver muitos serviços e estado para mover-se, especialmente se cargas de trabalho no cluster são geralmente grandes (e, portanto, demorando mais para mover-se no cluster). Na verdade, quando podemos medido tempos de criação em simulações com base em dados de cluster real, podemos viu que se os serviços foram grandes o suficiente e cluster bastante foi utilizado que a criação desses serviços grandes poderia ser mais lenta para baixo e que podemos pode melhorar isso introduzindo a política de métricas de desfragmentação.

Como arquivo de criação ou acesso poderia obter diminuíam que se um disco rígido foi fragmentado e poderia ser agiliza desfragmentando a unidade para que havia maiores blocos contíguos disponível, você pode configurar métricas de desfragmentação para que o Gerenciador de recursos de Cluster tentar proativamente concentrar a carga dos serviços em menos nós para que (quase) sempre há espaço para serviços de grandes , permitindo que eles sejam criadas rapidamente. A maioria das pessoas não precisa disto, porque serviços geralmente devem ser pequenos e, portanto, não é difícil localizar sala para eles, mas se você tiver serviços grandes e precisa deles criados rapidamente (e aceitar as outras compensações como impactfulness aumento de falhas e alguns recursos sendo deixado as enquanto esperam para cargas de trabalho ser agendado) e a estratégia de desfragmentação é para você.

O diagrama a seguir fornece uma representação visual dos dois clusters diferentes, que é desfragmentado e outro que não é. No caso equilibrado, considere os movimentos que seriam necessários para colocar um dos objetos de serviço de maior, se um novo ser criado, em comparação com o cluster desfragmentado, onde ele pode ser imediatamente colocado em nós 4 ou 5.

![Comparando balanceadas e desfragmentado Clusters][Image1]

## <a name="defragmentation-pros-and-cons"></a>Desfragmentação prós e contras
Quais são as outras compensações conceituais? Recomendamos que medida completa de suas cargas de trabalho antes de ligar métricas de desfragmentação. Aqui está uma tabela rápida de ponderações:

| Profissionais de desfragmentação  | Desfragmentação contras |
|----------------------|----------------------|
|Permite a criação mais rápida de serviços de grande | Carregamento de concentra-se no menos nós, aumentando disputa
|Permite que o menor movimentação de dados durante a criação    | Falhas podem afetar mais serviços e causar rotatividade mais
|Permite rich descrição requisitos e a recuperação de espaço | Configuração de gerenciamento de recursos de geral mais complexa

Você pode combinar métricas desfragmentadas e normais no mesmo cluster e o Gerenciador de recursos fará é melhor para garantir que você obtenha um layout que consolida quanto das métricas desfragmentação como ele pode enquanto tentando espaçar o restante. Os resultados exatos que você obterá dependerá o número de balanceamento métricas em comparação com o número de métricas de desfragmentação e seus pesos cargas atuais, etc.

## <a name="configuring-defragmentation-metrics"></a>Configurando métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster e métricas individuais podem ser selecionadas para desfragmentação:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Próximas etapas
- O Gerenciador de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles fazer check-out neste artigo [descrevendo um cluster de estrutura de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Métricas são como o Gerenciador de recursos de Cluster do serviço tecidos gerencia consumo e a capacidade do cluster. Para saber mais sobre eles e como configurá-los check-out [neste artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
