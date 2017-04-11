<properties 
    pageTitle="Exemplos de Cache relacionada Azure | Microsoft Azure" 
    description="Saiba como usar o Cache relacionada do Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Exemplos de Cache relacionada Azure 

Este tópico fornece uma lista de amostras de Cache relacionada do Azure, que aborda os cenários como se conectar a um cache, ler e gravar dados para e de um cache e usando os provedores de Cache do ASP.NET relacionada. Alguns dos exemplos são projetos para download e alguns fornecem orientações passo a passo e incluem trechos de código, mas não vincular a um projeto para download.

## <a name="hello-world-samples"></a>Exemplos de mundo de saudação

Os exemplos nesta seção mostram as Noções básicas para se conectar a uma instância de Cache relacionada do Azure e ler e gravar dados para o cache usando uma variedade de idiomas e relacionada clientes.

O exemplo de [Olá](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como executar várias operações de cache usando o cliente de .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

Este exemplo mostra como:

-   Usar várias opções de conexão
-   Ler e gravar objetos para e do cache usando operações síncronas e assíncronas
-   Usar comandos de relacionada MGET/MSET para retornar valores das chaves especificadas
-   Executar operações de transação relacionada
-   Trabalhar com listas relacionada e conjuntos de classificado
-   Armazenar objetos .NET usando JsonConvert serializadores
-   Usar conjuntos relacionada para implementar a marcação
-   Trabalhar com Cluster relacionada

Para obter mais informações, consulte a documentação de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) no github e obter mais cenários de uso, consulte os testes de unidade [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) .

[Como usar o Cache do Azure relacionada com Python](cache-python-get-started.md) mostra como começar com o Azure relacionada Cache usando Python e o cliente de [Aj relacionada](https://github.com/andymccurdy/redis-py) .

[Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra uma maneira de serializar objetos .NET para poder escrever eles e lê-los de uma instância de Cache relacionada do Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Usar o Cache relacionada como uma escala de check-out Backplane para SignalR do ASP.NET

O exemplo de [Usar Cache relacionada como uma escala de check-out Backplane para SignalR ASP.NET](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) demonstra como você pode usar o Azure relacionada Cache como um backplane SignalR. Para obter mais informações sobre backplane, consulte [SignalR Scaleout com relacionada](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Exemplo de consulta de cliente do Cache de relacionada

Este exemplo demonstra compara desempenho entre o acesso a dados de um cache e acessar dados do armazenamento de persistência. Este exemplo tem dois projetos.

-   [Demonstração de como relacionada Cache pode melhorar o desempenho por cache de dados](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Propagar o banco de dados e o Cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado de sessão ASP.NET e o cache de saída

O exemplo de [Usar Azure relacionada Cache para armazenar ASP.NET SessionState e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como usar Azure relacionada Cache para armazenar a sessão do ASP.NET e o Cache de saída usando os provedores SessionState e OutputCache para relacionada.

## <a name="manage-azure-redis-cache-with-maml"></a>Gerenciar o Cache relacionada Azure com MAML

A [Gerenciar o Cache do Azure relacionada usando bibliotecas de gerenciamento do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como você pode usar bibliotecas de gerenciamento do Azure para gerenciar - (criar / atualizar / excluir) o Cache. 

## <a name="custom-monitoring-sample"></a>Personalizar monitoramento de amostra

O exemplo de [dados de monitoramento de Cache acesso relacionada](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como você pode acessar dados de monitoramento para o Cache do Azure relacionada fora do Portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clonar Twitter estilo escrito usando PHP e relacionada

O exemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) é relacionada Olá mundo. É um mínimo clonar de rede social do Twitter estilo escrito usando relacionada e PHP usando o cliente de [Predis](https://github.com/nrk/predis) . O código-fonte destina-se a ser muito simples e ao mesmo tempo para mostrar diferentes relacionada estruturas de dados.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda

A amostra de [monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite que você monitore a largura de banda usada no cliente. Para medir a largura de banda, executar o exemplo no computador cliente cache, fazer chamadas para o cache e observar a largura de banda relatada pela amostra de monitor de largura de banda.
