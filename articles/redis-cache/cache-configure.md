<properties 
    pageTitle="Como configurar o Azure relacionada Cache | Microsoft Azure"
    description="Entender a configuração relacionada padrão para Cache relacionada do Azure e aprenda a configurar suas instâncias de Cache relacionada do Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Como configurar o Cache de relacionada do Azure

Este tópico descreve como rever e atualizar a configuração para suas instâncias de Cache relacionada do Azure e discute a configuração padrão relacionada server para instâncias de Cache relacionada do Azure.

>[AZURE.NOTE] Para obter mais informações sobre como configurar e usar recursos premium do cache, consulte [como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md), [como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md)e [como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Definir configurações de cache relacionada

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Cache de relacionada Azure fornece as seguintes configurações na lâmina **configurações** .

![Configurações de Cache de relacionada](./media/cache-configure/redis-cache-settings.png)



-   [Suporte e solução de problemas de configurações](#support-amp-troubleshooting-settings)
-   [Configurações gerais](#general-settings)
    -   [Propriedades](#properties)
    -   [Teclas de acesso](#access-keys)
    -   [Configurações avançadas](#advanced-settings)
    -   [Relacionada Supervisor de Cache](#redis-cache-advisor)
-   [Configurações de escala](#scale-settings)
    -   [Nível de preço](#pricing-tier)
    -   [Tamanho de cluster de relacionada](#cluster-size)
-   [Configurações de gerenciamento de dados](#data-management-settings)
    -   [Relacionada persistência de dados](#redis-data-persistence)
    -   [Importação/exportação](#importexport)
-   [Configurações de administração](#administration-settings)
    -   [Reinicialização](#reboot)
    -   [Agendar atualizações](#schedule-updates)
-   [Configurações de diagnóstico](#diagnostics-settings)
-   [Configurações de rede](#network-settings)
-   [Configurações de gerenciamento de recursos](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Suporte e solução de problemas de configurações

As configurações na seção **suporte + solução de problemas** fornecem opções de resolução de problemas com o cache.

![Suporte + solução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

Clique em **diagnosticar e resolver problemas** ser fornecido com problemas comuns e estratégias para solucioná-los.

Clique em **log de atividade** para exibir ações executadas em seu cache. Você também pode usar a filtragem para expandir este modo de exibição para incluir outros recursos. Para obter mais informações sobre como trabalhar com logs de auditoria, consulte [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) e [operações com o Gerenciador de recursos de auditoria](../resource-group-audit.md). Para obter mais informações sobre como monitorar eventos de Cache relacionada do Azure, consulte [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

**Integridade do recurso** inspeções seus recursos e informa se ele está funcionando conforme esperado. Para obter mais informações sobre o serviço de integridade do recurso do Azure, consulte [Visão geral de integridade do recurso do Azure](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Integridade do recurso é atualmente não é possível informar sobre a integridade de instâncias do Azure relacionada Cache hospedado em uma rede virtual. Para obter mais informações, consulte [todos os recursos de cache funcionam ao hospedar um cache em um VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Clique em **nova solicitação de suporte** para abrir uma solicitação de suporte para o cache.

## <a name="general-settings"></a>Configurações gerais

As configurações na seção **Geral** permitem acessar e configurar as seguintes configurações para o cache.

![Configurações gerais](./media/cache-configure/redis-cache-general-settings.png)

-   [Propriedades](#properties)
-   [Teclas de acesso](#access-keys)
-   [Configurações avançadas](#advanced-settings)
-   [Relacionada Supervisor de Cache](#redis-cache-advisor)

### <a name="properties"></a>Propriedades

Clique em **Propriedades** para exibir informações sobre o cache, incluindo o ponto de extremidade de cache e portas.

![Propriedades do Cache de relacionada](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Teclas de acesso

Clique em **teclas de acesso** para exibir ou gerar as teclas de acesso para seu cache. Essas chaves são usadas junto com o nome de host e portas da lâmina **Propriedades** pelos clientes se conectam ao seu cache.

![Teclas de acesso do Cache de relacionada](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Configurações avançadas

As configurações a seguir são configurar na lâmina **Configurações avançadas** .

-   [Portas de acesso](#access-ports)
-   [Política de MaxMemory e reservados maxmemory](#maxmemory-policy-and-maxmemory-reserved)
-   [Notificações de Keyspace (configurações avançadas)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Portas de acesso

Por padrão, o access não SSL é desabilitado para novos caches. Para habilitar a porta não SSL, clique em **não** para **Permitir acesso somente via SSL** na **lâmina de configurações avançadas** e clique em **Salvar**.

![Portas de acesso de Cache relacionada](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Política de MaxMemory e reservados maxmemory

As configurações de **política de Maxmemory** e **reservada maxmemory** na lâmina **Configurações avançadas** configuram as políticas de memória para o cache. A configuração de **política de maxmemory** configura a política de remoção para o cache e **reservada maxmemory** configura a memória reservada para processos de fora do cache.

![Relacionada a política de Maxmemory de Cache](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Política de MaxMemory** permite que você escolha entre as seguintes políticas de remoção.

-   voláteis-lru - este é o padrão.
-   AllKeys-lru
-   voláteis aleatórias
-   AllKeys aleatórios
-   ttl voláteis
-   noeviction

Para obter mais informações sobre políticas de maxmemory, consulte [políticas de remoção](http://redis.io/topics/lru-cache#eviction-policies).

A configuração **reservada maxmemory** configura a quantidade de memória em MB reservada para operações de fora do cache como replicação durante tolerância a falhas. Ele também pode ser usado quando você tem uma taxa de fragmentação alta. Configurar esse valor permite que você tenha uma experiência de servidor relacionada mais consistente quando varia de sua carga. Este valor deve ser definido superior para cargas de trabalho que são escrever pesado. Quando a memória é reservada para essas operações não está disponível para armazenamento de dados armazenados em cache.

>[AZURE.IMPORTANT] A configuração **reservada maxmemory** está disponível somente para padrão e Premium armazena.

### <a name="keyspace-notifications-advanced-settings"></a>Notificações de Keyspace (configurações avançadas)

Relacionada keyspace notificações são configuradas no blade **Configurações avançadas** . Notificações de Keyspace permitir que os clientes receber notificações quando ocorrerem determinados eventos.

![Relacionada Cache configurações avançada](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notificações de Keyspace e a configuração de **Notificar-keyspace-eventos** estão disponíveis apenas para padrão e Premium armazena.

Para obter mais informações, consulte [Notificações de Keyspace relacionada](http://redis.io/topics/notifications). Para o código de exemplo, consulte o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) na amostra [Olá](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Relacionada Supervisor de Cache

A lâmina **recomendações** exibe recomendações para o cache. Durante operações normais, nenhuma recomendação for é exibidas. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se quaisquer condições ocorrerem durante as operações de seu cache como uso de memória alta, largura de banda de rede ou carga do servidor, um alerta é exibido na lâmina **Cache relacionada** .

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Mais informações podem ser encontradas na lâmina **recomendações** .

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Você pode monitorar essas métricas nas seções [gráficos de monitoramento](cache-how-to-monitor.md#monitoring-charts) e [gráficos de uso](cache-how-to-monitor.md#usage-charts) da lâmina **Cache relacionada** .

Cada nível de preços tem limites diferentes para conexões do cliente, memória e largura de banda. Se o cache aproximar capacidade máxima para essas métricas em um período de tempo constante, uma recomendação será criada. Para obter mais informações sobre os limites revisados pela ferramenta **recomendações** e métricas, consulte a tabela a seguir.

| Relacionada métrica de Cache      | Para mais informações, consulte                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Uso de largura de banda de rede | [Desempenho de cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes conectados       | [Configuração de servidor de relacionada padrão - maxclients](#maxclients)            |
| Carga do servidor             | [Gráficos de uso - relacionada a carga do servidor](cache-how-to-monitor.md#usage-charts)  |
| Uso da memória            | [Desempenho de cache - tamanho](cache-faq.md#cache-performance)                |

Para atualizar o cache, clique em **Atualizar agora** para alterar o [nível de preços](#pricing-tier) e dimensionar seu cache. Para obter mais informações sobre como escolher um nível de preços, consulte [quais oferta de Cache relacionada e tamanho devo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Configurações de escala

As configurações na seção **Dimensionar** permitem acessar e configurar as seguintes configurações para o cache.

![Rede](./media/cache-configure/redis-cache-scale.png)

-   [Nível de preço](#pricing-tier)
-   [Tamanho de cluster de relacionada](#cluster-size)

### <a name="pricing-tier"></a>Nível de preço

Clique em **nível de preços** para exibir ou alterar o nível de preços para seu cache. Para obter mais informações sobre dimensionamento, consulte [como escala Azure relacionada Cache](cache-how-to-scale.md).

![Relacionada Cache nível de preço](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Tamanho de Cluster de relacionada

Clique em **Tamanho de Cluster relacionada (visualização)** para alterar o tamanho de cluster para uma execução cache de premium com cluster ativado.

>[AZURE.NOTE] Observe que enquanto a camada Azure relacionada Cache Premium for lançada para disponibilidade geral, o recurso de tamanho de Cluster relacionada está no modo de visualização.

![Tamanho de cluster de relacionada](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **contagem fragmentar** e clique **Okey** para salvar.

>[AZURE.IMPORTANT] Relacionada cluster só está disponível para caches Premium. Para obter mais informações, consulte [como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Configurações de gerenciamento de dados

As configurações na seção **gerenciamento de dados** permitem acessar e configurar as seguintes configurações para o cache.

![Gerenciamento de dados](./media/cache-configure/redis-cache-data-management.png)

-   [Relacionada persistência de dados](#redis-data-persistence)
-   [Importação/exportação](#importexport)

### <a name="redis-data-persistence"></a>Relacionada persistência de dados

Clique em **relacionada persistência de dados** para habilitar, desabilitar ou configurar persistência de dados para o cache de premium.

![Relacionada persistência de dados](./media/cache-configure/redis-cache-persistence-settings.png)

Para ativar persistência relacionada, clique em **habilitado** para habilitar o backup do RDB (relacionada banco de dados). Para desabilitar persistência relacionada, clique em **desativado**.

Para configurar o intervalo de backup, selecione uma **Frequência de Backup** da lista suspensa. Opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Esse intervalo começa contagem regressiva após a operação de backup anterior for concluída com êxito e decorrida ela um novo backup é iniciado.

Clique na **Conta de armazenamento** para selecionar a conta de armazenamento para usar e escolha a **chave primária** ou **chave secundária** usar na lista suspensa **Chave de armazenamento** . Você deve escolher uma conta de armazenamento na mesma região como o cache e uma conta de **Armazenamento Premium** é recomendada porque o armazenamento de premium tem maior produtividade. Sempre que a chave de armazenamento para sua conta de persistência é gerado novamente, você deve escolher novamente a tecla desejada na lista suspensa **Chave de armazenamento** .

Clique em **Okey** para salvar a configuração de persistência.

>[AZURE.IMPORTANT] Persistência de dados relacionada está disponível somente para caches Premium. Para obter mais informações, consulte [como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importação/exportação

Importação/exportação é uma operação de gerenciamento de dados do Azure relacionada Cache que permite importar dados para o Cache do Azure relacionada ou exportar dados do Azure relacionada Cache por importar e exportar um instantâneo de banco de dados de Cache relacionada (RDB) de um cache premium em um blob de página em uma conta de armazenamento do Azure. Isso permite que você migrar entre diferentes instâncias do Azure relacionada Cache ou preencher o cache com dados antes de usar.

Importar pode ser usado para trazer relacionada compatível RDB arquivos de qualquer servidor relacionada em execução em qualquer nuvem ou ambiente, incluindo relacionada em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e outras pessoas. Importar dados é uma maneira fácil de criar um cache com dados preenchidos. Durante o processo de importação do Azure relacionada Cache carrega os arquivos RDB de armazenamento do Azure na memória e insere as teclas o cache.

Exportação permite que você exporte os dados armazenados no Azure relacionada Cache relacionada compatível RDB (s). Você pode usar esse recurso para mover dados de uma instância de Cache relacionada do Azure para outro ou para outro servidor relacionada. Durante o processo de exportação, que um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor de Cache relacionada do Azure e o arquivo é carregado para a conta de armazenamento designado. Quando a operação de exportação for concluído com qualquer um status de sucesso ou falha, o arquivo temporário é excluído.

>[AZURE.IMPORTANT] Importação/exportação está disponível somente para caches de nível Premium. Para obter mais informações e instruções, consulte [Importar e exportar dados no Azure relacionada Cache](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Configurações de administração

As configurações na seção **Administração** permitem executar as seguintes tarefas administrativas para o cache de premium. 

![Administração](./media/cache-configure/redis-cache-administration.png)

-   [Reinicialização](#reboot)
-   [Agendar atualizações](#schedule-updates)

>[AZURE.IMPORTANT] As configurações desta seção estão disponíveis apenas para caches de nível Premium.

### <a name="reboot"></a>Reinicialização

A lâmina **reinicializar** permite reinicializar um ou mais nós do cache. Isso permite que você teste seu aplicativo para resiliência em caso de uma falha.

![Reinicialização](./media/cache-configure/redis-cache-reboot.png)

Se você tiver um cache premium com cluster ativado, você pode selecionar quais fragmentos do cache para reinicializar.

![Reinicialização](./media/cache-configure/redis-cache-reboot-cluster.png)

Reiniciar uma ou mais nós do cache, selecione os nós desejado e clique em **Reiniciar**. Se você tiver um cache premium com cluster ativado, selecione o shard(s) para reinicializar e clique em **Reiniciar**. Após alguns minutos, a reinicialização de nós selecionada e são online novamente alguns minutos mais tarde.

>[AZURE.IMPORTANT] Reinicialização só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [Administração do Azure relacionada Cache - reinicializar](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Agendar atualizações

A lâmina **Agendar atualizações** permite designar uma janela de manutenção relacionada atualizações de servidor para o cache. 

>[AZURE.IMPORTANT] Observe que a janela de manutenção se aplica somente para relacionada atualizações de servidor e não para qualquer Azure atualizações ou atualizações para o sistema operacional das VMs que hospedam o cache.

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início de janela de manutenção para cada dia e clique **Okey**. Observe que o horário de janela de manutenção está em UTC. 

>[AZURE.IMPORTANT] Agendar atualizações só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [Administração do Azure relacionada Cache - agendar atualizações](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Configurações de diagnóstico

A seção **Diagnóstico** permite configurar diagnósticos para seu Cache relacionada.

![Diagnósticos](./media/cache-configure/redis-cache-diagnostics.png)

Clique em **Diagnóstico** para [Configurar a conta de armazenamento](cache-how-to-monitor.md#enable-cache-diagnostics) usada para armazenar o diagnóstico de cache.

![Relacionada diagnóstico de Cache](./media/cache-configure/redis-cache-diagnostics-settings.png)

Clique em **relacionada métricas** [vejam métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) de cache e **regras de alerta** para [Configurar regras de alerta](cache-how-to-monitor.md#operations-and-alerts).

Para obter mais informações sobre diagnóstico do Azure relacionada Cache, consulte [como monitorar Azure relacionada Cache](cache-how-to-monitor.md).


## <a name="network-settings"></a>Configurações de rede

As configurações na seção **rede** permitem acessar e configurar as seguintes configurações para o cache.

![Rede](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Configurações de rede virtual estão disponíveis apenas para caches premium que foram configurados com suporte VNET durante a criação de cache. Para obter informações sobre como criar um cache premium com VNET de suporte e atualizar suas configurações, consulte [como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Configurações de gerenciamento de recursos

![Gerenciamento de recursos](./media/cache-configure/redis-cache-resource-management.png)

Seção **marcas** ajuda você a organizar seus recursos. Para obter mais informações, consulte [usando marcas para organizar seus recursos Azure](../resource-group-using-tags.md).

A seção **bloqueia** permite bloquear uma assinatura, grupo de recursos ou recurso para impedir que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos. Para obter mais informações, consulte [recursos de bloqueio com o Gerenciador de recursos do Azure](../resource-group-lock-resources.md).

A seção **usuários** oferece suporte para o controle de acesso baseado em função (RBAC) no portal do Azure para ajudar as organizações a atender às suas necessidades de gerenciamento de acesso simplesmente e precisão. Para obter mais informações, consulte [controle de acesso baseado em função no portal do Azure](../active-directory/role-based-access-control-configure.md).

Clique em **Exportar modelo** para criar e exportar um modelo de seus recursos implantados para implantações futuras. Para obter mais informações sobre como trabalhar com modelos, consulte [recursos de implantar com modelos do Gerenciador de recursos do Azure](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configuração de servidor de relacionada padrão

Novas instâncias de Cache relacionada do Azure são configuradas com os seguintes relacionada configuração valores padrão.

>[AZURE.NOTE] As configurações desta seção não podem ser alteradas usando o `StackExchange.Redis.IServer.ConfigSet` método. Se esse método é chamado com um dos comandos nesta seção, é lançada uma exceção similar ao seguinte:  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Todos os valores que são configuráveis, como **max-memória-policy**, são configuráveis por meio das ferramentas de gerenciamento de portal ou linha de comando Azure como Azure CLI ou PowerShell.

|Configuração|Valor padrão|Descrição|
|---|---|---|
|bancos de dados|16|O número padrão de bancos de dados é 16, mas você pode configurar um número diferente baseado no nível de preços. <sup>1</sup> o banco de dados padrão é DB 0, você pode selecionar um diferente em um base de por conexão usando `connection.GetDatabase(dbid)` onde dbid é um número entre `0` e `databases - 1`.|
|maxclients|Depende do preço de nível<sup>2</sup>|Este é o número máximo de clientes conectados permitidos ao mesmo tempo. Quando o limite é atingido relacionada fechará todas as conexões de novos enviando um erro 'número máximo de clientes atingido'.|
|política de MaxMemory|lru voláteis|Diretiva de MaxMemory é a configuração para como relacionada selecionará o que remova quando maxmemory (o tamanho do cache oferta selecionada quando você criou o cache) é alcançado. Com o Cache do Azure relacionada a configuração padrão é voláteis-lru, que remove as teclas com um conjunto usando um algoritmo LRU de expirar. Esta configuração pode ser configurada no portal do Azure. Para obter mais informações, consulte [Maxmemory-política e reservados maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|exemplos de MaxMemory|3|LRU e algoritmos TTL mínimo não são precisos algoritmos mas aproximadas algoritmos (para economizar memória), para que você possa selecionar também verificar o tamanho da amostra. Por exemplo, para padrão a relacionada verificar três chaves e escolha aquele que foi usada menos recentemente.|
|limite de tempo de LUA|5.000|Tempo máximo de execução de um script de Lua em milissegundos. Se o tempo de execução máximo for atingido relacionada registrará que um script ainda está em execução após o máximo tempo permitido e começará a responder às consultas com um erro.|
|limite de evento LUA|500|Este é o tamanho máximo da fila de eventos de script.|
|cliente-saída-limite de buffer normalclient-saída-limite de buffer pubsub|0 0 032mb 8mb 60|Os limites de buffer de saída do cliente podem ser usados para forçar desconexão de clientes que não está lendo dados do servidor rapidez suficiente, por algum motivo (um motivo comum é que um cliente Pub/Sub não pode consumir mensagens tão rápidas quanto o publisher pode apresentá-los). Para obter mais informações, consulte [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> O limite para `databases` é diferente para cada Cache relacionada Azure camada de preços e podem ser definidas na criação de cache. Se nenhuma `databases` configuração for especificada durante a criação de cache, o padrão é 16.

-   Básico e do padrão
    -   C0 cache (250 MB) - até 16 bancos de dados
    -   C1 cache (1 GB) - até 16 bancos de dados
    -   C2 cache (2,5 GB) - até 16 bancos de dados
    -   C3 cache (6 GB) - até 16 bancos de dados
    -   C4 cache (13 GB) - até 32 bancos de dados
    -   C5 cache (26 GB) - até 48 bancos de dados
    -   C6 cache (53 GB) - até 64 bancos de dados
-   Armazena Premium
    -   P1 (6 GB - 60 GB) - até 16 bancos de dados
    -   P2 (13 GB - 130 GB) - até 32 bancos de dados
    -   P3 (26 GB - 260 GB) - até 48 bancos de dados
    -   P4 (GB 53-530 GB) - até 64 bancos de dados
    -   Todos os caches premium com cluster relacionada habilitado - cluster relacionada só aceita a utilização do banco de dados 0 portanto o `databases` um limite para qualquer cache premium com cluster relacionada habilitado efetivamente é 1 e o comando [Select](http://redis.io/commands/select) não é permitido. Para obter mais informações, consulte [preciso fazer alterações em meu aplicativo cliente para usar clusters?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] O `databases` configuração pode ser configurado somente durante a criação de cache e apenas usando o PowerShell, CLI ou outros clientes de gerenciamento. Para obter um exemplo de configuração `databases` durante a criação de cache usando o PowerShell, consulte [AzureRmRedisCache de novo](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Cache relacionada Azure preços camada.

-   Básico e do padrão
    -   C0 cache (250 MB) - até 256 conexões
    -   C1 cache (1 GB) - até 1.000 conexões
    -   C2 cache (2,5 GB) - até 2.000 conexões
    -   C3 cache (6 GB) - até 5.000 conexões
    -   C4 cache (13 GB) - até 10.000 conexões
    -   C5 cache (26 GB) - até 15.000 conexões
    -   C6 cache (53 GB) - até 20.000 conexões
-   Armazena Premium
    -   P1 (6 GB - 60 GB) - até 7.500 conexões
    -   P2 (13 GB - 130 GB) - até 15.000 conexões
    -   P3 (26 GB - 260 GB) - até 30.000 conexões
    -   P4 (GB 53-530 GB) - até 40.000 conexões

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Relacionada comandos que não têm suportados no Cache relacionada do Azure

>[AZURE.IMPORTANT] Porque a configuração e gerenciamento de instâncias do Azure relacionada Cache é gerenciada pela Microsoft os seguintes comandos estão desabilitados. Se você tentar invocá-los, você receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIGURAÇÃO
>-  DEPURAR
>-  MIGRAR
>-  SALVAR
>-  DESLIGAMENTO
>-  SLAVEOF
>-  CLUSTER - Cluster gravação estão desabilitados, mas somente leitura Cluster comandos são permitidos.

Para obter mais informações sobre comandos relacionada, consulte [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Console de relacionada

Com segurança emita comandos para suas instâncias de Cache relacionada do Azure usando o **Console relacionada**, que está disponível para o padrão e Premium armazena.

>[AZURE.IMPORTANT] O Console relacionada não funciona com VNET, clusters e bancos de dados diferente de 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - quando o cache é parte de um VNET, somente os clientes na VNET podem acessar o cache. Como o Console relacionada usa o cliente de cli.exe relacionada hospedado em VMs que não fazem parte de sua VNET, ele não pode conectar seu cache.
>-  [Cluster](cache-how-to-premium-clustering.md) - Console relacionada usa o cliente de cli.exe relacionada que não dá suporte a cluster neste momento. O utilitário de cli relacionada na ramificação [instável](http://redis.io/download) do repositório relacionada ao GitHub implementa suporte básico quando iniciado com o `-c` alternar. Para obter mais informações consulte [jogo com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) em [http://redis.io](http://redis.io) no [relacionada tutorial de cluster](http://redis.io/topics/cluster-tutorial).
>-  O Console relacionada faz uma nova conexão do banco de dados 0 cada vez que você enviar um comando. Não é possível usar o `SELECT` comando para selecionar outro banco de dados, porque o banco de dados é redefinido como 0 com cada comando. Para obter informações sobre como executar comandos relacionada, incluindo a alteração de um banco de dados diferentes, consulte [como executar comandos relacionada?](cache-faq.md#how-can-i-run-redis-commands)

Para acessar o Console relacionada, clique em **Console** da lâmina do **Cache relacionada** .

![Console de relacionada](./media/cache-configure/redis-console-menu.png)

Para emitir comandos na instância do cache, basta digite no comando desejado no console.

![Console de relacionada](./media/cache-configure/redis-console.png)

Para lista de comandos relacionada que estão desabilitados para Azure relacionada Cache, consulte a seção anterior [relacionada comandos que não têm suportados no Azure relacionada Cache](#redis-commands-not-supported-in-azure-redis-cache) . Para obter mais informações sobre comandos relacionada, consulte [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Mover o cache para uma nova assinatura

Você pode mover o cache para uma nova assinatura clicando em **Mover**.

![Mover Cache relacionada](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre mover recursos de um grupo de recursos para outro e de uma assinatura para outra, consulte [Mover recursos para novo grupo de recursos ou assinatura](../resource-group-move-resources.md).

## <a name="next-steps"></a>Próximas etapas
-   Para obter mais informações sobre como trabalhar com comandos relacionada, consulte [como executar comandos relacionada?](cache-faq.md#how-can-i-run-redis-commands).
