<properties 
    pageTitle="Como configurar persistência de dados para um Premium Azure relacionada Cache" 
    description="Saiba como configurar e gerenciar suas instâncias do Azure relacionada Cache de nível Premium de persistência de dados" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Como configurar persistência de dados para um Premium Azure relacionada Cache

Cache Azure relacionada tem ofertas de cache diferentes que fornecem flexibilidade na escolha do tamanho do cache e recursos, incluindo o novo nível de Premium.

A camada de premium Azure relacionada Cache inclui recursos como cluster, persistência e suporte de rede virtual. Este artigo descreve como configurar persistência em uma instância de Cache relacionada do Azure premium.

Para obter informações sobre outros recursos premium do cache, consulte [Introdução para o nível Premium de Cache do Azure relacionada](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é persistência de dados?
Persistência relacionada permite persistir dados armazenados em relacionada. Você também pode obter instantâneos e fazer backup dos dados, que você pode carregar no caso de uma falha de hardware. Esta é uma enorme vantagem sobre nível Basic ou padrão onde todos os dados são armazenados na memória e pode haver perda de dados no caso de uma falha anotados nós de Cache para baixo. 

Cache de relacionada Azure oferece persistência relacionada usando o [modelo RDB](http://redis.io/topics/persistence), onde os dados são armazenados em uma conta de armazenamento do Azure. Quando persistência é configurada, o Azure relacionada Cache persistir um instantâneo do cache relacionada em um formato binário relacionada em disco com base em uma frequência de backup configurável. Se ocorrer um evento grave que desabilita cache primário e réplica, o cache é reconstruído usando o instantâneo mais recente.

Persistência pode ser configurada da lâmina do **Novo Cache relacionada** durante a criação de cache e na lâmina **configurações** para caches de premium existente.

## <a name="create-a-premium-cache"></a>Criar um cache premium

Para criar um cache e configurar persistência, entrar no [portal do Azure](https://portal.azure.com) e clique em **novo**->**dados + armazenamento**>**Cache relacionada**.

![Criar um Cache relacionada][redis-cache-new-cache-menu]

Para configurar persistência, primeiro selecione um dos caches **Premium** na lâmina **Escolha sua camada de preços** .

![Escolha sua camada de preços][redis-cache-premium-pricing-tier]

Quando um nível de preço de premium estiver selecionada, clique em **relacionada persistência**.

![Relacionada persistência][redis-cache-persistence]

As etapas na seção a seguir descrevem como configurar persistência relacionada em seu novo cache premium. Após configurar a persistência relacionada, clique em **criar** para criar o novo cache premium com persistência relacionada.

## <a name="configure-redis-persistence"></a>Configurar persistência relacionada

Relacionada persistência está configurada no blade **relacionada persistência de dados** . Para novos caches: este blade é acessado durante o processo de criação de cache, conforme descrito na seção anterior. Para caches existentes, a lâmina **relacionada persistência de dados** é acessada da lâmina **configurações** para seu cache.

![Configurações de relacionada][redis-cache-settings]

Para ativar persistência relacionada, clique em **habilitado** para habilitar o backup do RDB (relacionada banco de dados). Para desabilitar persistência relacionada em um cache premium habilitado anteriormente, clique em **desativado**.

Para configurar o intervalo de backup, selecione uma **Frequência de Backup** da lista suspensa. Opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Esse intervalo começa contagem regressiva após a operação de backup anterior for concluída com êxito e decorrida ela um novo backup é iniciado.

Clique na **Conta de armazenamento** para selecionar a conta de armazenamento para usar e escolha a **chave primária** ou **chave secundária** usar na lista suspensa **Chave de armazenamento** . Você deve escolher uma conta de armazenamento na mesma região como o cache e uma conta de **Armazenamento Premium** é recomendada porque o armazenamento de premium tem maior produtividade. 

>[AZURE.IMPORTANT] Se a chave de armazenamento para sua conta de persistência é gerado novamente, você deve rechoose a tecla desejada na lista suspensa **Chave de armazenamento** .

![Relacionada persistência][redis-cache-persistence-selected]

Clique em **Okey** para salvar a configuração de persistência.

O próximo backup (ou o primeiro backup para novos caches) é iniciado assim que o intervalo de frequência de backup ociosa.



## <a name="persistence-faq"></a>Persistência perguntas Frequentes

A lista a seguir contém respostas para perguntas frequentes sobre persistência Azure relacionada Cache.

-   [Pode habilitar persistência em um cache criado anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [Posso alterar a frequência de backup depois de criar o cache?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Por que se eu tiver uma frequência de backup de 60 minutos há mais de 60 minutos entre backups?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [O que acontece com os backups antigos quando um novo backup é feito?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [O que acontece se eu tiver dimensionada para um tamanho diferente e um backup for restaurado que foi feita antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Pode habilitar persistência em um cache criado anteriormente?

Sim, relacionada persistência pode ser configurada na criação de cache e em caches de premium existente.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de backup depois de criar o cache?

Sim, você pode alterar a frequência de backup no blade **relacionada persistência de dados** . Para obter instruções, consulte [Configurar relacionada persistência](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de backup de 60 minutos há mais de 60 minutos entre backups?

O intervalo de frequência de backup não é iniciado até que o processo de backup anterior foi concluída com êxito. Se a frequência de backup é 60 minutos e demora um processo de backup de 15 minutos para ser concluída com êxito, o próximo backup não iniciará até 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>O que acontece com os backups antigos quando um novo backup é feito?

Todos os backups, exceto para mais recente são excluídos automaticamente. Esta exclusão pode não ocorrer imediatamente, mas backups mais antigos não são mantidos indefinidamente.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver dimensionada para um tamanho diferente e um backup for restaurado que foi feita antes da operação de escala?

-   Se você tiver escala para um tamanho maior, não há nenhum impacto.
-   Se você tiver dimensionada para um tamanho menor, e você tiver um personalizado [bancos de dados de](cache-configure.md#databases) configuração que é maior que o [limitam de bancos de dados](cache-configure.md#databases) para o novo tamanho, dados nesses bancos de dados não ser restaurados. Para obter mais informações, consulte [é meus bancos de dados personalizados configuração afetada durante dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Se você tiver dimensionada para um tamanho menor, e não há espaço suficiente no tamanho menor para manter todos os dados desde o último backup, chaves serão removidas durante o processo de restauração, normalmente usando a política de remoção [allkeys lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Próximas etapas
Saiba como usar mais recursos premium do cache.

-   [Introdução para o nível Premium de Cache do Azure relacionada](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
