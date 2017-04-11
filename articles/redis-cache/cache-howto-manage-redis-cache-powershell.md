<properties
    pageTitle="Gerenciar Cache relacionada Azure com o PowerShell Azure | Microsoft Azure"
    description="Saiba como executar tarefas administrativas para o Cache de relacionada de Azure usando o PowerShell do Azure."
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gerenciar Cache relacionada Azure com o Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI Azure](cache-manage-cli.md)

Este tópico mostra como executar tarefas comuns, como criar, atualizar e dimensionar suas instâncias de Cache relacionada do Azure, como gerar as teclas de acesso e como exibir informações sobre seus caches. Para obter uma lista completa de cmdlets do PowerShell do Azure relacionada Cache, consulte [cmdlets do Azure relacionada Cache](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modelo clássico](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Pré-requisitos

Se você já tiver instalado o PowerShell do Azure, você deve ter Azure PowerShell versão 1.0.0 ou posterior. Você pode verificar a versão do PowerShell do Azure que você instalou com este comando no prompt de comando do PowerShell do Azure.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Primeiro, você deve efetuar login no Azure com este comando.

    Login-AzureRmAccount

Especifique o endereço de email da sua conta do Azure e sua senha no diálogo de entrada do Microsoft Azure.

Em seguida, se você tiver várias assinaturas Azure, você precisa definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o seguinte comando. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Antes de poder usar o Windows PowerShell com o Gerenciador de recursos do Azure, você precisa do seguinte:

- Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada para qualquer cmdlet que consulte neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o `New-AzureRmRedisCache` cmdlet, digite:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Como se conectar a nuvem de governo do Azure ou nuvem do Azure China

Por padrão o Azure ambiente é `AzureCloud`, que representa a instância de nuvem Azure global. Para conectar-se para uma instância diferente, use o `Add-AzureRmAccount` comando com a `-Environment` ou -`EnvironmentName` opção de linha de comando com o ambiente desejado ou o nome do ambiente.

Para ver a lista de ambientes disponíveis, execute o `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para conectar-se para a nuvem de governo do Azure

Para conectar-se para a nuvem de governo do Azure, use um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para criar um cache na nuvem Azure governo, use um dos seguintes locais.

-   USGov Virgínia
-   Iowa USGov

Para obter mais informações sobre a nuvem de governo do Azure, consulte [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [Guia de desenvolvedor do Microsoft Azure governamentais](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para conectar-se para a nuvem de China do Azure

Para se conectar à nuvem China Azure, use um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para criar um cache na nuvem China Azure, use um dos seguintes locais.

-   China Leste
-   América do Norte China

Para obter mais informações sobre a nuvem do Azure China, consulte [AzureChinaCloud Azure operado pela 21Vianet na China](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propriedades usadas para Azure relacionada Cache PowerShell

A tabela a seguir contém propriedades e descrições de parâmetros comumente usadas ao criar e gerenciar suas instâncias de Cache relacionada do Azure usando o PowerShell do Azure.

| Parâmetro          | Descrição                                                                                                                                                                                                        | Padrão  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nome               | Nome do cache                                                                                                                                                                                                  |          |
| Local           | Local do cache                                                                                                                                                                                              |          |
| ResourceGroupName  | Nome do grupo de recursos no qual deseja criar o cache                                                                                                                                                                   |          |
| Tamanho               | O tamanho do cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB                                                                     | 1GB      |
| ShardCount         | O número de fragmentos criar ao criar um cache premium com cluster ativado. Os valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9 e 10                                                                                                      |          |
| SKU                | Especifica o SKU do cache. Os valores válidos são: básico, padrão, Premium                                                                                                                                         | Padrão |
| RedisConfiguration | Especifica configurações relacionada. Para obter detalhes sobre cada configuração, consulte a tabela de [Propriedades de RedisConfiguration](#redisconfiguration-properties) a seguir. |          |
| EnableNonSslPort   | Indica se a porta SSL não está habilitada.                                                                                                                                                                     | FALSO    |
| MaxMemoryPolicy    | Este parâmetro foi substituído - use RedisConfiguration em vez disso.                                                                                                                                              |          |
| StaticIP           | Quando o cache em um VNET de hospedagem, especifica um endereço IP exclusivo na sub-rede para o cache. Se não for fornecido, um será escolhido para você da sub-rede.                                                                                                                     |          |
| Sub-rede             | Quando o cache em um VNET de hospedagem, especifica o nome da sub-rede no qual deseja implantar o cache.                                                                                                                  |          |
| VirtualNetwork     | Quando o cache em um VNET de hospedagem, especifica a identificação de recurso da VNET no qual deseja implantar o cache.                                                                                                             |          |
| KeyType            | Especifica qual tecla de acesso para gerar novamente ao renovar teclas de acesso. Os valores válidos são: principal, secundário |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration

| Propriedade                      | Descrição                                                                                                          | Níveis de preços       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| RDB backup-habilitados            | Se [relacionada persistência de dados](cache-how-to-premium-persistence.md) está habilitada                                     | Premium apenas        |
| RDB-armazenamento-cadeia de conexão | A cadeia de conexão para a conta de armazenamento para [relacionada persistência de dados](cache-how-to-premium-persistence.md)       | Premium apenas        |
| frequência de backup RDB          | A frequência de backup de [relacionada persistência de dados](cache-how-to-premium-persistence.md)                               | Premium apenas        |
| MaxMemory reservada            | Configura a [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos de fora do cache | Padrão e Premium |
| política de MaxMemory              | Configura a [política de remoção](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para o cache           | Todos os níveis de preços   |
| Notificar-keyspace-eventos        | Configura [notificações de keyspace](cache-configure.md#keyspace-notifications-advanced-settings)                     | Padrão e Premium |
| entradas-de-ziplist-max-hash      | Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de pequenas agregar dados          | Padrão e Premium |
| hash-max-ziplist-valor        | Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de pequenas agregar dados          | Padrão e Premium |
| entradas-de-intset-max-conjunto        | Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de pequenas agregar dados          | Padrão e Premium |
| entradas-de-ziplist-max-zset      | Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de pequenas agregar dados          | Padrão e Premium |
| zset-max-ziplist-valor        | Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de pequenas agregar dados          | Padrão e Premium |
| bancos de dados                     | Configura o número de bancos de dados. Essa propriedade pode ser configurada apenas na criação de cache.                          | Padrão e Premium |

## <a name="to-create-a-redis-cache"></a>Para criar um Cache relacionada

Novas instâncias de Cache relacionada do Azure são criadas usando o cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] Na primeira vez que você cria um cache relacionada em uma assinatura usando o portal do Azure, o portal registra o `Microsoft.Cache` namespace para essa assinatura. Se você tentar criar o cache de relacionada primeiro em uma assinatura usando o PowerShell, você deve primeiro registrar namespace usando o seguinte comando; Caso contrário, cmdlets como `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` falhar.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Para ver uma lista de parâmetros disponíveis e suas descrições para `New-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para criar um cache com parâmetros padrão, execute o seguinte comando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` são parâmetros necessários, mas o restante são opcional e têm valores padrão. Executar o comando anterior cria uma instância de Cache de relacionada do Azure de SKU padrão com o nome especificado, o local e o grupo de recursos, que é 1 GB de tamanho com a porta não SSL desabilitado.

Para criar um cache premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), ou P4 (GB 53-530 GB). Para habilitar o agrupamento, especificar uma contagem de fragmentar usando o `ShardCount` parâmetro. O exemplo a seguir cria um cache de premium P1 com 3 fragmentos. Um cache de premium P1 é 6 GB de tamanho e como podemos especificado três fragmentos o tamanho total é 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o `RedisConfiguration` parâmetro, coloque os valores dentro `{}` como chave/valor, como pares `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo a seguir cria um cache padrão de 1 GB com `allkeys-random` notificações de política e keyspace de maxmemory configuradas com `KEA`. Para obter mais informações, consulte [notificações de Keyspace (configurações avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [Maxmemory-política e reservados maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar os bancos de dados definindo durante a criação do cache

O `databases` configuração pode ser configurada somente durante a criação de cache. O exemplo a seguir cria um premium P3 cache (26 GB) com 48 bancos de dados usando o cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para obter mais informações sobre o `databases` propriedade, consulte [configuração do servidor de Cache padrão do Azure relacionada](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre como criar um cache usando o cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) , consulte a seção anterior [para criar um Cache relacionada](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Para atualizar um cache relacionada

Instâncias de Cache relacionada Azure são atualizadas usando o cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Set-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

O `Set-AzureRmRedisCache` cmdlet pode ser usado para atualizar propriedades, como `Size`, `Sku`, `EnableNonSslPort`e o `RedisConfiguration` valores. 

O comando a seguir atualiza a política de maxmemory para Cache relacionada chamado myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Para dimensionar um cache relacionada

`Set-AzureRmRedisCache`pode ser usado para dimensionar um cache Azure relacionada a instância quando o `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. 

>[AZURE.NOTE]Dimensionamento um cache usando o PowerShell está sujeito às mesmas limites diretrizes como dimensionamento um cache do portal do Azure. Você pode dimensionar para um nível de preços diferentes com as seguintes restrições.
>
>-  Você não consegue dimensionar de um nível de preços superior para um nível inferior de preços.
>    -    Não é possível redimensionar de um cache **Premium** para baixo até um **padrão** ou um cache **básico** .
>    -    Não é possível redimensionar de um cache **padrão** para baixo até um cache **básico** .
>-  Você pode dimensionar de um cache **básicas** para um cache **padrão** , mas você não pode alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
>-  Você não consegue dimensionar de um cache **básicas** diretamente em um cache **Premium** . Você deve dimensionar do **básico** ao **padrão** em uma operação de escala e, em seguida, do **padrão** para **Premium** em uma operação de dimensionamento subsequente.
>-  Não é possível redimensionar de um tamanho maior para baixo até o **C0 (250 MB)** tamanho.
>
>Para obter mais informações, consulte [como escala Azure relacionada Cache](cache-how-to-scale.md).

O exemplo a seguir mostra como dimensionar um cache chamado `myCache` para um cache de 2,5 GB. Observe que este comando funciona para um Basic ou um cache padrão.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois que esse comando é emitido, o status do cache é retornado (semelhante a chamar `Get-AzureRmRedisCache`). Observe que o `ProvisioningState` é `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Quando a operação de dimensionamento for concluída, o `ProvisioningState` muda para `Succeeded`. Se você precisar fazer uma operação de dimensionamento subsequente, como a alteração de básicas para padrão e, em seguida, alterando o tamanho, você deve esperar até que a operação anterior é concluída ou você recebe um erro semelhante ao seguinte.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Para obter informações sobre um cache relacionada

Você pode recuperar informações sobre um cache usando o cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Get-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para retornar informações sobre todos os caches na assinatura atual, execute `Get-AzureRmRedisCache` sem parâmetros.

    Get-AzureRmRedisCache

Para retornar informações sobre todos os caches em um grupo de recursos específicos, execute `Get-AzureRmRedisCache` com a `ResourceGroupName` parâmetro.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Para retornar informações sobre um cache específica, execute `Get-AzureRmRedisCache` com a `Name` parâmetro contendo o nome do cache e o `ResourceGroupName` parâmetro com o grupo de recursos que contém o cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Para recuperar as teclas de acesso para um cache relacionada

Para recuperar as teclas de acesso para seu cache, você pode usar o cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Get-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para recuperar as chaves para o cache, chame o `Get-AzureRmRedisCacheKey` cmdlet e passar o nome do seu cache o nome do grupo de recursos que contém o cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Para gerar chaves de acesso para o cache relacionada

Para gerar as teclas de acesso para seu cache, você pode usar o cmdlet [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `New-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Para recriar a chave primária ou secundária para o cache, chame o `New-AzureRmRedisCacheKey` cmdlet e passar o nome, o grupo de recursos e especificar ou `Primary` ou `Secondary` para o `KeyType` parâmetro. No exemplo a seguir, a tecla de acesso secundário para um cache é gerada novamente.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Para excluir um cache relacionada

Para excluir um cache relacionada, use o cmdlet [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Remove-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

No exemplo a seguir, o cache chamado `myCache` é removido.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Importar um cache relacionada

Você pode importar dados para uma instância de Cache relacionada do Azure usando o `Import-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importação/exportação está disponível somente para caches de [nível premium](cache-premium-tier-intro.md) . Para obter mais informações sobre a importação/exportação, consulte [Importar e exportar dados no Azure relacionada Cache](cache-how-to-import-export-data.md).

Para ver uma lista de parâmetros disponíveis e suas descrições para `Import-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

O comando a seguir importa dados do blob especificado pelo uri SAS no Azure relacionada Cache.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Para exportar um cache relacionada

Você pode exportar dados de uma instância de Cache relacionada do Azure usando o `Export-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importação/exportação está disponível somente para caches de [nível premium](cache-premium-tier-intro.md) . Para obter mais informações sobre a importação/exportação, consulte [Importar e exportar dados no Azure relacionada Cache](cache-how-to-import-export-data.md).

Para ver uma lista de parâmetros disponíveis e suas descrições para `Export-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


O comando a seguir exporta dados de uma instância do Azure relacionada Cache no contêiner especificado pelo uri SAS.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Para reinicializar um cache relacionada

Você pode reinicializar sua instância de Cache relacionada do Azure usando o `Reset-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Reinicialização só está disponível para caches de [nível premium](cache-premium-tier-intro.md) . Para obter mais informações sobre como reiniciar o cache, consulte [Administração de Cache - reinicializar](cache-administration.md#reboot).

Para ver uma lista de parâmetros disponíveis e suas descrições para `Reset-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

O comando a seguir reinicializa ambos os nós do cache de especificado.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o Windows PowerShell com o Azure, consulte os seguintes recursos:

- [Documentação de cmdlet relacionada Cache Azure no MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlets de Gerenciador de recursos do Azure](http://go.microsoft.com/fwlink/?LinkID=394765): Saiba como usar os cmdlets no módulo AzureResourceManager.
- [Usando o recurso grupos para gerenciar os recursos Azure](../resource-group-template-deploy-portal.md): Aprenda a criar e gerenciar grupos de recursos no portal do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): Saiba mais sobre novos recursos do Azure.
- [Blog do Windows PowerShell](http://blogs.msdn.com/powershell): Saiba mais sobre novos recursos no Windows PowerShell.
- ["Olá, equipe de scripts!" Blog](http://blogs.technet.com/b/heyscriptingguy/): Obtenha reais dicas e truques da comunidade do Windows PowerShell.
