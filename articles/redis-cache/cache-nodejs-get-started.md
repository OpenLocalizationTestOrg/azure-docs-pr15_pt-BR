<properties
    pageTitle="Como usar o Cache do Azure relacionada com Node | Microsoft Azure"
    description="Introdução ao Azure relacionada Cache usando node e node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Como usar o Cache do Azure relacionada com Node

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache Azure relacionada lhe dá acesso a um cache seguro e dedicado relacionada, gerenciado pela Microsoft. O cache é acessível de qualquer aplicativo do Microsoft Azure.

Este tópico mostra como começar com o Azure relacionada Cache usando Node. Outro exemplo do uso do Azure relacionada Cache com Node, consulte [criar um aplicativo de bate-papo Node com Socket.IO em um site do Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Pré-requisitos

Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node_redis](https://github.com/mranney/node_redis). Para obter exemplos do uso de outros clientes Node, consulte a documentação individual para os clientes de Node listado em [clientes Node relacionada](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Criar um cache relacionada no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar as teclas de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conectar-se para o cache com segurança usando SSL

A versões mais recente do [node_redis](https://github.com/mranney/node_redis) oferece suporte para conexão com o Azure relacionada Cache usando SSL. O exemplo a seguir mostra como se conectar ao Azure relacionada Cache usando o ponto de extremidade SSL de 6380. Substituir `<name>` com o nome do seu cache e `<key>` com seja a chave primária ou secundária como descrito na seção anterior [recuperar as teclas de acesso e de nome de host](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar algo no cache e recuperá-la

O exemplo a seguir mostra como conectar a uma instância do Azure relacionada Cache, armazenar e recuperar um item do cache. Para obter mais exemplos de como usar relacionada com o cliente de [node_redis](https://github.com/mranney/node_redis) , consulte [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Saída:

    OK
    value


## <a name="next-steps"></a>Próximas etapas

- [Habilitar o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do seu cache.
- Leia o oficial [relacionada documentação](http://redis.io/documentation).



