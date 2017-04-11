<properties 
    pageTitle="Como administrar Azure relacionada Cache | Microsoft Azure"
    description="Saiba como executar tarefas de administração como reinicialização e agendar atualizações de Cache relacionada do Azure"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Como administrar Cache relacionada do Azure

Este tópico descreve como executar tarefas de administração como reinicializar e agendar atualizações para suas instâncias de Cache relacionada do Azure.

>[AZURE.IMPORTANT] As configurações e os recursos descritos neste artigo estão disponíveis apenas para caches de nível Premium.


## <a name="administration-settings"></a>Configurações de administração

As configurações de Cache relacionada do Azure **Administração** permitem executar as seguintes tarefas administrativas para o cache de premium. Para acessar as configurações de administração, clique em **configurações** ou **todas as configurações** do blade de Cache relacionada e role até a seção **Administração** na lâmina **configurações** .

![Administração](./media/cache-administration/redis-cache-administration.png)

-   [Reinicialização](#reboot)
-   [Agendar atualizações](#schedule-updates)

## <a name="reboot"></a>Reinicialização

A lâmina **reinicializar** permite reinicializar um ou mais nós do cache. Isso permite que você teste seu aplicativo para resiliência em caso de uma falha.

![Reinicialização](./media/cache-administration/redis-cache-reboot.png)

Se você tiver um cache premium com cluster ativado, você pode selecionar quais fragmentos do cache para reinicializar.

![Reinicialização](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejado e clique em **Reiniciar**. Se você tiver um cache premium com cluster ativado, selecione o shard(s) para reinicializar e clique em **Reiniciar**. Após alguns minutos, a reinicialização de nós selecionada e são online novamente alguns minutos mais tarde.

O impacto em aplicativos cliente varia dependendo os nós que você reinicializar.

-   **Mestre** - quando o nó mestre é reiniciado, Cache relacionada do Azure falha sobre o nó de réplica e promove-mestre. Durante esse failover, talvez haja um intervalo curto na qual as conexões podem falhar ao cache.
-   **Auxiliar** - quando o nó auxiliar é reiniciado, normalmente há nenhum impacto para os clientes de cache.
-   **Ambos mestre e slave** - quando ambos os nós de cache são reinicializados, todos os dados é perdida no cache e conexões para a falha de cache até o nó primário fica online novamente. Se você tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente é restaurado quando o cache vem online novamente. Observe que quaisquer gravações de cache ocorridas após o backup mais recente são perdidas.
-   **Nós de um cache premium com cluster ativado** - quando reiniciar os nós de um cache premium com cluster ativado, o comportamento é a mesma que quando você reinicializar nós de um cache não-clusterizados.


>[AZURE.IMPORTANT] Reinicialização só está disponível para caches de nível Premium.

## <a name="reboot-faq"></a>Reinicie perguntas Frequentes

-   [Qual nó deve reinicializar para testar meu aplicativo?](#which-node-should-i-reboot-to-test-my-application)
-   [Para reiniciar o cache para limpar conexões de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Posso perderá dados do meu cache se fazer uma reinicialização?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [Posso reinicializar meu usando o PowerShell, CLI ou outras ferramentas de gerenciamento de cache?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [Quais preços camadas podem usar a funcionalidade de reinicialização?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Qual nó deve reinicializar para testar meu aplicativo?

Para testar a resiliência do seu aplicativo contra falha do nó principal do cache, reinicie o nó **mestre** . Para testar a resiliência do seu aplicativo contra falha do nó secundário, reinicie o nó **auxiliar** . Para testar a resiliência de seu aplicativo contra falha total do cache, reinicialize **ambos os** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Para reiniciar o cache para limpar conexões de cliente?

Sim, se você reinicializar o cache de todas as conexões de cliente estão desmarcadas. Isso pode útil no caso onde todas as conexões de cliente são usadas para cima, por exemplo devido a um erro de lógica ou um bug no aplicativo cliente. Cada nível de preços tem diferentes [limites de conexão do cliente](cache-configure.md#default-redis-server-configuration) de vários tamanhos, e quando esses limites são atingidos, não há mais conexões de cliente são aceitas. Reiniciar o cache fornece uma maneira de desmarcar todas as conexões de cliente.

>[AZURE.IMPORTANT] Se suas conexões de cliente são usados para cima devido a um erro de lógica ou bugs no código do seu cliente, observe que StackExchange.Redis automaticamente se reconectar depois que o nó relacionada estiver online novamente. Se o problema subjacente não for resolvido, as conexões de cliente continuará a ser usado para cima.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Posso perderá dados do meu cache se fazer uma reinicialização?

Se você reinicializar nós **mestre** e **auxiliar** todos os dados no cache (ou em que fragmentar se você estiver usando um cache premium com cluster ativado) é perdido. Se você tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente será restaurado quando o cache vem online novamente. Observe que quaisquer gravações de cache que ocorreram depois que o backup foi feito são perdidas.

Se você reinicializar apenas um de nós, dados não são normalmente perdidos, mas ainda pode ser. Por exemplo, se o nó mestre é reiniciado e uma gravação de cache está em andamento, os dados da gravação de cache for perdido. Outro cenário perda de dados seria se você reinicializar um nó e o outro nó acontece com desce devido a uma falha ao mesmo tempo. Para obter mais informações sobre as possíveis causas para perda de dados, consulte [o que aconteceu com meus dados em relacionada?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reinicializar meu usando o PowerShell, CLI ou outras ferramentas de gerenciamento de cache?

Sim, para PowerShell instruções ver [reinicializar um cache relacionada](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Quais preços camadas podem usar a funcionalidade de reinicialização?

Reinicialização está disponível somente no premium preços camada.

## <a name="schedule-updates"></a>Agendar atualizações

A lâmina **Agendar atualizações** permite designar uma janela de manutenção para seu cache. Quando a janela de manutenção é especificada, todas as atualizações relacionada server são feitas durante esta janela. Observe que a janela de manutenção se aplica somente para relacionada atualizações de servidor e não para qualquer Azure atualizações ou atualizações para o sistema operacional das VMs que hospedam o cache.

![Agendar atualizações](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início de janela de manutenção para cada dia e clique **Okey**. Observe que o horário de janela de manutenção está em UTC. 

>[AZURE.NOTE] A janela de manutenção padrão atualizações é 5 horas. Esse valor não é configurável do portal do Azure, mas você pode configurá-lo usando o PowerShell a `MaintenanceWindow` parâmetro do cmdlet [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Para obter mais informações, consulte [pode eu gerenciado usando o PowerShell, CLI ou outras ferramentas de gerenciamento de atualizações agendadas?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Agendar atualizações perguntas Frequentes

-   [Quando ocorrem atualizações se não usar o recurso de atualizações de cronograma?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Que tipos de atualizações são feitas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [É possível gerenciadas atualizações agendadas usando o PowerShell, CLI ou outras ferramentas de gerenciamento?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [Quais preços camadas podem usar a funcionalidade de atualizações de cronograma?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando ocorrem atualizações se não usar o recurso de atualizações de cronograma?

Se você não especificar uma janela de manutenção, atualizações podem ser feitas a qualquer momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipos de atualizações são feitas durante a janela de manutenção agendada?

Somente relacionada atualizações são feitas durante a janela de manutenção programada de servidor. A janela de manutenção não se aplica ao Azure atualizações ou atualizações para o sistema operacional de máquina virtual.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>É possível gerenciadas atualizações agendadas usando o PowerShell, CLI ou outras ferramentas de gerenciamento?

Sim, você pode gerenciar suas atualizações agendadas usando os seguintes cmdlets do PowerShell.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Novo AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Novo AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Remover AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Quais preços camadas podem usar a funcionalidade de atualizações de cronograma?

Agendar atualizações só está disponível na premium preços camada.

## <a name="next-steps"></a>Próximas etapas

-   Explore mais recursos de [camada de premium de Cache de relacionada do Azure](cache-premium-tier-intro.md) .





