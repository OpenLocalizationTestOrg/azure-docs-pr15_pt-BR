<properties 
    pageTitle="Perguntas Frequentes de Cache relacionada Azure | Microsoft Azure" 
    description="Saiba as respostas para perguntas comuns, padrões e práticas recomendadas para Cache relacionada do Azure" 
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
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Cache relacionada Azure perguntas Frequentes

Aprenda as respostas para perguntas comuns, padrões e práticas recomendadas para Azure relacionada Cache. 


## <a name="what-if-my-question-isnt-answered-here"></a>E se minha dúvida não seja resolvida aqui?

Se sua dúvida não estiver listada aqui, informe-nos e ajudaremos você a encontrar uma resposta.

-   Você pode postar uma pergunta no [segmento Disqus](#comments) no final dessas perguntas Frequentes e entre em contato com a equipe de Cache do Azure e outros membros da comunidade sobre este artigo.
-   Para alcançar um público maior, você pode postar uma pergunta no [Fórum do MSDN de Cache do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e entre em contato com a equipe de Cache do Azure e outros membros da comunidade.
-   Se você quiser fazer uma solicitação de recurso, você pode enviar suas solicitações e ideias [relacionada](https://feedback.azure.com/forums/169382-cache)a voz de usuário de Cache do Azure.
-   Você também pode enviar um email para nós no [Azure Cache externo comentários](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Noções básicas de Cache relacionada Azure

As perguntas frequentes desta seção abordam alguns das noções básicas do Azure relacionada Cache.

-    [O que é Azure relacionada Cache?](#what-is-azure-redis-cache)
-    [Como pode começar a Azure relacionada Cache?](#how-can-i-get-started-with-azure-redis-cache)

As seguintes perguntas frequentes sobre abordam conceitos básicos e perguntas sobre o Cache de relacionada do Azure e são respondidas em uma das outras seções de perguntas Frequentes.

-   [Qual oferta de Cache relacionada e tamanho devo usar?](#what-redis-cache-offering-and-size-should-i-use)
-   [Quais clientes de cache relacionada pode usar?](#what-redis-cache-clients-can-i-use)
-   [Existe um emulador local para Azure relacionada Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Planejamento de perguntas frequentes

-   [Qual oferta de Cache relacionada e tamanho devo usar?](#what-redis-cache-offering-and-size-should-i-use)
-   [Desempenho de Cache relacionada Azure](#azure-redis-cache-performance)
-   [Em qual região posso localizar meu cache?](#in-what-region-should-i-locate-my-cache)
-   [Como estou cobrado para Azure relacionada Cache?](#how-am-i-billed-for-azure-redis-cache)
-   [Pode usar o Cache do Azure relacionada com o Azure governo nuvem ou nuvem do Azure China?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Perguntas frequentes de desenvolvimento

-   [O que fazer as opções de configuração de StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [Quais clientes de cache relacionada pode usar?](#what-redis-cache-clients-can-i-use)
-   [Existe um emulador local para Azure relacionada Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Como é possível executar comandos relacionada?](#how-can-i-run-redis-commands)
-   [Porque o Cache de relacionada Azure não tem uma referência de biblioteca de classe do MSDN como alguns dos outros serviços Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [Pode usar o Azure relacionada Cache como um cache de sessão PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>Perguntas frequentes sobre segurança

-   [Quando devo habilitar a porta não SSL para conectar ao relacionada?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>Perguntas frequentes de produção

-   [Quais são algumas práticas recomendadas de produção?](#what-are-some-production-best-practices)
-   [Quais são algumas das considerações ao usar comandos comuns de relacionada?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Como padrão de referência e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Detalhes importantes sobre o crescimento de pool](#important-details-about-threadpool-growth)
-   [Habilitar GC de servidor obter mais produtividade no cliente ao usar StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Monitoramento e perguntas frequentes de solução de problemas

As perguntas frequentes desta seção cobrem monitoramento e perguntas de solução de problemas comuns. Para obter mais informações sobre monitoramento e suas instâncias do Azure relacionada Cache de solução de problemas, consulte [como monitorar Azure relacionada Cache](cache-how-to-monitor.md) e [como solucionar problemas com o Azure relacionada Cache](cache-how-to-troubleshoot.md).

-   [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Cache diagnóstico armazenamento configurações de minha conta alteradas, o que aconteceu?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Por que o diagnóstico está habilitado para alguns caches de novo, mas não outros?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Por que estou vendo tempos limite?](#why-am-i-seeing-timeouts)
-   [Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Perguntas frequentes de oferta de Cache anterior

-   [Qual oferta de Cache do Azure é ideal para mim?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>O que é Azure relacionada Cache?

Cache Azure relacionada se baseia a popular código-fonte aberto [relacionada cache](http://redis.io). Ele fornece acesso a um seguro e dedicado relacionada cache, gerenciado pela Microsoft e acessível de qualquer aplicativo do Azure. Para obter uma visão mais detalhada, consulte a página de produto do [Azure relacionada Cache](https://azure.microsoft.com/services/cache/) no Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Como pode começar a Azure relacionada Cache?

Há várias maneiras que você possa começar com o Azure relacionada Cache.

-    Você pode fazer check-out de um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node](cache-nodejs-get-started.md)e [Python](cache-python-get-started.md). 
-    Você pode assistir [como construir alto desempenho usando o Microsoft Azure relacionada Cache Apps](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Você pode verificar a documentação do cliente para os clientes que correspondem a linguagem de desenvolvimento do seu projeto para ver como usar relacionada. Existem muitos clientes relacionada que podem ser usados com o Azure relacionada Cache. Para obter uma lista de clientes relacionada, consulte [http://redis.io/clients](http://redis.io/clients).


Se você ainda não tiver uma conta do Azure, você pode:

-    [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que podem ser usados para experimentar serviços Azure pagos. Mesmo após os créditos são usados para cima, você pode manter a conta e usar recursos e serviços Azure gratuitos.
-    [Ativar o Visual Studio benefícios do assinante](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Sua assinatura do MSDN fornece créditos todo mês que você pode usar para serviços do Azure pagos.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Qual oferta de Cache relacionada e tamanho devo usar?
Cada oferta Azure relacionada Cache fornece níveis diferentes de **tamanho**, **a largura de banda**, **alta disponibilidade**e opções de **SLA** .

A seguir algumas considerações para escolher uma oferta de Cache.

-   **Memória**: níveis padrão e o básicas oferecem 250 MB 53 GB. A camada de Premium oferece até 530 GB com mais disponíveis [na solicitação](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, consulte [Cache preços do Azure relacionada](https://azure.microsoft.com/pricing/details/cache/).
-   **Desempenho de rede**: se você tiver uma carga de trabalho que requer alta produtividade a camada Premium oferece mais largura de banda em comparação com o padrão ou básico. Também em cada nível caches de tamanhos maiores têm mais largura de banda devido a máquina virtual subjacente que hospeda o cache. Consulte a [tabela a seguir](#cache-performance) para obter mais informações.
-   **Produtividade**: nível Premium o oferece a taxa de transferência disponível máxima. Se o servidor de cache ou cliente atingir os limites de largura de banda, você receberá tempos limite no lado do cliente. Para obter mais informações, consulte a tabela a seguir.
-   **Alta disponibilidade/SLA**: Azure relacionada Cache garante que um cache padrão/Premium estará disponível pelo menos 99,9% das vezes. Para saber mais sobre nosso SLA, consulte [Cache preços do Azure relacionada](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA cobre apenas a conectividade com os pontos de extremidade do Cache. O SLA não aborda a proteção contra perda de dados. É recomendável usar o recurso de persistência de dados relacionada na camada Premium para aumentar a resiliência contra perda de dados.
-   **Persistência de dados relacionada**: nível Premium o permitirá que você manter os dados de cache em uma conta de armazenamento do Azure. Em um cache Basic/padrão todos os dados são armazenados apenas na memória. No caso de infraestrutura subjacente questões lá podem ser possibilidade de perda de dados. É recomendável usar o recurso de persistência de dados relacionada na camada Premium para aumentar a resiliência contra perda de dados. Cache de relacionada Azure oferece RDB e AOF (em breve) opções no persistence relacionada. Para obter mais informações, consulte [como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md).
-   **Cluster relacionada**: criar armazena maior que 53 GB ou aos dados de fragmentar em vários nós relacionada, você pode usar relacionada clusters, que está disponível no nível Premium. Cada nó consiste em um par de cache primário/réplica para alta disponibilidade. Para obter mais informações, consulte [como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md).
-   **Segurança aprimorada e isolamento de rede**: a implantação do Azure Virtual rede (VNET) fornece segurança aprimorada e isolamento para seu Cache relacionada do Azure, bem como sub-redes, políticas de controle de acesso, e outros recursos para obter mais restringem o acesso. Para obter mais informações, consulte [como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md).
-   **Configurar relacionada**: níveis Standard e Premium, você pode configurar relacionada para notificações de Keyspace.
-   **Número máximo de conexões de cliente**: nível Premium a oferece o número máximo de clientes que podem se conectar a relacionada, com um número maior de conexões para caches de tamanho maiores. [Consulte a página de preços de detalhes](https://azure.microsoft.com/pricing/details/cache/).
-   **Núcleo dedicado para servidor relacionada**: na camada Premium todos os tamanhos de cache tem um núcleo dedicado para relacionada. No Basic/padrão níveis C1 dimensionar em acima possui um núcleo dedicado para servidor relacionada.
-   **Relacionada é o único thread** então ter mais de duas cores não fornecem benefícios adicionais sobre ter apenas duas cores, mas tamanhos maiores de máquina virtual geralmente têm mais largura de banda que menores. Se o servidor de cache ou cliente atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
-   **Melhorias de desempenho**: Caches na camada Premium são implantados em hardware que possui processadores mais rápidos e proporciona melhor desempenho comparado com a camada Basic ou padrão. Caches de nível Premium têm latências menores e maior produtividade.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Desempenho de Cache relacionada Azure

A tabela a seguir mostra os valores de largura de banda máxima observados durante o teste de vários tamanhos de padrão e Premium armazena usando `redis-benchmark.exe` de uma VM Iaas contra o ponto de extremidade do Azure relacionada Cache. Observe que esses valores não são garantia e há nenhuma SLA para esses números, mas deve ser típico. Você deve carregar testar seu próprio aplicativo para determinar o tamanho do cache certo para o seu aplicativo.

Desta tabela nós pode desenhar as seguintes conclusões.

-   Taxa de transferência para os caches que tenham o mesmo tamanho é maior na camada Premium em comparação com a camada padrão. Por exemplo, com um Cache de 6 GB, produtividade de P1 é 140K RPS em comparação com 49 K para C3.
-   Relacionada clusters, produtividade aumenta linear à medida que aumenta o número de fragmentos (nós) no cluster. Por exemplo, se você criar um cluster de P4 de 10 fragmentos, a taxa de transferência disponível é 250K * 10 = 2,5 milhões RPS.
-   Taxa de transferência para tamanhos maiores de chave é maior na camada Premium em comparação com a camada padrão.

| Nível de preço             | Tamanho   | Cores de CPU | Largura de banda disponível                                    | Tamanho da chave de 1 KB                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tamanhos de cache padrão** |        |           | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** | **Solicitações por segundo (RPS)**            |
| C0                       | 250 MB | Compartilhado    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GB   | 1         | 100 / 12.5                                             | 12200                                    |
| C2                       | 2,5 GB | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62,5                                             | 61000                                    |
| C5                       | 26 GB  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | GB 53  | 8         | 2000 / 250                                             | 150000                                   |
| **Tamanhos de cache Premium**  |        | **Cores de CPU por fragmentar**  |                                         | **Solicitações por segundo (RPS), por fragmentar** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | 26 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | GB 53  | 8         | 4000 / 500                                             | 250000                                   |


Para obter instruções sobre como baixar as ferramentas relacionada como `redis-benchmark.exe`, consulte o [como executar comandos relacionada?](#cache-commands) seção.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>Em qual região posso localizar meu cache?

Para obter melhor desempenho e menor latência, localize o Cache do Azure relacionada na mesma região como seu aplicativo de cliente do cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Como estou cobrado para Azure relacionada Cache?

Preços do Azure Cache relacionada estão [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista preços como taxa de hora. Caches serão cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>Pode usar o Cache do Azure relacionada com o Azure governo nuvem ou nuvem do Azure China?

Sim, Azure relacionada Cache está disponível em nuvem de governo do Azure e o Azure China nuvem. Observe que as URLs para acessar e gerenciar o Cache do Azure relacionada são diferentes em nuvem de governo do Azure e nuvem do Azure China em comparação com a nuvem pública do Azure. Para obter mais informações sobre as considerações ao usar o Cache do Azure relacionada com nuvem de governo do Azure e nuvem do Azure China, consulte [Bancos de dados do governo Azure - Cache relacionada do Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache) e [Azure China Cloud - Azure relacionada Cache](https://www.azure.cn/documentation/services/redis-cache/).

Para obter informações sobre como usar o Cache do Azure relacionada com o PowerShell na nuvem do governo do Azure e nuvem do Azure China, consulte [como conectar à nuvem do governo Azure ou Azure China na nuvem](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazer as opções de configuração de StackExchange.Redis?

StackExchange.Redis tem várias opções. Esta seção fala sobre algumas das configurações comuns. Para obter informações mais detalhadas sobre opções de StackExchange.Redis, consulte [configuração de StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descrição|Recomendação
---|---|---
AbortOnConnectFail|Quando definido como true, a conexão não irá reconectar após uma falha de rede.|Definida como false e permita a StackExchange.Redis reconectar automaticamente.
ConnectRetry|O número de vezes para repetir tentativas de conexão durante inicial se conectar.| Consulte as anotações abaixo para orientação. |
ConnectTimeout|Tempo limite em ms para operações de conexão.| Consulte as anotações abaixo para orientação. |

Na maioria dos casos, os valores padrão do cliente são suficientes. Você pode ajustar as opções com base na sua carga de trabalho.

-   **Tentativas**
    -   Para ConnectRetry e ConnectTimeout a orientação geral é falha rápida e tente novamente. Isso se baseia na sua carga de trabalho e quanto tempo na média ele leva para seu cliente emitir um comando relacionada e recebe uma resposta.
    -   Deixe o StackExchange.Redis reconectar-se automaticamente em vez de verificar o status de conexão e reconectar-se. **Evite o uso da propriedade ConnectionMultiplexer.IsConnected**.
    -   Snowballing - às vezes, você pode enfrentar algum onde você está tentando e isso snowballs e nunca recupera. Nesse caso, você deve considerar usando um algoritmo de repetir retirada exponencial, conforme descrito em [Repetir orientação geral](best-practices-retry-general.md) publicado pelo grupo Microsoft Patterns & Practices.
-   **Valores de tempo limite**
    -   Considere a possibilidade de sua carga de trabalho e defina os valores adequadamente. Se você estiver armazenando valores grandes, defina o tempo limite para um valor maior.
    -   Definir `AbortOnConnectFail` StackExchange.Redis false e permita que reconectar-se para você.
    -   Use uma única instância de ConnectionMultiplexer para o aplicativo. Você pode usar um LazyConnection para criar uma única instância que é retornada por uma propriedade de Conexão, conforme mostrado em [conectar-se para o cache usando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Definir o `ConnectionMultiplexer.ClientName` propriedade para um nome exclusivo de ocorrência do aplicativo para fins de diagnóstico.
    -   Usar várias `ConnectionMultiplexer` instâncias para cargas de trabalho personalizadas.
    -   Se você tiver carga variável em seu aplicativo, você pode seguir este modelo. Por exemplo:
    -   Você pode ter um multiplexador para lidar com chaves grandes.
    -   Você pode ter um multiplexador para lidar com teclas pequenas.
    -   Você pode definir valores diferentes para tempos limite de conexão e uma lógica de repetição para cada ConnectionMultiplexer que você usar.
    -   Definir o `ClientName` propriedade em cada multiplexador para ajudar no diagnóstico.
    -   Isso levará mais simplificada latência por `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Quais clientes de cache relacionada pode usar?

Uma das excelentes coisas sobre relacionada, há muitos clientes suporte a muitos idiomas de desenvolvimento diferentes. Para obter uma lista atual de clientes, consulte [relacionada clientes](http://redis.io/clients). Para tutoriais que abordam vários idiomas diferentes e clientes, veja [como usar o Cache relacionada do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no idioma desejado o seletor de exibição do idioma na parte superior do artigo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Existe um emulador local para Azure relacionada Cache?

Não há nenhuma emulador local para Azure relacionada Cache, mas você pode executar a versão MSOpenTech do server.exe relacionada a [relacionada ferramentas de linha de comando](https://github.com/MSOpenTech/redis/releases/) no computador local e conectá-la para obter uma experiência semelhante ao emulador cache local, conforme mostrado no exemplo a seguir.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcionalmente, você pode configurar um arquivo de [redis.conf](http://redis.io/topics/config) para corresponder mais detalhadamente as [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) para o Cache de relacionada do Azure online se desejado.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Como é possível executar comandos relacionada?

Você pode usar qualquer um dos comandos listados no [relacionada comandos](http://redis.io/commands#) , exceto para os comandos listados em [relacionada comandos que não têm suportados no Azure relacionada Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para executar comandos relacionada, você tem várias opções.

-   Se você tiver um cache Standard ou Premium, você pode executar comandos relacionada usando o [Console relacionada](cache-configure.md#redis-console). Isso fornece uma maneira segura para executar comandos relacionada no portal do Azure.
-   Você também pode usar as ferramentas de linha de comando relacionada. Para usá-los, execute as seguintes etapas.
-   Baixe o [relacionada ferramentas de linha de comando](https://github.com/MSOpenTech/redis/releases/).
-   Conectar-se à cache usando `redis-cli.exe`. Passar o ponto de extremidade de cache usando que alternar a -h e a chave usando - a como mostrado no exemplo a seguir.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Observe que as ferramentas de linha de comando relacionada não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar com segurança as ferramentas para a porta SSL seguindo as instruções na postagem do blog [Anunciando provedor de estado de sessão ASP.NET a versão de visualização relacionada](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Porque o Cache de relacionada Azure não tem uma referência de biblioteca de classe do MSDN como alguns dos outros serviços Azure?

Cache de relacionada do Microsoft Azure baseia-se na fonte de abrir popular Cache relacionada e pode ser acessado por uma ampla variedade de [relacionada clientes](http://redis.io/clients) que estão disponíveis para muitas linguagens de programação. Cada cliente tem sua própria API que faz chamadas para a instância de cache relacionada usando [relacionada comandos](http://redis.io/commands).

Como cada cliente é diferente, não há não uma referência de classe centralizado no MSDN; em vez disso, cada cliente mantém sua própria documentação de referência. Além da documentação de referência, há vários tutoriais mostrando como começar a usar o Cache de relacionada de Azure usando diferentes idiomas e clientes do cache. Para acessar esses tutoriais, consulte [como usar o Cache relacionada do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no idioma desejado o seletor de exibição do idioma na parte superior do artigo.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Pode usar o Azure relacionada Cache como um cache de sessão PHP?

Sim, para usar o Azure relacionada Cache como um cache de sessão PHP, especifique a cadeia de conexão à instância do Azure relacionada Cache em `session.save_path`.

>[AZURE.IMPORTANT] Ao usar o Azure relacionada Cache como um cache de sessão PHP, você deve URL codificar a chave de segurança usada para conexão com o cache, conforme mostrado no exemplo a seguir.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Se a chave não é codificada de URL, você pode receber uma exceção similar ao seguinte:`Failed to parse session.save_path`

Para obter mais informações sobre como usar o Cache relacionada como um cache de sessão PHP com o cliente de PhpRedis, consulte [manipulador de sessão de PHP](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando devo habilitar a porta não SSL para conectar ao relacionada?

Relacionada servidor não dá suporte a SSL prontos para uso, mas não de Cache relacionada do Azure. Se você estiver se conectando ao Azure relacionada Cache e seu cliente dá suporte a SSL, como StackExchange.Redis, você deve usar SSL.

Observe que a porta não SSL é desabilitada por padrão para novas instâncias de Cache relacionada do Azure. Se o seu cliente não oferece suporte a SSL, você deve habilitar a porta não SSL seguindo as instruções na seção [portas de acesso](cache-configure.md#access-ports) do artigo [Configurar um cache no Cache relacionada do Azure](cache-configure.md) .

Relacionada ferramentas tais como `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar com segurança as ferramentas para a porta SSL seguindo as instruções na postagem do blog [Anunciando provedor de estado de sessão ASP.NET a versão de visualização relacionada](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Para obter instruções sobre como baixar as ferramentas relacionada, consulte o [como executar comandos relacionada?](#cache-commands) seção.



### <a name="what-are-some-production-best-practices"></a>Quais são algumas práticas recomendadas de produção?

-   [Práticas recomendadas StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Conceitos e configuração](#configuration-and-concepts)
-   [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas StackExchange.Redis

-   Definir `AbortConnect` como false, deixe o ConnectionMultiplexer reconectar automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Reutilizar o ConnectionMultiplexer - não criar um novo para cada solicitação. A `Lazy<ConnectionMultiplexer>` padrão [mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é altamente recomendável.
-   Relacionada funciona melhor com valores menores, portanto, considere talhar os dados maiores em várias teclas. [Isso relacionada discussão](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb é considerado "grande". Leia [Este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por valores grandes.
-   Defina as [configurações de pool](#important-details-about-threadpool-growth) para evitar tempos limite.
-   Use pelo menos o connectTimeout padrão de 5 segundos. Isso seria dará StackExchange.Redis tempo suficiente para restabelecer a conexão, no caso de um blip de rede.
-   Lembre-se de que os custos de desempenho associados operações diferentes que você está executando. Por exemplo, o `KEYS` comando é uma operação de (n) e deve-se evitar. O [site de redis.io](http://redis.io/commands/) tem detalhes sobre a complexidade de tempo para cada operação que ele suporta. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Conceitos e configuração

-   Use o padrão ou nível Premium para sistemas de produção. A camada básica é um sistema de nó único com nenhuma replicação de dados e não há SLA. Além disso, use pelo menos um cache de C1. Caches C0 realmente são destinados a cenários de desenvolvimento/teste simples.
-   Lembre-se de que relacionada é um armazenamento de dados **Na memória** . Leia [Este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que você estiverem cientes dos cenários onde pode ocorrer perda de dados.
-   Desenvolva seu sistema de forma que ele pode manipular conexão pontos de luz [devido a patches e failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teste de desempenho

-   Iniciar usando `redis-benchmark.exe` para obter uma aparência para produtividade possível antes de gravar seu próprio desempenho testes. Observação que essa referência relacionada não suporta SSL, para que você tem para [Ativar a porta SSL não por meio do portal Azure](cache-configure.md#access-ports) antes de você executará o teste. Para obter exemplos, consulte [como padrão de referência e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   O cliente máquina virtual usado para teste deve estar na mesma região como sua instância de cache relacionada.
-   Recomendamos usar Dv2 máquina virtual série para seu cliente, conforme eles tenham hardware melhor e fornecerão os melhores resultados.
-   Verifique se seu cliente de máquina virtual que você escolher tem pelo menos tantos computação e capacidade de largura de banda como o cache que você está testando. 
-   Habilite VRSS na máquina do cliente se você estiver no Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
-   Instâncias de relacionada de nível Premium serão ter melhor rede latência e produtividade porque estão sendo executados em hardware melhor para CPU e rede.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar comandos comuns de relacionada?

-   Você não deve executar determinados comandos relacionada que levar muito tempo para ser concluída sem entender o impacto desses comandos.
    -   Por exemplo, não execute o comando de [chaves](http://redis.io/commands/keys) em produção como ele pode levar muito tempo para retornar dependendo do número de teclas. Relacionada é um servidor de segmentação única e processa comandos, um por vez. Se você tiver outros comandos emitidos após chaves, elas não serão processadas até relacionada processa o comando de teclas. O [site de redis.io](http://redis.io/commands/) tem detalhes sobre a complexidade de tempo para cada operação que ele suporta. Clique em cada comando para ver a complexidade para cada operação.
-   Tamanhos de chave - devo usar small chave/valores ou grande chave /? Em geral isso depende do cenário. Se seu cenário exigir chaves maiores, em seguida, você pode ajustar o ConnectionTimeout e repetir valores e ajustar sua lógica de repetição. De uma perspectiva de servidor relacionada, valores menores são observados ter melhor desempenho.
-   Isso não significa que você não armazene valores maiores em relacionada; Você deve estar ciente das considerações a seguir. Latências será maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias de ConnectionMultiplexer, cada um configurado com um conjunto diferente de valores de tempo limite e tente novamente, conforme descrito na seção [o que as opções de configuração de StackExchange.Redis fazer](#cache-configuration) anterior.



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como padrão de referência e testar o desempenho do meu cache?

-   [Habilitar o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do seu cache. Você pode exibir as métricas no portal do Azure e você também pode [baixar e examine](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -los usando as ferramentas de sua escolha.
-   Você pode usar benchmark.exe relacionada ao carregar teste seu servidor relacionada.
-   Certifique-se de que a carga de teste de cliente e o cache relacionada estão na mesma região.
-   Use cli.exe relacionada e monitorar o cache usando o comando de informações.
-   Se sua carga está causando a fragmentação de memória alta, em seguida, você deve crescerão para um tamanho maior de cache.
-   Para obter instruções sobre como baixar as ferramentas relacionada, consulte o [como executar comandos relacionada?](#cache-commands) seção.

A seguir é um exemplo do uso de benchmark.exe relacionada. Para obter resultados precisos, execute este comando uma máquina virtual na mesma região como seu cache.

-   Solicitações de definir o em pipeline de teste usando uma carga k 1

    benchmark.exe relacionada - h **yourcache**. redis.cache.windows.net- **yourAccesskey** -t definir - n 1000000 -d 1024 - P 50
    
-   Teste o em pipeline obter solicitações usando uma carga k 1. 
    Observação: Executar o conjunto de teste mostrado acima primeiro para preencher o cache
    
    benchmark.exe relacionada - h **yourcache**. redis.cache.windows.net- **yourAccesskey** -t GET -n 1000000 - d 1024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento de pool

CLR ThreadPool tem dois tipos de threads - "Trabalho" e "Porta de conclusão e/s" (também conhecidos como IOCP) threads. 

-   Segmentos de trabalho são usados quando para coisas como processamento `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)` métodos. Esses threads também são usados por vários componentes no CLR quando trabalho precisa acontecer em um segmento de plano de fundo.
-   Segmentos IOCP são usados quando IO assíncrona acontece (por exemplo, lendo da rede). 

O pool de segmentos fornece novos segmentos de trabalho ou segmentos de conclusão e/s sob demanda (sem qualquer limitação) até atingir a configuração "Mínimo" para cada tipo de segmento. Por padrão, o número mínimo de threads está definido para o número de processadores em um sistema. 

Depois que o número de existentes (ocupado) threads visitas que será o número "mínimo" de threads, ThreadPool aceleração é a taxa na qual insere novos threads para um thread por 500 milissegundos. Isso significa que se seu sistema obtém um fluxo de trabalho que precisam de um segmento IOCP intermitente, ele processará que funcionam muito rapidamente. No entanto, se o fluxo de trabalho intermitente for maior que a configuração "Mínimo" configurada, haverá algum atraso no processamento parte do trabalho, como o pool aguarda uma das duas ações para ocorrer.

1. Um thread existente se torna livre para processar o trabalho.
1. Nenhum thread existente se torna gratuita para 500 ms, então um novo segmento é criado.

Basicamente, isso significa que quando o número de threads ocupados for maior que threads Min, você está pagando provavelmente um atraso de 500 ms antes de tráfego de rede é processado pelo aplicativo. Além disso, é importante observar que, quando um thread existente permanece ocioso por mais de 15 segundos (baseados no que eu lembrar), ele será limpo e esse ciclo de crescimento e perdas pode repetir.

Se olharmos para uma mensagem de erro de exemplo de StackExchange.Redis (build 1.0.450 ou posterior), você verá que ele agora imprime estatísticas de pool (consulte IOCP e TRABALHADOR detalhes abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo acima, você pode ver que segmento IOCP há 6 threads ocupados e o sistema estiver configurado para permitir 4 mínimo threads. Nesse caso, o cliente teria provavelmente visto dois atrasos de 500 ms porque 6 > 4.

Observe que StackExchange.Redis pode acertar tempos limite se crescimento de IOCP ou TRABALHADOR threads obtém limitado.

### <a name="recommendation"></a>Recomendação

Dadas essas informações, é altamente recomendável que os clientes defina o valor de configuração mínima para threads IOCP e TRABALHADOR para algo maior do que o valor padrão. Não oferecemos única orientação sobre o que esse valor deve ser porque o valor correto para um aplicativo será muito alta/baixa para outro aplicativo. Esta configuração também pode afetar o desempenho de outras partes do aplicativos complicados, portanto precisa ajustar essa configuração às suas necessidades específicas de cada cliente. Um bom ponto de partida é 200 ou 300, em seguida, teste e ajustar conforme necessário.

Como essa configuração:

-   No ASP.NET, use a [configuração "minIoThreads"][] sob o `<processModel>` elemento de configuração em Web. config. Se você estiver executando dentro de sites do Azure, essa configuração não é exposta pelas opções de configuração. No entanto, você ainda poderá definir isso programaticamente (veja abaixo) do seu método de Application_Start em global.asax.cs.

> **Observação importante:** o valor especificado no elemento configuração é uma configuração *por núcleo* . Por exemplo, se você tiver uma máquina de 4 núcleo e deseja sua configuração minIOThreads 200 em tempo de execução, você usaria `<processModel minIoThreads="50"/>`.

-   Fora do ASP.NET, use a [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitar GC de servidor obter mais produtividade no cliente ao usar StackExchange.Redis

Habilitar GC de servidor pode otimizar o cliente e forneça o melhor desempenho e a produtividade ao usar StackExchange.Redis. Para obter mais informações sobre o servidor GC e como ativá-lo, consulte os artigos a seguir.

-   [Para habilitar o servidor GC](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Fundamentos de coleta de lixo](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Coleta de lixo e desempenho](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorar a integridade e o desempenho do meu cache?

É possível monitorar instâncias do Microsoft Azure relacionada Cache no [portal do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas para o Startboard, personalizar o intervalo de data e hora de monitoramento gráficos, adicionar remover métricas dos gráficos e definir alertas quando determinadas condições forem atendidas. Para obter mais informações, consulte [Monitor Azure relacionada Cache](cache-how-to-monitor.md).

A seção **suporte + solução de problemas** da lâmina de Cache relacionada **configurações** também contém várias ferramentas para monitoramento e seus caches de solução de problemas. 

-   **Solução de problemas** fornece informações sobre problemas comuns e estratégias para solucioná-los.
-   **Logs de auditoria** fornece informações sobre ações executadas no seu cache. Você também pode usar a filtragem para expandir este modo de exibição para incluir outros recursos.
-   **Integridade do recurso** inspeções seus recursos e informa se ele está funcionando conforme esperado. Para obter mais informações sobre o serviço de integridade do recurso do Azure, consulte [Visão geral de integridade do recurso do Azure](../resource-health/resource-health-overview.md).
-   **Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para o cache.

Essas ferramentas permitem que você monitorar a integridade de suas instâncias de Cache relacionada do Azure e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações, consulte [suporte e solução de problemas de configurações](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Cache diagnóstico armazenamento configurações de minha conta alteradas, o que aconteceu?

Caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico e se a configuração for alterado (Diagnóstico ativado/desativado ou alterando a conta de armazenamento)-se aplica a todos os caches na assinatura que estão na região. Se tem alterado as configurações de diagnóstico para o cache, verifique se as configurações de diagnósticos para outro cache na mesma assinatura e região foi alterado. Uma maneira de verificar é para exibir os logs de auditoria para o cache para um `Write DiagnosticSettings` evento. Para obter mais informações sobre como trabalhar com logs de auditoria, consulte [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) e [operações com o Gerenciador de recursos de auditoria](../resource-group-audit.md). Para obter mais informações sobre como monitorar eventos de Cache relacionada do Azure, consulte [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Por que o diagnóstico está habilitado para alguns caches de novo, mas não outros?

Caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico. Se você criar um novo cache na mesma região e assinatura como cache de outro que tenha diagnóstico habilitado, diagnóstico está habilitado no novo cache usando as mesmas configurações.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Por que estou vendo tempos limite?

Tempos limite ocorrer no cliente que você usa para conversar relacionada. A maior parte relacionada server não vai expirar. Quando um comando é enviado para o servidor relacionada, o comando na fila e servidor relacionada eventualmente seleciona o comando e o executa. No entanto, o cliente pode tempo limite durante este processo e se ele faz uma exceção é gerado em lado da chamada. Para obter mais informações sobre solução de problemas de tempo limite, consulte [solução de problemas de lado cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [exceções de tempo limite de StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que meu cliente foi desconectado do cache?

A seguir estão algumas motivo comum para desconexão de cache.

-   Causas do lado do cliente
    -   O aplicativo cliente foi redistribuído.
    -   O aplicativo cliente realizada uma operação de escala.
        -   No caso de serviços de nuvem ou aplicativos Web, isso pode ser devido a escala automática.
    -   A camada de rede no lado do cliente é alterado.
    -   Ocorreram erros temporárias no cliente ou em nós de rede entre o cliente e servidor.
    -   Os limites de limite de largura de banda foram encontrados.
    -   CPU vinculado operações demorou demais para ser concluída.
-   Faz com que o servidor
    -   No cache padrão oferta, o serviço de Cache de relacionada Azure iniciou um sobre falhas de nó principal para o nó secundário.
    -   Azure foi patches a instância onde o cache foi implantado
        -   Isso pode ser relacionada atualizações de servidor ou gerais de manutenção de máquina virtual.







### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual oferta de Cache do Azure é ideal para mim?

>[AZURE.IMPORTANT]Acordo com as do ano passado [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), serviço serviço de Cache do Azure gerenciado e Cache do Azure na função será desativado em 30 de novembro de 2016. Nossa recomendação é usar o [Azure relacionada Cache](https://azure.microsoft.com/services/cache/). Para obter informações sobre migração, consulte [migrar do serviço de Cache gerenciados Azure relacionada cache](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Cache relacionada Azure
Cache Azure relacionada é disponibilizado em tamanhos até 53 GB e tem um SLA de 99,9% de disponibilidade. O novo [nível de premium](cache-premium-tier-intro.md) oferece tamanhos até 530 GB e suporte para clusters, VNET e persistência, com um SLA 99,9%.

Cache de relacionada Azure oferece aos clientes a capacidade de usar um cache seguro e dedicado relacionada, gerenciado pela Microsoft. Com esta oferta, você obtém aproveitar o conjunto de recursos avançados e ecossistema fornecidos pelo relacionada, confiáveis de hospedagem e monitoramento da Microsoft.

Diferentemente de caches tradicionais que lidam apenas com pares chave-valor, relacionada é popular para seus altamente desempenho tipos de dados. Relacionada também suporta executando operações atômicas nesses tipos, como acrescentar a uma cadeia de caracteres; incrementar o valor em um hash; enviando uma lista; computação conjunto interseção, união e diferença; ou obtendo o membro com maior classificação em um conjunto classificado. Outros recursos incluem suporte para transações, pub/sub, Lua script, chaves com limita a vida e definições de configuração para tornar relacionada se comportam mais como um cache tradicional.

Outro aspecto fundamental para o sucesso relacionada é o ecossistema íntegra, vibrante Abrir origem criado em torno dele. Isso é refletido no conjunto diverso de clientes relacionada disponíveis em vários idiomas. Isso permite que ela seja usada por praticamente qualquer carga de trabalho que você criaria dentro do Azure. 

Para obter mais informações sobre como começar com o Azure relacionada Cache, consulte [como usar Azure relacionada Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e [documentação do Azure relacionada Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Serviço de Cache gerenciado
[Gerenciadas Cache serviço está definido para ser descartados 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>Cache na função
[Cache na função está definido para ser descartados 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[configuração de "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
