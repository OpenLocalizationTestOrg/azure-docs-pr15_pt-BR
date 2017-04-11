<properties 
    pageTitle="Migrar o cache para relacionada | Microsoft Azure"
    description="Saiba como migrar aplicativos de serviço de Cache gerenciados para Cache relacionada do Azure"
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
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrar do serviço de Cache gerenciado para Cache relacionada Azure

Migrar seus aplicativos que usam o serviço de Cache do Azure gerenciado Azure relacionada cache pode ser feito com mínimas alterações para seu aplicativo, dependendo dos recursos de serviço de Cache gerenciados usados pelo seu aplicativo de cache. Enquanto as APIs não são exatamente o mesmo que eles são semelhantes e muita seu código existente que usa o serviço de Cache gerenciados para acessar um cache pode ser reutilizada com mínimas alterações. Este tópico mostra como fazer as alterações de aplicativo para migrar seus aplicativos de serviço de Cache gerenciados para usar Cache relacionada do Azure e configuração necessárias e como alguns dos recursos do Azure relacionada Cache podem ser usado para implementar a funcionalidade de um cache de serviço de Cache gerenciados.

## <a name="migration-steps"></a>Etapas de migração

As etapas a seguir são necessárias para migrar um aplicativo de serviço de Cache gerenciados para usar Cache relacionada do Azure.

-   Mapear recursos do serviço de Cache gerenciados para Cache relacionada do Azure
-   Escolha uma oferta de Cache
-   Criar um Cache
-   Configurar os clientes de Cache
    -   Remover a configuração do serviço de Cache gerenciado
    -   Configurar um cliente de cache usando o pacote de NuGet StackExchange.Redis
-   Migrar código gerenciado serviço de Cache
    -   Conectar-se para o cache usando a classe de ConnectionMultiplexer
    -   Tipos de dados primitivos de acesso no cache
    -   Trabalhar com objetos .NET no cache
-   Migrar o estado da sessão ASP.NET e o cache de saída para Cache relacionada do Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapear recursos do serviço de Cache gerenciados para Cache relacionada do Azure

Azure gerenciado serviço de Cache e Azure relacionada Cache são semelhantes, mas implementam alguns dos seus recursos de maneiras diferentes. Esta seção descreve algumas das diferenças e fornece orientação sobre implementação dos recursos de serviço de Cache gerenciados no Azure relacionada Cache.

|Recurso de serviço de Cache de gerenciado|Suporte de serviço de Cache gerenciado|Suporte de Cache relacionada Azure|
|---|---|---|
|Caches nomeados|Um cache padrão está configurado e no cache Standard e Premium ofertas, até nove adicionais denominada caches podem ser configuradas se desejado.|Azure caches relacionada tem um número configurável de bancos de dados (padrão de 16) que podem ser usadas para implementar uma funcionalidade semelhante à caches nomeados. Para obter mais informações, consulte [padrão relacionada a configuração do servidor](cache-configure.md#default-redis-server-configuration).|
|Alta disponibilidade|Fornece alta disponibilidade para itens no cache nas ofertas de cache Standard e Premium. Se itens forem perdidas devido a uma falha, cópias de backup dos itens no cache ainda estão disponíveis. Gravações o cache secundário são feitas em sincronia.|Alta disponibilidade está disponível nas ofertas de cache Standard e Premium, que tem uma configuração de principal/réplica de dois nós (cada fragmentar em cache Premium tem um par de principal/réplica). Gravações réplica são feitas de forma assíncrona. Para obter mais informações, consulte [preços do Azure relacionada Cache](https://azure.microsoft.com/pricing/details/cache/).|
|Notificações|Permite que clientes receber notificações assíncronas quando uma variedade de operações de cache ocorrem em um cache nomeado.|Aplicativos cliente podem usar relacionada pub/sub ou [notificações de Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para obter uma funcionalidade semelhante para notificações.|
|Cache local|Armazena uma cópia dos objetos armazenados em cache localmente no cliente de acesso rápido extra.|Aplicativos cliente precisaria implementar essa funcionalidade usando um dicionário ou estrutura de dados semelhantes.|
|Diretiva de remoção|Nenhum ou LRU. A política padrão é LRU.|Cache de relacionada Azure suporta as seguintes políticas de remoção: voláteis lru, allkeys-lru, voláteis-ttl voláteis aleatórias, allkeys-aleatório, noeviction. A política padrão é voláteis lru. Para obter mais informações, consulte [padrão relacionada a configuração do servidor](cache-configure.md#default-redis-server-configuration).|
|Política de expiração|A política de expiração padrão é absoluto e o intervalo de expiração padrão é dez minutos. Políticas de deslizando e nunca também estão disponíveis.|Por padrão, itens do cache não expiram, mas uma expiração pode ser configurada individualmente por gravação usando sobrecargas de conjunto de cache. Para obter mais informações, consulte [Adicionar e recuperar objetos do cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Regiões e marcação|Regiões são subgrupos para itens armazenados em cache. Regiões também oferecem suporte a anotação de itens armazenados em cache com adicionais sequências de caracteres descritivas chamadas marcas. Regiões oferecem suporte a capacidade de executar operações de pesquisa em todos os itens marcados nessa região. Todos os itens dentro de uma região estão localizados em um único nó do cluster cache.|Um cache relacionada consiste em um único nó (a menos que cluster relacionada está habilitado) para que o conceito de regiões de serviço de Cache gerenciados não se aplica. Relacionada oferece suporte à pesquisa e operações de curinga ao recuperar chaves para que marcas descritivas podem ser incorporadas os principais nomes e usadas para recuperar os itens mais tarde. Para obter um exemplo de implementação de uma solução de marcação usando relacionada, consulte [Implementando o cache de marcação com relacionada](http://stackify.com/implementing-cache-tagging-redis/).|
|Serialização|Cache gerenciado suporta NetDataContractSerializer, BinaryFormatter e o uso de serializadores personalizados. O padrão é NetDataContractSerializer.|É responsabilidade do aplicativo cliente para serializar objetos .NET antes de colocá-los em cache, com a opção do serializador para cima até o desenvolvedor do aplicativo de cliente. Para obter mais informações e códigos de amostra, consulte [trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Emulador de cache | Cache gerenciado fornece um emulador de cache local. | Cache Azure relacionada não tiver um emulador, mas você pode [executar a compilação MSOpenTech do relacionada-server.exe localmente](cache-faq.md#cache-emulator) para fornecer uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolha uma oferta de Cache

Cache do Microsoft Azure relacionada está disponível nos seguintes níveis:

-   **Básicas** – nó único. Vários tamanhos até 53 GB.
-   **Padrão** – dois nós primário/réplica. Vários tamanhos até 53 GB. 99,9 SLA %.
-   **Premium** – dois nós primário/réplica com até 10 fragmentos. Vários tamanhos de 6 GB para 530 GB (Fale conosco para obter mais informações). Todos os recursos de nível padrão e mais incluindo suporte para [relacionada cluster](cache-how-to-premium-clustering.md), [relacionada persistência](cache-how-to-premium-persistence.md)e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada nível é diferente em termos de recursos e preços. Os recursos são abordados neste manual e para obter mais informações sobre preços, consulte [Detalhes de preços do Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolha o tamanho que corresponde ao tamanho de seu cache de serviço de Cache gerenciados anterior e, em seguida, expandir ou reduzir, dependendo dos requisitos do seu aplicativo. Para obter mais orientações sobre como escolher a oferta certa do Azure relacionada Cache, consulte [quais oferta de Cache relacionada e tamanho devo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar um Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de Cache

Depois que o cache é criado e configurado, a próxima etapa é remover a configuração do serviço de Cache gerenciados e adicionar a adicionar a configuração do Cache de relacionada do Azure e referências para que clientes de cache podem acessar o cache.

-   Remover a configuração do serviço de Cache gerenciado
-   Configurar um cliente de cache usando o pacote de NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do serviço de Cache gerenciado

Antes dos aplicativos cliente podem ser configurados para Cache de relacionada do Azure, a configuração do serviço de Cache gerenciados existente e referências de assembly devem ser removidas Desinstalando o pacote gerenciado NuGet de serviço de Cache.

Para desinstalar o pacote gerenciado NuGet de serviço de Cache, clique com botão direito do projeto de cliente no **Solution Explorer** e escolha **Gerenciar pacotes do NuGet**. Selecione o nó **pacotes instalados** e digite W**indowsAzure.Caching** na caixa Pesquisar pacotes instalados. Selecione **Windows** **Azure Cache** (ou do **Windows** **Azure cache** dependendo da versão do pacote do NuGet), clique em **desinstalar**e, em seguida, clique em **Fechar**.

![Desinstalar o pacote de NuGet do serviço de Cache gerenciado Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote de gerenciado NuGet de serviço de Cache remove os conjuntos de serviço de Cache gerenciados e as entradas do serviço de Cache gerenciados na App ou Web. config do aplicativo cliente. Porque algumas configurações personalizadas não podem ser removidas ao desinstalar o pacote NuGet, abra Web. config ou App e certifique-se de que os seguintes elementos são completamente removidos.

Certifique-se de que o `dataCacheClients` entrada é removida do `configSections` elemento. Não remova todo o `configSections` elemento; remover o `dataCacheClients` entrada, se ele estiver presente.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Certifique-se de que o `dataCacheClients` seção é removida. O `dataCacheClients` seção será semelhante ao seguinte exemplo.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Após a configuração do serviço de Cache gerenciado é removida, você pode configurar o cliente de cache conforme descrito na seção a seguir.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache usando o pacote de NuGet StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar código gerenciado serviço de Cache

A API para o cliente de cache de StackExchange.Redis é semelhante ao serviço de Cache gerenciado. Esta seção fornece uma visão geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Conectar-se para o cache usando a classe de ConnectionMultiplexer

No serviço de Cache gerenciado, as conexões com o cache foram feitas pela `DataCacheFactory` e `DataCache` classes. Em Cache relacionada Azure, essas conexões são gerenciadas pela `ConnectionMultiplexer` classe.

Adicione o seguinte usando instrução na parte superior de qualquer arquivo do qual você deseja acessar o cache.

    using StackExchange.Redis
                                
Se este namespace não for resolvido, certifique-se de que você adicionou o pacote StackExchange.Redis NuGet conforme descrito em [Configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Observe que o cliente StackExchange.Redis requer .NET Framework 4 ou superior.

Para se conectar a uma instância do Azure relacionada Cache, chamada estática `ConnectionMultiplexer.Connect` método e passar o ponto de extremidade e a chave. Uma abordagem para compartilhamento de um `ConnectionMultiplexer` instância em seu aplicativo é ter uma propriedade estática que retorna uma instância conectada, semelhante ao seguinte exemplo. Isso fornece uma maneira de threads inicializar apenas um único conectado `ConnectionMultiplexer` instância. Neste exemplo `abortConnect` é definida como false, o que significa que a chamada seja bem-sucedida, mesmo se uma conexão para o cache não é estabelecida. Um recurso chave do `ConnectionMultiplexer` é que ela será automaticamente restaurar a conectividade com o cache de uma vez o problema de rede ou outras causas são resolvidas.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

O ponto de extremidade do cache, chaves e portas podem ser obtidas a lâmina de **Cache relacionada** para a sua instância de cache. Para obter mais informações, consulte [Propriedades de Cache relacionada](cache-configure.md#properties).

Depois que a conexão for estabelecida, retorna uma referência para o banco de dados do cache relacionada chamando a `ConnectionMultiplexer.GetDatabase` método. O objeto retornado da `GetDatabase` método é um objeto de passagem leve e não precisam ser armazenados.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

O cliente StackExchange.Redis usa o `RedisKey` e `RedisValue` tipos para acessar e armazenar itens no cache. Esses tipos mapeiam em tipos de idioma mais primitivos, incluindo a cadeia de caracteres e geralmente não são usados diretamente. Relacionada cadeias de caracteres são o tipo mais básico de valor relacionada e podem conter vários tipos de dados, incluindo fluxos binários serializados, e enquanto você não pode usar o tipo diretamente, você usará métodos que contêm `String` no nome. Para tipos de dados mais primitivos armazenar e recuperar itens do cache usando o `StringSet` e `StringGet` métodos, a menos que você está armazenando coleções ou outros tipos de dados relacionada no cache. 

`StringSet`e `StringGet` são muito semelhantes ao serviço de Cache gerenciado `Put` e `Get` métodos, com uma grande diferença de que antes de definir e obter um objeto .NET no cache deve serializá-lo primeiro. 

Ao chamar `StringGet`, se o objeto existir, ele será retornado e se não estiver, null será retornado. Nesse caso, você pode recuperar o valor da fonte de dados desejado e armazená-la no cache para uso posterior. Isso é conhecido como o padrão de cache-aside.

Para especificar a validade de um item no cache, use o `TimeSpan` parâmetro da `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Cache de relacionada Azure pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes de um objeto .NET pode ser armazenados em cache devem ser serializada. Isso é responsabilidade do desenvolvedor do aplicativo. Isso permite a flexibilidade de desenvolvedor nas opções do serializador. Para obter mais informações e códigos de amostra, consulte [trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrar o estado da sessão ASP.NET e o cache de saída para Cache relacionada do Azure

Cache Azure relacionada tem provedores para o estado de sessão do ASP.NET e o cache de saída de página. Para migrar seu aplicativo que usa as versões de serviço de Cache gerenciados desses provedores, primeiro remova as seções existentes da Web. config e configure as versões do Azure relacionada Cache dos provedores. Para obter instruções sobre como usar os provedores Azure relacionada Cache ASP.NET, consulte o [Provedor de estado de sessão do ASP.NET para Azure relacionada Cache](cache-aspnet-session-state-provider.md) e [Provedor de Cache de saída do ASP.NET para Azure relacionada Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Próximas etapas

Explore a [documentação do Azure relacionada Cache](https://azure.microsoft.com/documentation/services/cache/) para tutoriais, exemplos, vídeos e muito mais.

