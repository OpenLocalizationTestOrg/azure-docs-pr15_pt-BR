<properties 
    pageTitle="Como usar o Cache relacionada Azure | Microsoft Azure" 
    description="Aprenda a melhorar o desempenho de seus aplicativos do Azure com Cache relacionada do Azure" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Como usar o Cache relacionada Azure

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este guia mostra como começar a usar o **Azure relacionada Cache**. Cache de relacionada do Microsoft Azure baseia-se na fonte de abrir popular Cache relacionada. Ele fornece acesso a um cache seguro e dedicado relacionada, gerenciado pela Microsoft. Um cache criado usando o Azure relacionada Cache é acessível em qualquer aplicativo do Microsoft Azure.

Cache do Microsoft Azure relacionada está disponível nos seguintes níveis:

-   **Básicas** – nó único. Vários tamanhos até 53 GB.
-   **Padrão** – dois nós primário/réplica. Vários tamanhos até 53 GB. 99,9 SLA %.
-   **Premium** – dois nós primário/réplica com até 10 fragmentos. Vários tamanhos de 6 GB para 530 GB (Fale conosco para obter mais informações). Todos os recursos de nível padrão e mais incluindo suporte para [relacionada cluster](cache-how-to-premium-clustering.md), [relacionada persistência](cache-how-to-premium-persistence.md)e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada nível é diferente em termos de recursos e preços. Para obter informações sobre preços, consulte [Detalhes de preços do Cache][].

Este guia mostra como usar o cliente de [StackExchange.Redis][] usando C\# código. Os cenários cobertos incluem **criar e configurar um cache**, **Configurando clientes de cache**e **Adicionando e removendo objetos do cache**. Para obter mais informações sobre como usar o Azure relacionada Cache, consulte a seção [Próximas etapas][] . Para um tutorial passo a passo da criação de um ASP.NET MVC web app com o Cache relacionada, consulte [como criar um aplicativo Web com Cache relacionada](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Introdução ao Azure relacionada Cache

Introdução ao Azure relacionada Cache é fácil. Para começar, você provisionar e configurar um cache. A seguir, você configurar os clientes de cache para que possam acessar o cache. Quando os clientes de cache estão configurados, você pode começar a trabalhar com eles.

-   [Criar o cache][]
-   [Configurar os clientes de cache][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Criar um cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para acessar o cache depois que ele é criado

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para obter mais informações sobre como configurar seu cache, veja [como configurar Azure relacionada Cache](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Depois de seu projeto de cliente está configurado para armazenamento em cache, você pode usar as técnicas descritas nas seções a seguir para trabalhar com seu cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Trabalhando com Caches

As etapas desta seção descrevem como executar tarefas comuns com Cache.

-   [Conectar-se ao cache][]
-   [Adicionar e recuperar objetos do cache][]
-   [Trabalhar com objetos .NET no cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Conectar-se ao cache

Para trabalhar programaticamente com um cache, você precisa de uma referência para o cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar o cliente de StackExchange.Redis para acessar um Cache do Azure relacionada.

    using StackExchange.Redis;

>[AZURE.NOTE] O cliente StackExchange.Redis requer .NET Framework 4 ou superior.

A conexão para o Cache do Azure relacionada é gerenciada pela `ConnectionMultiplexer` classe. Essa classe foi projetada para ser compartilhado e reutilizadas em todo o seu aplicativo cliente e não precisa ser criado em uma base por operação. 

Para se conectar a um Cache de relacionada do Azure e ser retornada uma instância de uma conectada `ConnectionMultiplexer`, chamar estática `Connect` método e passar o ponto de extremidade de cache e a chave, como o exemplo a seguir. Use a tecla gerada a partir do Portal do Azure como o parâmetro de senha.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Aviso: Nunca credenciais do repositório no código-fonte. Para manter este exemplo simples, eu estou mostrando-as no código-fonte. Consulte [como cadeias de caracteres de aplicativo e o trabalho de cadeias de caracteres de Conexão][] para obter informações sobre como armazenar credenciais.

Se você não quiser usar SSL, definir `ssl=false` ou omitir o `ssl` parâmetro.

>[AZURE.NOTE] A porta não SSL é desabilitada por padrão para novos caches. Para obter instruções sobre como ativar a porta não SSL, consulte as [Portas de acesso](cache-configure.md#access-ports)..

Uma abordagem para compartilhamento de um `ConnectionMultiplexer` instância em seu aplicativo é ter uma propriedade estática que retorna uma instância conectada, semelhante ao seguinte exemplo. Isso fornece uma maneira de threads inicializar apenas um único conectado `ConnectionMultiplexer` instância. Nesses exemplos `abortConnect` é definida como false, o que significa que a chamada seja bem-sucedida, mesmo se uma conexão para o Cache do Azure relacionada não é estabelecida. Um recurso chave do `ConnectionMultiplexer` é que ela será automaticamente restaurar a conectividade com o cache de uma vez o problema de rede ou outras causas são resolvidas.

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

Para obter mais informações sobre opções de configuração de conexão avançada, consulte [modelo de configuração de StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Depois que a conexão for estabelecida, retorna uma referência para o banco de dados do cache relacionada chamando a `ConnectionMultiplexer.GetDatabase` método. O objeto retornado da `GetDatabase` método é um objeto de passagem leve e não precisam ser armazenados.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Agora que você sabe como se conectar a uma instância de Cache relacionada do Azure e retornar uma referência para o banco de dados do cache, vamos dar uma olhada em trabalhando com o cache.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Adicionar e recuperar objetos do cache

Itens podem ser armazenados em e recuperadas de um cache usando o `StringSet` e `StringGet` métodos.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Relacionada a maioria dos dados como cadeias de caracteres relacionada, mas essas cadeias de caracteres podem conter vários tipos de dados, incluindo serializados dados binários, que podem ser usados quando armazenar .NET objetos no cache de lojas.

Ao chamar `StringGet`, se o objeto existir, ele será retornado, e se não estiver, `null` será retornado. Nesse caso, você pode recuperar o valor da fonte de dados desejado e armazená-la no cache para uso posterior. Isso é conhecido como o padrão de cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar a validade de um item no cache, use o `TimeSpan` parâmetro da `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET no cache

Cache de relacionada Azure pode armazenar em cache objetos .NET, bem como tipos de dados primitivos, mas antes de um objeto .NET pode ser armazenados em cache devem ser serializada. Isso é responsabilidade do desenvolvedor do aplicativo e oferece a flexibilidade do desenvolvedor na opção do serializador.

Uma maneira simple de serializar objetos é usar o `JsonConvert` métodos de serialização em [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializar para e do JSON. O exemplo a seguir mostra um get e set usando um `Employee` instância do objeto.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos, siga estes links para saber mais sobre Azure relacionada Cache.

-   Confira os provedores ASP.NET para Azure relacionada Cache.
    -   [Provedor de estado de sessão do Azure relacionada](cache-aspnet-session-state-provider.md)
    -   [Azure relacionada provedor de Cache do Cache de saída do ASP.NET](cache-aspnet-output-cache-provider.md)
-   [Habilitar o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do seu cache. Você pode exibir as métricas no Portal do Azure e você também pode [baixar e examine](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -los usando as ferramentas de sua escolha.
-   Confira a [documentação do cliente de cache de StackExchange.Redis][].
    -   Cache de relacionada Azure podem ser acessado de muitos clientes relacionada e linguagens de desenvolvimento. Para obter mais informações, consulte [http://redis.io/clients][].
-   Azure Cache relacionada também pode ser usado com ferramentas tais como Redsmin e relacionada Gerenciador de área de trabalho e serviços de terceiros.
    -   Para obter mais informações sobre Redsmin, veja [como recuperar uma cadeia de conexão do Azure relacionada e usá-lo com Redsmin][].
    -   Acessar e inspecionar seus dados no Azure relacionada Cache com uma interface gráfica usando [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Consulte a documentação [relacionada][] e leia sobre [relacionada tipos de dados][] e [uma introdução quinze minutos de relacionada tipos de dados][].



<!-- INTRA-TOPIC LINKS -->
[Próximas etapas]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Criar o cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Conectar-se ao cache]: #connect-to-cache
[Adicionar e recuperar objetos do cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/Clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Como recuperar uma cadeia de conexão do Azure relacionada e usá-lo com Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes de preços de cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentação do cliente de cache de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Relacionada]: http://redis.io/documentation
[Relacionada tipos de dados]: http://redis.io/topics/data-types
[uma introdução de quinze minutos para tipos de dados relacionada]: http://redis.io/topics/data-types-intro

[Como funcionam as cadeias de caracteres de aplicativo e cadeias de caracteres de Conexão]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


