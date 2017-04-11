<properties
   pageTitle="Configurando resiliência e recuperação em Elasticsearch no Azure"
   description="Considerações relacionadas à resiliência e recuperação for Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="configuring-resilience-and-recovery-on-elasticsearch-on-azure"></a>Configurando resiliência e recuperação em Elasticsearch no Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série](guidance-elasticsearch.md). 

Um recurso chave do Elasticsearch é o suporte que ele fornece para resiliência em caso de falhas de nó e/ou eventos de partição de rede. Replicação é a maneira mais óbvia nas quais você pode melhorar a resiliência de qualquer cluster, permitindo que Elasticsearch garantir que mais de uma cópia de qualquer item de dados está disponível em nós diferentes no caso de um nó deve ficar inacessível. Se um nó ficar temporariamente indisponível, outros nós contendo réplicas de dados de nó que está faltando podem servir os dados ausentes até que o problema for resolvido. Em caso de um problema de termos mais tempo, o nó ausente pode ser substituído por um novo e Elasticsearch pode restaurar os dados para o novo nó a partir das réplicas.

Aqui, estamos resumir as opções de recuperação e resiliência disponíveis com Elasticsearch quando hospedado no Azure e descrevem alguns aspectos importantes de um cluster de Elasticsearch que você deve considerar para minimizar as chances de perda de dados e tempos de recuperação de dados estendido.

Este artigo também ilustra alguns testes de amostra que foram executadas para mostrar os efeitos de diferentes tipos de falhas em um cluster de Elasticsearch e como o sistema responde como ele recupera.

Um cluster de Elasticsearch usa réplicas para manter a disponibilidade e melhorar o desempenho de leitura. Réplicas devem ficar armazenadas em VMs diferentes dos fragmentos principais que eles replicam. A intenção é que se a máquina virtual hospedagem de um nó de dados falha ou fica indisponível, o sistema pode continuar funcionando usando as VMs mantendo as réplicas.

## <a name="using-dedicated-master-nodes"></a>Usando dedicado nós mestres

Um nó em um cluster de Elasticsearch é escolhido como o nó mestre. A finalidade deste nó é realizar operações de gerenciamento de cluster como:

- Detectar nós com falha e migrando para réplicas.

- Realocação fragmentos equilibrando a carga de trabalho de nó.

- Recuperando fragmentos quando um nó é colocado online.

Você deve considere usar dedicado nós mestres em clusters críticas e certifique-se de que há 3 nós dedicada cuja função somente é ser mestre. Essa configuração reduz a quantidade de trabalho intenso recurso que esses nós precisem realizar (eles não armazenar dados ou lidar com consultas) e ajuda a melhorar a estabilidade do cluster. Somente um de nós vai ser escolhido, mas as outras conterão uma cópia do estado do sistema e podem assumir o mestre lecionado deve falhar.

## <a name="controlling-high-availability-with-azure--update-domains-and-fault-domains"></a>Controlando alta disponibilidade com Azure – domínios de atualização e falhas 

VMs diferentes podem compartilhar o mesmo hardware físico. Em um data center Azure, um único rack pode hospedar um número de VMs e todas essas VMs compartilhar uma opção de origem e de rede de energia comuns. Falha de um único nível de rack, portanto, pode afetar a um número de VMs. Azure usa o conceito de domínios de falha experimentar e afaste esse risco. Um domínio de falha corresponde aproximadamente a um grupo de VMs que compartilham o mesmo rack. Para garantir que uma falha de nível de rack não falhar um nó e os nós mantendo todas as suas réplicas simultaneamente, você deve garantir que as VMs são distribuídas em domínios de falha.

Da mesma forma, VMs podem ser feitas para baixo pelo [Controlador de tecidos do Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) para executar atualizações de sistema operacional e a manutenção planejadas. Azure aloca VMs para atualizar domínios. Quando ocorre um evento de manutenção planejada, VMs somente em um domínio de única atualização são afetadas em qualquer momento. VMs em outros domínios de atualização são deixadas executando até que as VMs no domínio de atualização sendo atualizados são colocadas on-line. Portanto, você também precisa garantir que VMs hospedagem nós e suas réplicas pertencem a domínios de atualização diferente sempre que possível.

> [AZURE.NOTE] Para obter mais informações sobre domínios de falhas e atualizar, consulte [Gerenciar a disponibilidade de máquinas virtuais][].

Você não pode alocar explicitamente uma máquina virtual para um domínio de atualização específica e falhas. Essa alocação é controlada pelo Azure quando VMs são criadas. No entanto, você pode especificar que VMs devem ser criadas como parte de um conjunto de disponibilidade. VMs no mesmo conjunto de disponibilidade serão ser distribuídas em domínios de atualização e domínios de falha. Se você criar VMs manualmente, o Azure cria cada disponibilidade definida com dois domínios de falhas e cinco atualização. VMs estão alocadas para esses domínios de falha e atualizar domínios, ciclo redondo como ainda mais VMs são provisionadas, da seguinte maneira: 

| MÁQUINA VIRTUAL | Domínio de falhas | Domínio de atualização |
|----|--------------|---------------|
|  1 |            0 |             0 |
|  2 |            1 |             1 |
|  3 |            0 |             2 |
|  4 |            1 |             3 |
|  5 |            0 |             4 |
|  6 |            1 |             0 |
|  7 |            0 |             1 |

> [AZURE.IMPORTANT] Se você criar VMs usando o Gerenciador de recursos do Azure, cada conjunto de disponibilidade pode ser alocado até 3 falhas domínios e atualização de 20. Este é um bom motivo para usar o Gerenciador de recursos.

Em geral, coloque todas as VMs que têm a mesma finalidade no mesmo conjunto de disponibilidade, mas criar conjuntos de disponibilidade diferentes para VMs que executam funções diferentes. Com Elasticsearch, que isso significa que você deve considerar criar disponibilidade pelo menos separada define para:

- VMs hospedagem nós de dados.
- VMs hospedagem nós do cliente (se você estiver usando-las).
- VMs nós mestres de hospedagem.

Além disso, você deve garantir que cada nó em um cluster está ciente dos domínios de atualização e falhas pertence. Esta informação pode ajudar a garantir que Elasticsearch não criar fragmentos e suas réplicas na mesma falha e atualizar domínios, minimizando a possibilidade de um fragmentar e suas réplicas de sendo desligado ao mesmo tempo. Você pode configurar um nó Elasticsearch para espelhar a distribuição de hardware do cluster configurando [percepção de alocação fragmentar](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Por exemplo, você pode definir um par de atributos personalizados nó chamado *faultDomain* e *updateDomain* no arquivo elasticsearch.yml, da seguinte maneira:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Nesse caso, os atributos são definidos usando os valores contidos na * \${FAULTDOMAIN}* e * \${UPDATEDOMAIN}* variáveis de ambiente quando Elasticsearch é iniciado. Você também precisa adicionar as seguintes entradas ao arquivo Elasticsearch.yml para indicar que *faultDomain* *updateDomain* são alocação atributos de percepção e especificar os conjuntos de valores aceitáveis para esses atributos:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Você pode usar o reconhecimento de alocação fragmentar em conjunto com [a filtragem de alocação fragmentar](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) explicitamente especificar quais nós podem hospedar fragmentos para qualquer dado índice.

Se você precisar dimensionar além do número de domínios de falha e domínios de atualização em um conjunto de disponibilidade, você pode criar VMs em conjuntos de disponibilidade adicionais. No entanto, você precisa compreender que nós em conjuntos de disponibilidade diferentes podem ser desligadas para manutenção simultaneamente. Tente garantir que cada fragmentar e pelo menos uma das suas réplicas estão contidos dentro do mesmo conjunto de disponibilidade.

> [AZURE.NOTE] Atualmente, há um limite de 100 VMs por conjunto de disponibilidade. Para obter mais informações, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).

### <a name="backup-and-restore"></a>Backup e restauração

Uso de réplicas não oferece proteção completa de uma falha grave (como excluir acidentalmente todo o cluster). Você deve garantir que você faça backup dos dados em um cluster regularmente, e se você tem uma estratégia de testada e para restaurar o sistema desses backups.

Usar o Elasticsearch [instantâneo e restauração APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) : elástica não capitular essas. >> fazer backup e restaurar índices. Instantâneos podem ser salvos em um sistema de arquivos compartilhado. Como alternativa, plug-ins estão disponíveis que pode escrever instantâneos para o sistema de arquivos distribuído do Hadoop (HDFS) (o [plug-in HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) ou para o armazenamento do Azure (o [plug-in do Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Considere os seguintes pontos ao selecionar o mecanismo de armazenamento de instantâneo:

- Você pode usar o [armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) para implementar um sistema de arquivos compartilhado acessível de todos os nós.

- Use o plug-in HDFS apenas se você estiver executando Elasticsearch em conjunto com Hadoop.

- O plug-in HDFS requer que você desabilitar o Gerenciador de segurança Java executado dentro da instância de Elasticsearch da máquina virtual Java (JVM).

- O plug-in HDFS compatível com qualquer sistema de arquivos compatíveis com o HDFS, desde que a configuração correta do Hadoop é usada com Elasticsearch.

  
## <a name="handling-intermittent-connectivity-between-nodes"></a>Lidar com conectividade intermitente entre nós

Problemas de rede intermitente, máquina virtual reinicialização após a manutenção de rotina do data center e outros eventos semelhantes podem causar nós temporariamente inacessíveis. Nesses casos, onde o evento é provavelmente será vida curta, a sobrecarga de redistribuição os fragmentos ocorre duas vezes em rápido sucessivas (uma vez quando a falha é detectada e novamente quando o nó ficam visíveis no mestre) podem se tornar uma sobrecarga significativa que afeta o desempenho. Você pode impedir inacessibilidade dos nó temporário causando o mestre rebalancear cluster definindo a *atrasada\_tempo limite* propriedade de um índice, ou para todos os índices. O exemplo abaixo define o atraso para 5 minutos:

```http
PUT /_all/settings
{
    "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
    }
}
```

Para obter mais informações, consulte [atrasando alocação quando deixa de um nó](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

Em uma rede que está sujeita a interrupções, você também pode modificar os parâmetros que configurar um mestre para detectar quando outro nó não está mais acessível. Esses parâmetros são parte do módulo [Descoberta zen](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fornecido com Elasticsearch e você pode configurá-las no arquivo Elasticsearch.yml. Por exemplo, o parâmetro *discovery.zen.fd.ping.retries* Especifica quantas vezes um nó mestre tentará ping em outro nó no cluster antes de decidir que ele tenha falhado. Este parâmetro padrão é 3, mas você pode modificá-lo da seguinte maneira:

```yaml
discovery.zen.fd.ping_retries: 6
```

## <a name="controlling-recovery"></a>Controlando a recuperação

Quando a conectividade de um nó é restaurada após uma falha, qualquer fragmentos naquele nó precisará ser recuperadas para exibi-las atualizados. Por padrão, Elasticsearch recupera fragmentos na seguinte ordem:

- Por data de criação de índice inversa. Índices mais recentes são recuperados antes de índices mais antigos.

- Por nome de índice inversa. Índices que têm nomes em ordem alfanumérica maiores que outras pessoas serão restaurados primeiro.

Se alguns índices são mais importantes outras pessoas, mas não correspondem aos critérios que você pode substituir a precedência de índices definindo a propriedade *index.priority* . Índices com um valor maior desta propriedade serão recuperados antes de índices que têm um valor mais baixo:

```http
PUT low_priority_index
{
    "settings": {
        "index.priority": 1
    }
}

PUT high_priority_index
{
    "settings": {
        "index.priority": 10
    }
}
```

Para obter mais informações, consulte [Prioridade de recuperação de índice](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Você pode monitorar o processo de recuperação de um ou mais índices usando a * \_recuperação* API:

```http
GET /high_priority_index/_recovery?pretty=true
```

Para obter mais informações, consulte [Recuperação de índices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Um cluster com fragmentos que exigem recuperação terá um status de *amarelo* para indicar que fragmentos nem todas estão disponíveis atualmente. Quando todos os fragmentos estiverem disponíveis, o status do cluster deve reverter para *verde*. Um cluster com um status de *vermelho* indica que um ou mais fragmentos estão física ausentes, talvez seja necessário restaurar os dados de um backup.

## <a name="preventing-split-brain"></a>Evitando cérebro de divisão 

Um cérebro divisão pode ocorrer se as conexões entre nós falharem. Se um nó mestre ficar inacessível a parte do cluster, uma eleição ocorrerá no segmento de rede que permanece pode ser conectado e outro nó tornará o mestre. Em um cluster mal configurados, é possível para cada parte do cluster ter diferentes mestres resulta em inconsistências de dados ou corrupção. Esse fenômeno é conhecido como um *cérebro de divisão*.

Você pode reduzir as chances de um cérebro dividido Configurando o *mínimo\_mestre\_nós* propriedade do módulo descoberta, no arquivo elasticsearch.yml. Esta propriedade especifica quantos nós devem estar disponíveis para habilitar a eleição de um mestre. O exemplo a seguir define o valor desta propriedade para 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Este valor deve ser definido para a maioria mais baixa do número de nós que são capazes de fulfil a função de mestre. Por exemplo, se o seu cluster tem 3 nós mestres, *mínimo\_mestre\_nós* deve ser definida como 2. Se você tiver 5 nós mestres, *mínimo\_mestre\_nós* deve ser definida para 3. Ideal, você deve ter um número ímpar de nós mestres.

> [AZURE.NOTE] É possível para um cérebro de divisão ocorre se vários nós mestres no mesmo cluster são iniciados simultaneamente. Enquanto essa ocorrência é rara, você pode impedir que ele iniciando nós em série com um pequeno atraso (5 segundos) entre cada um deles. 

## <a name="handling-rolling-updates"></a>Atualizações sem interrupção de manipulação

Se você estiver executando uma atualização de software para nós por conta própria (como migrar para uma versão mais recente ou executar um patch), talvez você precise realizar o trabalho em nós individuais que requer levando-los offline mantendo o resto do cluster disponível. Nessa situação, considere a implementação o seguinte processo. 

1. Certifique-se de que realocação fragmentar está atrasada suficientemente para impedir que o mestre lecionado redistribuição fragmentos de um nó ausente entre o resto do cluster. Por padrão, realocação de fragmentar está atrasada por 1 minuto, mas você pode aumentar a duração se um nó é provavelmente estará disponível por um período maior. O exemplo a seguir aumenta o atraso para 5 minutos:

    ```http
    PUT /_all/_settings
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m"
        }
    }
    ```

    > [AZURE.IMPORTANT] Você também pode desativar realocação fragmentar completamente definindo a *cluster.routing.allocation.enable* do cluster para *Nenhum*. No entanto, evite usar essa abordagem caso novos índices são provavelmente serão criadas enquanto o nó estiver offline, pois isso pode causar alocação de índice falha resulta em um cluster com status vermelho.

2. Pare Elasticsearch no nó a ser mantida. Se estiver executando Elasticsearch como um serviço, você poderá interromper o processo de uma maneira controlada usando um comando de sistema operacional. O exemplo a seguir mostra como parar o serviço de Elasticsearch em um único nó em execução no Ubuntu:

    ```bash
    service elasticsearch stop
    ```

    Como alternativa, você pode usar a API de desligamento diretamente no nó:

    ```http
    POST /_cluster/nodes/_local/_shutdown
    ```

3. Realizar a manutenção necessária no nó

4. Reinicie o nó e aguarde a ingressar no cluster.

5. Alocação de fragmentar reabilitar:

    ```http
    PUT /_cluster/settings
    {
        "transient": {
            "cluster.routing.allocation.enable": "all"
        }
    }
    ```

> [AZURE.NOTE] Se você precisar manter mais de um nó, repita as etapas 2&ndash;4 em cada nó antes de habilitar novamente o alocação fragmentar.

Se puder, interrompa a indexação novos dados durante este processo. Isso ajudará a minimizar o tempo de recuperação quando nós estão online novamente e reingressam no cluster.

Lembre-se de atualizações automáticas para itens como a JVM (o ideal desativar atualizações automáticas para esses itens), especialmente quando executando Elasticsearch sob Windows. O agente de atualização de Java pode baixar a versão mais recente do Java automaticamente, mas pode exigir Elasticsearch ser reiniciado para que a atualização seja efetivada. Isso pode resultar em perda temporária não coordenada de nós, dependendo de como o Java Update agent está configurado. Isso também pode resultar em diferentes instâncias do Elasticsearch no mesmo cluster executando versões diferentes do JVM que podem causar problemas de compatibilidade.

## <a name="testing-and-analyzing-elasticsearch-resilience-and-recovery"></a>Teste e analisando recuperação e resiliência de Elasticsearch

Esta seção descreve uma série de testes que foram executadas para avaliar a resiliência e a recuperação de um cluster de Elasticsearch que contém três nós de dados e três mestre.

Cenários a seguir foram testados: 

- Falha de nó e reinicie sem perda de dados. Um nó de dados for interrompido e reiniciado após 5 minutos. Elasticsearch foi configurado para não realocar fragmentos ausentes nesse intervalo, para que nenhuma e/s adicional é incorridos no percorrer fragmentos. Quando o nó for reiniciado, o processo de recuperação traz os fragmentos nesse nó volta atualizado.

- Falha de nó com perda de dados grave. Um nó de dados for interrompido e os dados que ele contém são apagados para simular falha grave disco. O nó é, em seguida, reiniciado (após 5 minutos), efetivamente atuando como substituto para o nó original. O processo de recuperação requer recriar os dados ausentes para este nó e pode envolver realocação fragmentos contidos em outros nós.

- Falha de nó e reinicie sem perda de dados, mas com realocação fragmentar. Um nó de dados for interrompido e os fragmentos que mantém são realocados para outros nós. O nó é reiniciado e realocação mais ocorre para rebalancear cluster.

- Sem interrupção atualizações. Cada nó do cluster for interrompido e reiniciado após um pequeno intervalo para simular máquinas sendo reinicializadas após uma atualização de software. Somente um nó está parado em qualquer momento. Fragmentos não são realocados enquanto um nó está inoperante.

Cada cenário foi sujeito a carga de trabalho mesma, incluindo uma mistura de consultas de filtro, agregações e tarefas de inclusão de dados enquanto nós foram tiradas offline e recuperados. Em massa Inserir operações em cada armazenados documentos de 1000 e foram realizadas em relação a um índice enquanto as agregações a carga de trabalho e consultas de filtro usado um índice separado contendo milhões de vários documentos. Isso era permitir o desempenho das consultas a ser avaliada separadamente das inserções em massa. Cada índice contido cinco fragmentos e uma réplica.

As seções a seguir resumem os resultados desses testes, observando qualquer degradação no desempenho enquanto um nó está offline ou sendo recuperado e quaisquer erros que foram relatados. Os resultados são apresentados graficamente, realce os pontos em que um ou mais nós estão faltando e estimando o tempo necessário para o sistema para recuperar totalmente e obter um nível semelhante de desempenho que estava presente antes dos nós ficar offline.

> [AZURE.NOTE] As testam utilizações usadas para realizar esses testes estão disponíveis online. Você pode adaptar e usar estas aproveita para verificar a resiliência e a capacidade de recuperação de suas próprias configurações de cluster. Para obter mais informações, consulte a [execução dos testes de resiliência Elasticsearch automatizados][].

## <a name="node-failure-and-restart-with-no-data-loss-results"></a>Falha de nó e reinicie sem perda de dados: resultados

<!-- TODO; reformat this pdf for display inline --> 

Os resultados deste teste são mostrados no arquivo [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario1.pdf). Os gráficos mostram o perfil de desempenho da carga de trabalho e os recursos físicos para cada nó no cluster. A parte inicial dos gráficos mostram o sistema executando normalmente para aproximadamente 20 minutos, ponto em que nó 0 é desligado por 5 minutos antes de ser reiniciado. As estatísticas por um mais de 20 minutos são ilustradas; o sistema leva cerca de 10 minutos para recuperar e coloque. Isso é ilustrado pela taxas de transação e tempos de resposta para as cargas de trabalho diferentes.

Observe os seguintes pontos:

- Durante o teste, sem erros foram relatados. Nenhum dado foi perdido e todas as operações foi concluída com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta agregada e filtro de consulta) descartados e os tempos de resposta médio aumentado enquanto nó 0 estava offline.

- Durante o período de recuperação, as taxas de transação e tempos de resposta para a consulta agregada e a consulta de filtro operações gradualmente foram restauradas. O desempenho de inserção em massa recuperados por um curto enquanto antes diminuindo. Entretanto, provavelmente devido ao volume de dados causando o índice usado pela inserção em massa a crescer e as taxas de transação para esta operação podem ser vistas lento mesmo antes de nó 0 é colocado offline.

- O gráfico de utilização de CPU para nó 0 mostra atividade reduzida durante a fase de recuperação, isso é devido o disco maiores e causada pelo mecanismo de recuperação, o nó de atividade de rede tem acompanhar com quaisquer dados que ele tenha perdido enquanto ela estiver offline e atualizar os fragmentos que ele contém.

- Os fragmentos para os índices não são distribuídos exatamente igualmente em todos os nós. Há dois índices contendo 5 fragmentos e cada, 1 réplica fazendo um total de 20 fragmentos. Dois nós, portanto, conterá 6 fragmentos enquanto as outras duas mantenha 7 cada. Isso é evidente nos gráficos de utilização de CPU durante o período de 20 minutos inicial, nó 0 é menos ocupado do que as outras duas. Após a recuperação é concluída, alguns migrando parece ocorrer como nó 2 aparece se tornar o nó mais levemente carregado.

    
## <a name="node-failure-with-catastrophic-data-loss-results"></a>Falha de nó com perda de dados grave: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados deste teste são descritos no arquivo [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario2.pdf). Como com o primeiro teste, a parte inicial dos gráficos mostra o sistema executando normalmente por cerca de 20 minutos, no qual nó ponto 0 é desligar por 5 minutos. Durante esse intervalo, os dados Elasticsearch neste nó são removidos, simular a perda de dados grave, antes de ser reiniciado. Recuperação total parece levar 12-15 minutos antes dos níveis de desempenho visto antes do teste são restaurados.

Observe os seguintes pontos:

- Durante o teste, sem erros foram relatados. Nenhum dado foi perdido e todas as operações foi concluída com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta agregada e filtro de consulta) descartados e os tempos de resposta médio aumentado enquanto nó 0 estava offline. Neste ponto, o perfil de desempenho do teste é semelhante ao primeiro cenário. Isso não é surpresa como, neste ponto, os cenários são as mesmas.

- Durante o período de recuperação, as taxas de transação e tempos de resposta foram restaurados, embora durante esse tempo houve volatilidade muito mais nas figuras. Este é provavelmente devido ao trabalho adicional que os nós no cluster estão executando, fornecendo dados para restaurar os fragmentos ausentes. Este trabalho adicional é evidente na utilização da CPU, atividade do disco e gráficos de atividade de rede.

- O gráfico de utilização de CPU para nós 0 e 1 mostra atividade reduzida durante a fase de recuperação, que isso é devido a disco maiores e a atividade de rede causado pelo processo de recuperação. No primeiro cenário, somente o nó sendo recuperado apresentado esse comportamento, mas neste cenário parece provável que a maioria dos dados ausentes para nó 0 está sendo restaurado de nó 1.

- A atividade de e/s de nó 0 é realmente reduzida em comparação com o primeiro cenário. Isso pode ser devido a eficiência e/s de simplesmente copiar os dados para um inteiro fragmentar em vez da série de solicitações de i menores necessárias para que um fragmentar existente atualizado.

- A atividade de rede para todos os três nós indicam picos de atividade como dados transmitidos e recebidos entre nós. Cenário 1, somente nó 0 apresentado como muito atividade de rede, mas esta atividade pareceu sejam mantidos por um período maior. Novamente, essa diferença pode ser devido a eficiência de transmissão de dados inteiros para um fragmentar como uma única solicitação em vez da série de menores solicitações recebidas ao recuperar uma fragmentar.

## <a name="node-failure-and-restart-with-shard-reallocation-results"></a>Falha de nó e reiniciar com realocação fragmentar: resultados

<!-- TODO; reformat this pdf for display inline -->

O arquivo [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario3.pdf) ilustra os resultados deste teste. Como com o primeiro teste, a parte inicial dos gráficos mostram o sistema executando normalmente por cerca de 20 minutos, no qual nó ponto 0 é desligar por 5 minutos. Neste ponto, o cluster Elasticsearch tenta recriar os fragmentos ausentes e elimine os fragmentos em nós restantes. Após o nó de 5 minutos 0 é colocado online e novamente o cluster tem que reequilibrar os fragmentos. Desempenho é restaurado após 12-15 minutos.

Observe os seguintes pontos:

- Durante o teste, sem erros foram relatados. Nenhum dado foi perdido e todas as operações foi concluída com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta agregada e filtro de consulta) descartados e os tempos de resposta médio aumentaram significativamente enquanto nó 0 estava offline em comparação com os dois testes anterior. Isso é devido a atividade de cluster maior recriar os fragmentos ausentes e redistribuição cluster devido aos valores de elevado de atividade de disco e de rede para nós 1 e 2 neste período.

- Durante o período depois nó 0 é colocado online, as taxas de transação e tempos de resposta permanecem voláteis.

- Os gráficos da CPU utilização e disco atividade mostra nó 0 muito reduzida ação inicial durante a fase de recuperação. Isso ocorre porque neste ponto, nó 0 não está atendendo a todos os dados. Após um período de aproximadamente 5 minutos, o nó ruptura em ação < RBC: isso me fez snort em voz alta. Eu não sou chegando com uma melhor maneira de dizer isso apesar.  >> conforme exibido pelo aumento súbito na rede, disco e atividade da CPU. Isso provavelmente é causado pelo cluster redistribuir fragmentos em nós. Nó 0 mostra atividade normal.
  
## <a name="rolling-updates-results"></a>Sem interrupção atualizações: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados deste teste, no arquivo [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario4.pdf), mostram como cada nó é colocada offline e, em seguida, trouxe de volta para cima novamente sucessivamente. Cada nó está desligado por 5 minutos antes de ser reiniciado ponto em que o próximo nó em sequência for interrompido.

Observe os seguintes pontos:

- Embora cada nó é alternado, o desempenho em termos de tempos de produtividade e resposta permanece razoavelmente mesmo.

- Atividade de disco aumenta para cada nó por um período curto como ele é colocado online. Este é provavelmente devido ao processo de recuperação avance quaisquer alterações que ocorreram enquanto o nó estava pressionada.

- Quando um nó ficar offline, picos de atividade de rede ocorrerem em nós restantes. Picos também ocorrerem quando um nó for reiniciado.

- Após o nó final reciclado, o sistema insere um período de volatilidade significativo. Isso provavelmente é causado pelo processo de recuperação precisar sincronizar alterações entre cada nó e certifique-se de que todas as réplicas e seus fragmentos correspondentes são consistentes. Em um ponto, este massa sucessivas de causas esforço Inserir operações de tempo limite e falhar. Os erros relatados cada caso foram:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Experimento subsequente mostrava que apresentando um atraso de alguns minutos entre ciclo cada nó eliminada esse erro, para que ela provavelmente foi causado por disputa entre o processo de recuperação tentando restaurar vários nós simultaneamente e em massa Inserir operações tentando armazenar milhares de documentos novos.


## <a name="summary"></a>Resumo

Os testes executados indicaram que:

- Elasticsearch foi altamente flexível para os modos mais comuns de falha probabilidade de ocorrer em um cluster.

- Elasticsearch pode recuperar rapidamente se um cluster elaborado estiver sujeito a perda de dados grave em um nó. Isso pode acontecer se você configurar Elasticsearch para salvar os dados para o armazenamento efêmero e o nó subsequentemente está provisionado novamente após a reinicialização. Esses resultados mostram que mesmo nesse caso, os riscos de usar o armazenamento efêmero são provavelmente compensadas pelos benefícios de desempenho que fornece essa classe de armazenamento.

- No primeiro três cenários, sem erros em Inserir em massa simultâneos, agregação e cargas de trabalho de consulta de filtro a um nó foi feito offline e recuperados.

- Somente o último cenário indicada possibilidade de perda de dados, e essa perda afetado somente novos dados sendo adicionados. É recomendável em aplicativos realizando a inclusão de dados a reduzir essa probabilidade por repetindo operações de inserção com falha conforme o tipo de erro relatado é altamente probabilidade de serem temporárias.

- Os resultados do último teste também mostram que se você estiver executando manutenção planejada de nós em um cluster, desempenho irá se beneficiar se você permitir vários minutos entre ciclo de um nó e a próxima. Em uma situação planejada (como data center reciclagem nós após executar uma atualização do sistema operacional), você tem menos controle sobre como e quando nós são feitas para baixo e reiniciados. O disputa que surge quando Elasticsearch tenta recuperar o estado do cluster após interrupções de nó sequenciais pode resultar em erros e tempos limite. 

[Gerenciar a disponibilidade de máquinas virtuais]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[Execução dos testes de resiliência Elasticsearch automatizada]: guidance-elasticsearch-running-automated-resilience-tests.md
