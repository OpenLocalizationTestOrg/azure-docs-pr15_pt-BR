<properties 
    pageTitle="Como solucionar problemas com o Azure relacionada Cache | Microsoft Azure" 
    description="Saiba como resolver problemas comuns com Azure relacionada Cache." 
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

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Como solucionar problemas de Cache relacionada do Azure

Este artigo fornece orientação para as seguintes categorias de problemas do Azure relacionada Cache de solução de problemas.

-   [Solução de problemas de lado cliente](#client-side-troubleshooting) - esta seção fornece diretrizes sobre como identificar e resolver problemas causados pelo aplicativo conectando ao Azure relacionada Cache.
-   [Solução de problemas de lado do servidor](#server-side-troubleshooting) - esta seção fornece diretrizes sobre como identificar e resolver problemas causados no lado do servidor do Azure relacionada Cache.
-   [Exceções de tempo limite de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - esta seção fornece informações sobre solução de problemas ao usar o cliente de StackExchange.Redis.


>[AZURE.NOTE] Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos relacionada e monitorar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na seção de [informações adicionais](#additional-information) .

## <a name="client-side-troubleshooting"></a>Solução de problemas de lado do cliente


Esta seção descreve problemas que ocorrem devido a uma condição no aplicativo do cliente.

-   [Pressão de memória no cliente](#memory-pressure-on-the-client)
-   [Intermitente de tráfego](#burst-of-traffic)
-   [Cliente alto uso da CPU](#high-client-cpu-usage)
-   [Largura de banda do lado cliente excedida](#client-side-bandwidth-exceeded)
-   [Tamanho grande solicitação/resposta](#large-requestresponse-size)
-   [O que aconteceu com meus dados em relacionada?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Pressão de memória no cliente

#### <a name="problem"></a>Problema

Pressão de memória no computador cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de dados que foi enviados pelo instância relacionada sem qualquer atraso. Quando atinge pressão de memória, o sistema normalmente é página dados de memória física para a memória virtual que está no disco. Esta *página com falha* faz com que o sistema lento significativamente.

#### <a name="measurement"></a>Medida 

1.  Monitorar o uso da memória no computador para certificar-se de que ela não exceda memória disponível. 
2.  Monitorar o `Page Faults/Sec` contador de desempenho. A maioria dos sistemas será tem algumas falhas de página par durante a operação normal, portanto observar picos nesse contador de desempenho de falhas de página que correspondem com tempos limite.

#### <a name="resolution"></a>Resolução

Atualizar o cliente para um cliente de maior tamanho de máquina virtual com mais memória ou aprofundar seus padrões de uso de memória para reduzir consuption de memória.


### <a name="burst-of-traffic"></a>Intermitente de tráfego

#### <a name="problem"></a>Problema

Explosões de tráfego combinado com ruim `ThreadPool` configurações podem resultar em atrasos no processamento de dados já enviadas pelo servidor relacionada, mas ainda não consumidas no lado do cliente.

#### <a name="measurement"></a>Medida 

Monitor como seu `ThreadPool` estatísticas mudam ao longo do tempo usando código [assim](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Você também pode examinar o `TimeoutException` mensagem do StackExchange.Redis. Aqui está um exemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Na mensagem acima, há vários problemas que são interessantes:

 1. Observe que na `IOCP` seção e o `WORKER` seção tiver um `Busy` valor que seja maior que o `Min` valor. Isso significa que seu `ThreadPool` configurações necessitam de ajuste.
 2. Você também pode ver `in: 64221`. Isso indica que 64211 bytes foram recebidos na camada de soquete de núcleo mas ainda não foram lidos pelo aplicativo (por exemplo, StackExchange.Redis). Isso geralmente significa que seu aplicativo não estiver lendo dados da rede rapidamente o servidor está enviando-lo para você.

#### <a name="resolution"></a>Resolução

Defina as [Configurações de pool](https://gist.github.com/JonCole/e65411214030f0d823cb) para certificar-se de que o pool de segmentos podem chegar rapidamente em cenários intermitente.


### <a name="high-client-cpu-usage"></a>Cliente alto uso da CPU

#### <a name="problem"></a>Problema

Alto uso de CPU no cliente é uma indicação de que o sistema não consegue acompanhar o trabalho que ele foi solicitado a executar. Isso significa que o cliente pode falhar ao processar uma resposta do relacionada no hábil apesar relacionada enviou a resposta muito rapidamente.

#### <a name="measurement"></a>Medida

Monitore o uso de CPU ampla do sistema por meio do Portal do Azure ou o contador de desempenho associados. Tenha cuidado para não monitorar CPU do *processo* como um único processo pode ter pouca utilização da CPU ao mesmo tempo que geral do sistema CPU pode ser alto. Assista picos de uso de CPU que correspondem com tempos limite. Como resultado de CPU alta, você também poderá ver alto `in: XXX` valores em `TimeoutException` conforme descrito na seção [intermitente de tráfego](#burst-of-traffic) de mensagens de erro.

>[AZURE.NOTE] StackExchange.Redis 1.1.603 e posterior inclui o `local-cpu` métrica em `TimeoutException` mensagens de erro. Verifique se você usando a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Há erros constantemente sendo corrigidos no código para torná-lo mais robusto para tempos limite para que ter a versão mais recente é importante.

#### <a name="resolution"></a>Resolução

Atualize para um tamanho maior de máquina virtual com mais capacidade de CPU ou investigar o que está causando picos de CPU. 



### <a name="client-side-bandwidth-exceeded"></a>Largura de banda do lado cliente excedida

#### <a name="problem"></a>Problema

Máquinas cliente tamanhos diferentes têm limitações em quanta largura de banda de rede tiverem disponíveis. Se o cliente exceder a largura de banda disponível, em seguida, dados não serão processados no lado do cliente tão rapidamente quanto o servidor está enviando-lo. Isso pode causar tempos limite.

#### <a name="measurement"></a>Medida

Monitore como seu uso de largura de banda alterar ao longo do tempo usando código [assim](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Observe que este código pode não ser executado com êxito em alguns ambientes com permissões restritas (como Azure sites).

#### <a name="resolution"></a>Resolução 

Aumentar o tamanho da máquina virtual do cliente ou reduzir o consumo de largura de banda de rede.


### <a name="large-requestresponse-size"></a>Tamanho grande solicitação/resposta

#### <a name="problem"></a>Problema

Solicitação/resposta grande pode causar tempos limite. Como exemplo, suponha que o valor de tempo limite configurado no cliente é 1 segundo. Seu aplicativo solicita duas chaves (por exemplo, 'A' e 'B') ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes oferecem suporte a "Pipelining" de solicitações, por exemplo, que ambas as solicitações 'A' e 'B' são enviadas na conexão ao servidor de um após o outro sem aguardar as respostas. O servidor enviará as respostas novamente na mesma ordem. Se resposta 'A' for grande suficiente ele pode consumir na maioria do limite para solicitações subsequentes. 

O exemplo a seguir demonstra esse cenário. Neste cenário, solicitação 'A' e 'B' são enviadas rapidamente, o servidor é iniciado enviando respostas 'A' e 'B' rapidamente, mas devido a tempos de transferência de dados, 'B' travam atrás a solicitação e expire mesmo que o servidor respondeu rapidamente.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medida

Isso é difícil de medir. Você tem, basicamente instrumentar seu código de cliente para controlar grandes solicitações e respostas. 

#### <a name="resolution"></a>Resolução

1.  Relacionada é otimizado para um grande número de valores pequenos, em vez de alguns valores grande. A solução preferida é dividir seus dados em valores menores relacionadas. Consulte o [o que é o intervalo de tamanho de valor ideal para relacionada? É 100KB muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) postar para obter detalhes em torno de por valores menores são recomendados.
2.  Aumente o tamanho da sua máquina virtual (para o cliente e servidor de Cache relacionada), para obter mais recursos de largura de banda, reduzir os tempos de transferência de dados para respostas maiores. Observe que obtendo mais largura de banda no apenas servidor ou apenas no cliente pode não ser suficiente. Meça o uso de largura de banda e compará-lo com os recursos do tamanho da máquina virtual que você possui no momento.
3.  Aumentar o número de `ConnectionMultiplexer` objetos uso e solicitações de alternada sobre diferentes conexões.


### <a name="what-happened-to-my-data-in-redis"></a>O que aconteceu com meus dados em relacionada?

#### <a name="problem"></a>Problema

Posso para determinados esperado a minha instância do Azure relacionada Cache de dados, mas ele não parece estar lá.

##### <a name="resolution"></a>Resolução

Consulte [o que aconteceu com meus dados em relacionada?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para possíveis causas e resoluções.


## <a name="server-side-troubleshooting"></a>Solução de problemas de lado do servidor

Esta seção descreve problemas que ocorrem devido a uma condição no servidor de cache.

-   [Pressão de memória no servidor](#memory-pressure-on-the-server)
-   [Alto uso da CPU / servidor carregar](#high-cpu-usage-server-load)
-   [Largura de banda de lado do servidor excedida](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pressão de memória no servidor

#### <a name="problem"></a>Problema

Pressão de memória no lado do servidor leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de solicitações. Quando atinge pressão de memória, o sistema normalmente é página dados de memória física para a memória virtual que está no disco. Esta *página com falha* faz com que o sistema lento significativamente. Há diversas causas possíveis essa pressão de memória: 

1.  Você tiver preenchido cache toda a capacidade com dados. 
2.  Relacionada está vendo fragmentação de memória alta - geralmente é causada armazenando objetos grandes (relacionada é otimizado para um pequeno objetos - Consulte a [o que é o intervalo de tamanho de valor ideal para relacionada? É 100KB muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) postar para obter detalhes). 

#### <a name="measurement"></a>Medida

Relacionada expõe duas métricas que podem ajudá-lo a identificar este problema. O primeiro é `used_memory` e a outra é `used_memory_rss`. [Essas métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) estão disponíveis no Portal do Azure ou por meio do comando de [Informações relacionada](http://redis.io/commands/info) .

#### <a name="resolution"></a>Resolução

Há várias alterações possíveis que você pode fazer para ajudar a manter o uso da memória íntegra:

1. [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e configurar tempos de expiração em suas chaves. Observe que isso pode não ser suficiente se você tiver fragmentação.
2. [Configurar um valor reservado maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) grande o suficiente para compensar fragmentação de memória.
3. Divida os objetos armazenados em cache grandes em menores objetos relacionados.
4. [Escala](cache-how-to-scale.md) para um tamanho maior de cache.
5. Se você estiver usando um [cache de premium com cluster relacionada ativado](cache-how-to-premium-clustering.md) , você pode [aumentar o número de fragmentos](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Alto uso da CPU / servidor carregar

#### <a name="problem"></a>Problema

Alto uso da CPU pode significam que o lado do cliente pode falhar processar uma resposta do relacionada no hábil apesar relacionada enviou a resposta muito rapidamente.

#### <a name="measurement"></a>Medida

Monitore o uso de CPU ampla do sistema por meio do Portal do Azure ou o contador de desempenho associados. Tenha cuidado para não monitorar CPU do *processo* como um único processo pode ter pouca utilização da CPU ao mesmo tempo que geral do sistema CPU pode ser alto. Assista picos de uso de CPU que correspondem com tempos limite.

#### <a name="resolution"></a>Resolução

[Escala](cache-how-to-scale.md) para um cache maior nível com mais capacidade de CPU ou investigar o que está causando picos de CPU. 

### <a name="server-side-bandwidth-exceeded"></a>Largura de banda de lado do servidor excedida

#### <a name="problem"></a>Problema

Instâncias de cache de tamanhos diferentes têm limitações em quanta largura de banda de rede tiverem disponíveis. Se o servidor exceder a largura de banda disponível, dados não serão enviados para o cliente como rapidamente. Isso pode causar tempos limite.

#### <a name="measurement"></a>Medida

Você pode monitorar o `Cache Read` métrica, que é a quantidade de dados lidas do cache em Megabytes por segundo (MB/s) durante o intervalo de relatório especificado. Esse valor corresponde a largura de banda de rede usado por esse cache. Se você quiser configurar alertas para limites de largura de banda de rede de lado do servidor, você pode criá-los usando `Cache Read` contador. Compare sua leituras com os valores [desta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observado para cache de vários níveis e tamanhos de preços.

#### <a name="resolution"></a>Resolução

Se você for consistentemente próximo a largura de banda máxima observada do tamanho da sua camada e cache preços, considere a possibilidade de [dimensionamento](cache-how-to-scale.md) para um nível de preços ou tamanho com maior largura de banda, usando os valores [nesta](cache-faq.md#cache-performance) tabela como um guia.


## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite de StackExchange.Redis

StackExchange.Redis usa uma configuração nomeado `synctimeout` para operações síncronas que tem um valor padrão de 1000 ms. Se uma chamada síncrona não concluída no tempo estipulado, o cliente StackExchange.Redis gera um erro de tempo limite parecido com o exemplo a seguir.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Esta mensagem de erro contém métricas que podem ajudar a indicar a causa e possível a resolução do problema. A tabela a seguir contém detalhes sobre as métricas de mensagem de erro.

| Métrica de mensagem de erro | Detalhes                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | Na última fatia tempo: 0 comandos foram emitidos                                                                                                                                                                                              |
| gerente        | O Gerenciador de soquete está executando `socket.select` que significa que está solicitando o sistema operacional para indicar um soquete que tem algo a fazer; basicamente: o leitor não está lendo ativamente da rede porque ela não acha que há algo a ver |
| fila      | Existem 73 total operações em andamento                                                                                                                                                                                                        |
| qu         | 6 das operações em andamento estão na fila não enviada e ainda não foram gravados para a rede de saída                                                                                                                                                           |
| QS         | 67 das operações de em andamento he foram enviadas para o servidor, mas uma resposta ainda não está disponível. A resposta pode ser `Not yet sent by the server` ou`sent by the server but not yet processed by the client.`                                                   |
| componentes enfileirados         | 0 das operações em andamento viu respostas, mas ainda não foram marcados como concluída devido a aguardando no loop conclusão                                                                                                                                      |
| wr         | Não há um gravador ativa (significando que não estão sendo ignoradas as solicitações não enviadas 6) bytes/activewriters                                                                                                                                                   |
| em         | Não há nenhuma leitores ativos e zero bytes estão disponíveis para ser lido na NIC bytes/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Etapas para investigar

1. Como uma prática recomendada Verifique se você está usando o seguinte padrão para se conectar ao usando o cliente de StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Para obter mais informações, consulte [conectar ao cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Verifique se o Cache de relacionada do Azure e o aplicativo cliente estão na mesma região no Azure. Por exemplo, você pode estar recebendo tempos limite quando o cache é Leste EUA, mas o cliente está em Oeste EUA e a solicitação não concluída dentro do `synctimeout` intervalo ou você pode estar recebendo tempos limite ao depurar da sua máquina de desenvolvimento local. 

    Altamente recomenda-se para tiver o cache e no cliente na mesma região Azure. Se você tiver um cenário que inclui chamadas de região cruzada, você deve definir o `synctimeout` intervalo para um valor maior que o intervalo de 1000 ms padrão, incluindo uma `synctimeout` propriedade na cadeia de conexão. O exemplo a seguir mostra um trecho de cadeia de conexão de cache StackExchange.Redis com um `synctimeout` de 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Verifique se você usando a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Há erros constantemente sendo corrigidos no código para torná-lo mais robusto para tempos limite para que ter a versão mais recente é importante.

4. Se houver solicitações obtendo vinculados por limitações de largura de banda no servidor ou cliente, levará mais tempo para que elas sejam concluir e, portanto, causar tempos limite. Para ver se seu tempo limite será devido a largura de banda de rede no servidor, consulte [largura de banda de lado do servidor excedida](#server-side-bandwidth-exceeded). Para ver se seu tempo limite será devido a largura de banda de rede de cliente, consulte [largura de banda do lado cliente excedida](#client-side-bandwidth-exceeded).

6. Você está começando a CPU é vinculados no servidor ou no cliente?
    -   Verificar se você está recebendo vinculados por CPU em seu cliente que pode causar a solicitação não ser processada dentro do `synctimeout` intervalo, fazendo um tempo limite. Movendo para um tamanho maior de cliente ou distribuir a carga pode ajudar a controlar isso. 
    -   Verificar se você está obtendo CPU vinculado no servidor monitorando os `CPU` [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Solicitações de enquanto relacionada está vinculado à CPU podem causar essas solicitações ao tempo limite. Para resolver isso, você pode distribuir a carga entre vários fragmentos em cache premium ou atualizar para um tamanho maior ou nível de preços. Para obter mais informações, consulte [Servidor lado largura de banda excedida](#server-side-bandwidth-exceeded).

7. Existem comandos levando muito tempo para processar no servidor? Comandos que estão demorando muito tempo para processar no servidor relacionada de execução prolongadas pode causar tempos limite. Alguns exemplos de comandos de execução demorada são `mget` com grandes números de teclas, `keys *` ou mal escritos lua scripts. Você pode se conectar à sua instância de Cache relacionada do Azure usando o cliente de cli relacionada ou usar o [Console relacionada](cache-configure.md#redis-console) e execute o comando [SlowLog](http://redis.io/commands/slowlog) para ver se há solicitações demorando mais do que o esperado. Servidor relacionada e StackExchange.Redis são otimizados para muitas solicitações de pequenas em vez de menos solicitações grandes. Dividir seus dados em partes menores pode melhorar as coisas aqui. 

    Para obter informações sobre a conexão com o ponto de extremidade do Azure relacionada Cache SSL usando cli relacionada e stunnel, consulte a postagem do blog [Anunciando provedor de estado de sessão ASP.NET a versão de visualização relacionada](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Para obter mais informações, consulte [SlowLog](http://redis.io/commands/slowlog).

8. Alta relacionada a carga do servidor pode causar tempos limite. Você pode monitorar a carga do servidor monitorando os `Redis Server Load` [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga de servidor de 100 (valor máximo) significa que o servidor relacionada foi ocupado, sem tempo ocioso, solicitações de processamento. Para ver se determinadas solicitações são ocupar todos os recursos de servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, consulte [alta da CPU / servidor carregar](#high-cpu-usage-server-load).

9. Foi lá qualquer outro evento no lado do cliente que pode ter causado um blip de rede? Verificar no cliente (web, função de trabalho ou uma VM Iaas) se havia em um evento como o número de instâncias do cliente de dimensionamento para cima ou para baixo, ou implantar uma nova versão do cliente ou dimensionar automaticamente está habilitado? Em nossos testes que descobrimos que escala automática ou escala para cima/baixo pode causar conectividade de rede de saída pode ser perdida por vários segundos. Código de StackExchange.Redis é flexível tais eventos e irá se reconectar. Durante esse período de conexão novamente todas as solicitações na fila podem tempo limite.

10. Foi lá uma solicitação de grande anterior várias solicitações de pequenas no cache relacionada que expirou? O parâmetro `qs` no erro mensagem informa quantas solicitações foram enviadas do cliente para o servidor, mas ainda não processada uma resposta. Este valor pode manter aumentando porque StackExchange.Redis usa uma conexão de TCP única e só podem ler uma resposta uma vez. Embora a primeira operação expirou, ele não parar os dados que está sendo enviados de/para o servidor e outras solicitações são bloqueadas até que isso estiver concluído, causando desvantagens de tempo. Uma solução é minimizar a chance de tempos limite, garantindo que o cache é grande o suficiente para sua carga de trabalho e dividir valores grandes em partes menores. Outra solução possível é usar um pool de `ConnectionMultiplexer` objetos no seu cliente e, em seguida, escolha menos carregado `ConnectionMultiplexer` ao enviar uma nova solicitação. Isso deve impedir que um único limite fazendo com que outras solicitações também tempo limite.

11. Se você estiver usando `RedisSessionStateprovider`, verifique se você definiu o tempo limite repetir corretamente. `retrytimeoutInMilliseconds`deve ser maior que `operationTimeoutinMilliseonds`, caso contrário ocorrerá sem repetições. No exemplo a seguir `retrytimeoutInMilliseconds` está definido como 3000. Para obter mais informações, consulte o [Provedor de estado de sessão do ASP.NET para Azure relacionada Cache](cache-aspnet-session-state-provider.md) e [como usar os parâmetros de configuração de provedor de estado de sessão e provedor de Cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Verificar o uso da memória no servidor do Azure relacionada Cache [Monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se for uma política de remoção no lugar, relacionada inicia removendo teclas quando `Used_Memory` atinge o tamanho do cache. Ideal seria `Used Memory RSS` deve ser somente ligeiramente acima de `Used memory`. Uma grande diferença significa que há fragmentação da memória (internos ou externos. Quando `Used Memory RSS` é menor que `Used Memory`, significa que a parte da memória cache foram trocada pelo sistema operacional. Se isso acontecer, você pode esperar alguns latências significativas. Porque relacionada não tem controle sobre como suas alocações são mapeadas para páginas de memória, altas `Used Memory RSS` é geralmente o resultado de um aumento no uso de memória. Quando relacionada libera memória, a memória é dado de volta para o alocador e o alocador pode ou não pode dar a memória volta ao sistema. Pode haver uma discrepâncias entre o `Used Memory` consumo de valor e memória conforme relatado pelo sistema operacional. Pode ser devido a fato de memória foi usada e foi lançada por relacionada, mas não fornecido back do sistema. Para ajudar a minimizar problemas de memória, você pode executar as seguintes etapas.
    -   Atualize o cache para um tamanho maior, para que você não estiver executando ao limitações de memória no sistema.
    -   Definir tempos de expiração das chaves para que os valores mais antigos forem excluídos proativamente.
    -   Monitorar o o `used_memory_rss` métrica em cache. Quando este valor aproxima o tamanho de seu cache, você provavelmente serão iniciar vendo problemas de desempenho. Distribua os dados em vários fragmentos se você estiver usando um cache premium ou atualizar para um tamanho maior de cache.

    Para obter mais informações, consulte [Pressão de memória no servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais

-   [Qual oferta de Cache relacionada e tamanho devo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Como padrão de referência e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Como é possível executar comandos relacionada?](cache-faq.md#how-can-i-run-redis-commands)
-   [Como monitorar Cache relacionada do Azure](cache-how-to-monitor.md)