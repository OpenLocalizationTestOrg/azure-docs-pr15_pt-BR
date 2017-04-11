<properties 
    pageTitle="Como monitorar Azure relacionada Cache | Microsoft Azure" 
    description="Aprenda a monitorar a integridade e o desempenho suas instâncias de Cache relacionada do Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Como monitorar Cache relacionada do Azure

Cache de relacionada Azure fornece várias opções para monitoramento suas instâncias de cache. Você pode exibir métricas, fixar gráficos de métricas para o Startboard, personalizar o intervalo de data e hora de monitoramento gráficos, adicionar remover métricas dos gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem que você monitorar a integridade de suas instâncias de Cache relacionada do Azure e ajudá-lo a gerenciar seus aplicativos de cache.

Quando o diagnóstico de cache está ativado, métricas para instâncias Azure relacionada Cache são coletadas aproximadamente a cada 30 segundos e armazenadas para que eles podem ser exibidos nos gráficos métricas e avaliados por regras de alerta.

Medições de cache são coletadas usando o relacionada comando [INFO](http://redis.io/commands/info) . Para obter mais informações sobre as informações de diferentes valores usados para cada métrica em cache, consulte [métricas disponíveis e intervalos de relatório](#available-metrics-and-reporting-intervals).

Para exibir métricas de cache, [Procure](cache-configure.md#configure-redis-cache-settings) sua instância de cache no [portal do Azure](https://portal.azure.com). Métricas de instâncias do Azure relacionada Cache são acessadas na lâmina **relacionada métricas** .

![Relacionada métricas][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Se a seguinte mensagem for exibida na lâmina **relacionada métricas** , siga as etapas na seção [Habilitar o diagnóstico de cache](#enable-cache-diagnostics) para habilitar o diagnóstico de cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

A lâmina **relacionada métricas** tem gráficos de **monitoramento** que exibem métricas do cache. Cada gráfico pode ser personalizado adicionando ou removendo métricas e alterando o intervalo de relatórios. Para exibir e configurar operações e alertas, a lâmina de **Cache relacionada** tem uma seção de **operações** que exibe o cache de **eventos** e **regras de alerta**.

## <a name="enable-cache-diagnostics"></a>Habilitar o diagnóstico de cache

Cache de relacionada Azure oferece a capacidade de ter dados de diagnóstico armazenados em uma conta de armazenamento para que você pode usar as ferramentas que você deseja acessar e processar os dados diretamente. Em ordem para diagnósticos de cache a serem coletados, armazenados e exibidas no portal do Azure, uma conta de armazenamento deverá ser configurada. Caches na mesma região e assinatura compartilharem a mesma conta de armazenamento de diagnóstico e quando a configuração é alterada-se aplica a todos os caches na assinatura que estão na região.

Para habilitar e configurar diagnósticos de cache, navegue até a lâmina de **Cache relacionada** para a sua instância de cache. Se o diagnóstico ainda não está ativado, uma mensagem será exibida em vez de um gráfico de diagnóstico.

![Habilitar o diagnóstico de cache][redis-cache-enable-diagnostics]

Clique na mensagem para exibir a lâmina **métrica** e clique em **configurações de diagnósticos** para habilitar e configurar as configurações de diagnósticos para a instância do serviço de cache.

![Configurações de diagnóstico][redis-cache-diagnostic-settings]

![Configurar diagnósticos][redis-cache-configure-diagnostics]

Clique **no** botão para habilitar o diagnóstico de cache e exibir a configuração de diagnóstico.

Clique na seta à direita da **Conta de armazenamento** para selecionar uma conta de armazenamento para armazenar dados de diagnóstico. Para obter melhor desempenho, selecione uma conta de armazenamento na mesma região como seu cache.

Quando as configurações de diagnósticos estão configuradas, clique em **Salvar** para salvar a configuração. Observe que pode levar alguns minutos para que as alterações tenham efeito.

>[AZURE.IMPORTANT] Caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico e quando a configuração é alterado (Diagnóstico ativado/desativado ou alterando a conta de armazenamento)-se aplica a todos os caches na assinatura que estão na região.

Para exibir as métricas armazenadas, examine as tabelas em sua conta de armazenamento com nomes que começam com `WADMetrics`. Para obter mais informações sobre como acessar as métricas armazenadas fora do portal do Azure, consulte a amostra de [dados de monitoramento de Cache acesso relacionada](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Apenas métricas que estão armazenadas na conta de armazenamento selecionado são exibidas no portal do Azure. Se você alterar contas de armazenamento, os dados na conta de armazenamento configurado anteriormente permanecerão disponíveis para download, mas não é exibida no portal do Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de relatório

Métricas do cache são relatadas usando vários intervalos de relatórios, incluindo a **última hora**, **dia**, **semana passada**e **personalizado**. A lâmina **métrica** para cada gráfico métricas exibe os valores de médio, mínimos e máximos para cada métrica no gráfico e algumas métricas exibem um total para o intervalo de relatório. 

Cada métrica inclui duas versões. Uma medida medidas de desempenho para todo o cache e para caches que usam o [agrupamento](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no desempenho nome medidas para um único fragmentar em cache. Por exemplo, se um cache tiver 4 fragmentos, `Cache Hits` é a quantidade total de visitas para todo o cache, e `Cache Hits (Shard 3)` é apenas as visitas para que fragmentar do cache.

>[AZURE.NOTE] Mesmo quando o cache está ocioso sem aplicativos cliente ativo conectado, você poderá ver algumas atividades de cache, como clientes conectados, uso de memória e operações estão sendo executadas. Esta atividade é normal durante a operação de uma instância de Cache relacionada do Azure.

| Métrica            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorrências de cache        | O número de pesquisas de chave bem-sucedida durante o intervalo de relatório especificado. Isso mapeia para `keyspace_hits` da [informações](http://redis.io/commands/info) relacionada de comando.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Erros de cache      | O número de pesquisas de chave falhou durante o intervalo de relatório especificado. Isso mapeia para `keyspace_misses` do comando INFO relacionada. Erros de cache não necessariamente que há um problema com o cache. Por exemplo, ao usar o padrão de programação de cache-aside, um aplicativo procura primeiro no cache de um item. Se o item não estiver lá (Falha de cache), o item é recuperado do banco de dados e adicionado ao cache para a próxima vez. Erros de cache são comportamento normal para o cache-aside programação padrão. Se o número de erros de cache for maior que o esperado, examine a lógica de aplicativo que preenche e lê do cache. Se itens estão sendo removidos do cache devido a pressão de memória, pode haver alguns erros de cache, mas uma métrica melhor para monitorar a pressão de memória seria `Used Memory` ou `Evicted Keys`. |
| Clientes conectados | O número de conexões de cliente com o cache durante o intervalo de relatório especificado. Isso mapeia para `connected_clients` do comando INFO relacionada. Depois que o [limite de conexão](cache-configure.md#default-redis-server-configuration) é alcançado tentativas subsequentes de conexão para o cache falhará. Observe que mesmo se não houver nenhum aplicativo cliente ativo, pode ainda haver alguns casos de clientes conectados devido a conexões e processos internos.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Chaves removidas      | O número de itens removido do cache durante o intervalo de relatório especificado devido ao `maxmemory` limite. Isso mapeia para `evicted_keys` do comando INFO relacionada.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Chaves expiradas      | O número de itens expirados do cache durante o intervalo de relatório especificado. Este valor mapeia para `expired_keys` do comando INFO relacionada.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Obtém              | O número de operações de get do cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores das informações de relacionada comando tudo: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, e `cmdstat_getrange`e é equivalente a soma de acessos e cache erros durante o intervalo de relatório.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Relacionada a carga do servidor | A porcentagem de ciclos em que o servidor relacionada está ocupado processando e não aguardando ocioso para mensagens. Se este contador atingir 100 significa que o servidor relacionada atingiu um teto de desempenho e não é possível processar a CPU trabalhe qualquer mais rápido. Se você estiver vendo alta relacionada a carga do servidor, em seguida, você verá exceções de tempo limite no cliente. Nesse caso, você deve considerar dimensionamento para cima ou partição dos dados em vários caches.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Conjuntos              | O número de operações de conjunto para o cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores das informações de relacionada comando tudo: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, e `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Total de operações  | O número total de comandos processados pelo servidor cache durante o intervalo de relatório especificado. Este valor mapeia para `total_commands_processed` do comando INFO relacionada. Observe que quando Azure relacionada Cache é usado apenas para pub/sub não haverá nenhum métricas para `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mas haverá `Total Operations` métricas que reflitam o uso de cache para operações de pub/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Memória usada       | A quantidade de memória de cache usada para pares de chave/valor no cache em MB durante o intervalo de relatório especificado. Este valor mapeia para `used_memory` do comando INFO relacionada. Isso não inclui metadados ou fragmentação.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Memória usada RSS   | A quantidade de memória de cache usada em MB durante o intervalo de relatório especificado, incluindo fragmentação e metadados. Este valor mapeia para `used_memory_rss` do comando INFO relacionada.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | Utilização da CPU do servidor do Azure relacionada Cache como uma porcentagem durante o intervalo de relatório especificado. Este valor mapeia para o sistema operacional `\Processor(_Total)\% Processor Time` contador de desempenho.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Leitura de cache        | A quantidade de dados lidas do cache em Megabytes por segundo (MB/s) durante o intervalo de relatório especificado. Esse valor é derivado dos cartões de interface de rede que oferecem suporte a máquina virtual que hospeda o cache e não está relacionada específico. **Esse valor corresponde a largura de banda de rede usado por esse cache. Se você quiser configurar alertas para limites de largura de banda de rede de lado do servidor, então criá-la usando essa `Cache Read` contador. Consulte [Esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observado para cache de vários níveis e tamanhos de preços.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Gravação de cache       | A quantidade de dados gravados do cache em Megabytes por segundo (MB/s) durante a especificado relatórios intervalo. Esse valor é derivado dos cartões de interface de rede que oferecem suporte a máquina virtual que hospeda o cache e não está relacionada específico. Esse valor corresponde a largura de banda de rede de dados enviados para o cache do cliente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Como exibir métricas e personalizar gráficos

Você pode exibir uma visão geral das métricas para seu cache na lâmina **relacionada métricas** . Para acessar o **relacionada métricas** blade escolha **todas as configurações** > **relacionada métricas**.

![Relacionada métricas][redis-cache-redis-metrics]


A lâmina **relacionada métricas** contém os gráficos a seguir.

| Gráfico de métricas de relacionada | Métricas exibidas     |
|------------------|-------------------|
| Cache de leitura e o Cache de gravação | Leitura de cache |
|                            | Gravação de cache |
| Clientes conectados      | Clientes conectados |
| Visitas e erros  | Ocorrências de cache        |
|                  | Erros de cache      |
| Total de comandos   | Total de operações  |
| Obtém e conjuntos    | Obtém              |
|                  | Conjuntos              |
| Uso da CPU         | CPU           |
| Uso da memória      | Memória usada   |
|                   | Memória usada RSS |
| Relacionada a carga do servidor | Carga do servidor   |
| Contagem de chave | Total de chaves |
|           | Chaves removidas |
|           | Chaves expiradas |


Para obter uma exibição mais detalhada das métricas em um gráfico específico e para personalizar o gráfico, clique no gráfico desejado da lâmina **relacionada métricas** para exibir a lâmina **métrica** desse gráfico.

![Blade métrica][redis-cache-metric-blade]

Todos os alertas que são definidos em métricas exibidas por um gráfico estão listados na parte inferior da lâmina **métrica** desse gráfico.

Para adicionar ou remover métricas ou alterar o intervalo de relatório, escolha **Editar gráfico**.

Para adicionar ou remover métricas do gráfico, clique na caixa de seleção ao lado do nome da métrica. Para alterar o intervalo de relatórios, clique no intervalo desejado. Para alterar o **tipo de gráfico**, clique no estilo desejado. Depois que são feitas as alterações desejadas, clique em **Salvar**. 

![Editar gráfico][redis-cache-edit-chart]

Quando você clicar em **Salvar** suas alterações irá persistir até você deixar a lâmina **métrica** . Quando você voltar mais tarde, você verá a métrica original e o intervalo de tempo novamente. Para obter mais informações sobre como personalizar gráficos, consulte [métricas de serviço do Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para visualizar as métricas de um período específico em um gráfico, passe o mouse sobre uma das barras específicas ou pontos no gráfico que corresponde ao tempo desejado e as métricas para intervalo são exibidas.

![Exibir detalhes do gráfico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Como monitorar um cache premium com clusters

Caches Premium que tem [cluster](cache-how-to-premium-clustering.md) habilitado podem ter até 10 fragmentos. Cada fragmentar tem suas próprias métricas e essas métricas são agregadas para fornecer métricas para o cache como um todo. Cada métrica inclui duas versões. Uma medida medidas de desempenho para todo o cache e uma segunda versão da métrica que inclui `(Shard 0-9)` no desempenho nome medidas para um único fragmentar em cache. Por exemplo, se um cache tem 3 fragmentos, `Cache Hits` é a quantidade total de visitas para todo o cache, e `Cache Hits (Shard 2)` é apenas as visitas para que fragmentar do cache.

Cada gráfico monitoramento exibe as métricas de nível superior para o cache juntamente com as métricas para cada fragmentar de cache.

![Monitor][redis-cache-premium-monitor]

Posicionar o mouse sobre os pontos de dados exibe os detalhes para esse ponto no tempo. 

![Monitor][redis-cache-premium-point-summary]

Os valores maiores geralmente são os valores agregados para o cache enquanto os menores valores são as métricas individuais para o fragmentar. Observe que neste exemplo, há três fragmentos e o cache de visitas são distribuídas uniformemente entre os fragmentos.

![Monitor][redis-cache-premium-point-shard]

Para ver mais detalhes clique no gráfico para exibir um modo de exibição expandido na lâmina **métrica** .

![Monitor][redis-cache-premium-chart-detail]

Por padrão, cada gráfico inclui o contador de desempenho do cache de nível superior, bem como os contadores de desempenho para os fragmentos individuais. Você pode personalizá-los na lâmina **Editar gráfico** .

![Monitor][redis-cache-premium-edit]

Para obter mais informações sobre os contadores de desempenho disponíveis, consulte [métricas disponíveis e intervalos de relatório](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Operações e alertas

A seção de **operações** na lâmina **Cache relacionada** tem seções de **eventos** e **regras de alerta** .

![Oeprations][redis-cache-operations-events]

Para ver uma lista das operações de cache recentes, clique no gráfico de **eventos** para exibir a lâmina de **eventos** . Recuperando e recriando teclas de acesso e a ativação e a resolução de regras de alerta são exemplos de operações. Para obter mais informações sobre cada evento, clique no evento na lâmina **eventos** .

Para obter mais informações sobre eventos, consulte [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md).

A seção de **regras de alerta** exibe a contagem de alertas para a instância de cache. Uma regra de alerta permite monitorar sua instância de cache e receber um email sempre que um determinado valor métrico atinge o limite definido na regra. 

Regras de alerta são avaliadas aproximadamente a cada cinco minutos, e quando uma regra de alerta é ativada, qualquer notificações configuradas são enviadas. Notificações e ativações de regra de alerta não são processadas instantaneamente; pode haver um atraso de alguns minutos antes de uma regra de alerta está ativada e notificações enviadas.

Regras de alerta podem ser visualizadas e definir a lâmina **métrica** para um gráfico de monitoramento específico da lâmina **regras de alerta** .

Regras de alerta tem as seguintes propriedades.

| Propriedade regra de alerta                 | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso                            | O recurso avaliado pela regra de alerta. Ao criar uma regra de alerta de um cache relacionada, o cache é um recurso.                                                                                                                                                                                                                                                                                                                                                  |
| Nome                                | Nome que identifica exclusivamente a regra de alerta dentro da instância atual do cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| Descrição                         | Descrição opcional da regra de alerta.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Métrica                              | A métrica monitorar pela regra de alerta. Para obter uma lista de métricas de cache, consulte métricas disponíveis e intervalos de relatório.                                                                                                                                                                                                                                                                                                                                             |
| Condição                           | O operador de condição da regra do alerta. Possíveis opções são: maior que, maior que ou igual a, menor que, menor que ou igual a                                                                                                                                                                                                                                                                                                                             |
| Limite                           | O valor usado para comparar com a métrica usando o operador especificado pela propriedade condição. Dependendo a métrica, esse valor pode estar em bytes/segundo, bytes, % ou contagem.                                                                                                                                                                                                                                                                                     |
| Período                              | Especifica o período em que o valor médio da métrica é usado para a comparação de regra de alerta. Por exemplo, se o período estiver sobre a última hora, o valor médio da métrica sobre o intervalo de hora anterior é usado para a comparação. Se quiser ser notificado quando o limite é atendido devido a um aumento na atividade, um período mais curto é apropriado. Para ser notificado quando houver atividade contínua acima do limite, use um período maior. |
| Serviço de email e coadministradores | Quando for verdadeiro, o administrador do serviço e o administrador colegas são enviados por e-mail quando o alerta é ativado.                                                                                                                                                                                                                                                                                                                                                                    |
| Email do administrador adicional      | Endereço de email opcional de administrador adicional para ser notificado quando o alerta é ativado.                                                                                                                                                                                                                                                                                                                                                                    |

Apenas uma notificação é enviada por ativação de regra de alerta. Depois que o limite para uma regra é excedido e uma notificação é enviada, a regra não é avaliada novamente até que a métrica cai abaixo do limite. Se a métrica subsequentemente exceder o limite, o alerta é reativado e uma nova notificação é enviada.

Para exibir todas as regras de alerta para a sua instância de cache, clique na parte de **regras de alerta** na lâmina **Cache relacionada** . Para exibir apenas as regras de alerta que usam uma métrica específica, navegue até a lâmina **métrica** para o gráfico que contém métrica.

![Regras de alerta][redis-cache-alert-rules]

Para adicionar uma regra de alerta, clique em **Adicionar alerta** de lâmina **métrica** ou a lâmina de **regras de alerta** . 

Insira os critérios de regra desejado na lâmina de regra de **Adicionar um alerta** e clique em **Okey**. 

![Adicionar regra de alerta][redis-cache-add-alert]

>[AZURE.NOTE] Quando uma regra de alerta é criada clicando em **Adicionar alerta** da lâmina **métrica** , somente as métricas exibidas no gráfico em que lâmina aparecem na lista suspensa **métrica** . Quando uma regra de alerta é criada clicando em **Adicionar alerta** da lâmina **regras de alerta** , todas as métricas de cache estão disponíveis no menu suspenso **métrica** .

Depois que uma regra de alerta é salvo ele aparece no blade **regras de alerta** , bem como na lâmina **métrica** para qualquer gráficos que exibem a métrica usada na regra de alerta. Para editar uma regra de alerta, clique no nome da regra de alerta para exibir a lâmina de **Editar regra** . Da lâmina **Editar regra** que você pode editar as propriedades da regra, excluir ou desabilitar a regra de alerta ou reativar a regra se ela foi desabilitada anteriormente.

>[AZURE.NOTE] Quaisquer alterações feitas nas propriedades da regra podem demorar um pouco antes que eles sejam refletidos na lâmina de **regras de alerta** ou a lâmina **métrica** .

Quando uma regra de alerta é ativada, um email será enviado dependendo a configuração da regra de alerta e um ícone de alerta é exibido na parte de **regras de alerta** no blade **Cache relacionada** .

Uma regra de alerta é considerada ser resolvidos quando a condição de alerta já não é avaliada como true. Depois que a condição de regra de alerta for resolvida, o ícone de alerta é substituído por uma marca de seleção. Para obter detalhes sobre ativações alertas e resoluções, clique na parte de **eventos** no blade **Relacionada Cache** para exibir os eventos na lâmina **eventos** .

Para obter mais informações sobre alertas no Azure, consulte [notificações de alerta de recebimento](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Métricas na lâmina Cache relacionada

A lâmina de **Cache relacionada** exibe as seguintes categorias de métricas.

-   [Gráficos de monitoramento](#monitoring-charts)
-   [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitoramento

A seção de **monitoramento** tem **visitas e erros**, **obtém e define**, **conexões**e gráficos de **Comandos Total** .

![Gráficos de monitoramento][redis-cache-monitoring-part]

Os gráficos de **monitoramento** exibem as métricas a seguir.

| Gráfico de monitoramento | Métricas de cache     |
|------------------|-------------------|
| Visitas e erros  | Ocorrências de cache        |
|                  | Erros de cache      |
| Obtém e conjuntos    | Obtém              |
|                  | Conjuntos              |
| Conexões      | Clientes conectados |
| Total de comandos   | Total de operações  |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção de [como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) a seguir.

### <a name="usage-charts"></a>Gráficos de uso

A seção de **uso** tem **Relacionada a carga do servidor**, **O uso da memória**, **Largura de banda de rede**e gráficos de **Uso da CPU** e também exibe o **nível de preços** para a instância de cache.

![Gráficos de uso][redis-cache-usage-part]

Exibe o **nível de preços** os preços de cache nível e pode ser usado para [Dimensionar](cache-how-to-scale.md) o cache para um nível de preços diferentes.

Os gráficos de **uso** exibem as métricas a seguir.

| Gráfico de uso       | Métricas de cache |
|-------------------|---------------|
| Relacionada a carga do servidor | Carga do servidor   |
| Uso da memória      | Memória usada   |
| Largura de banda de rede | Gravação de cache   |
| Uso da CPU         | CPU           |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção de [como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) a seguir.
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



