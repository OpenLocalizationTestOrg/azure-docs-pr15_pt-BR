<properties
   pageTitle="Como usar o Cache do Azure relacionada com Java | Microsoft Azure"
    description="Começar a usar o Cache de relacionada de Azure usando Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Como usar o Cache do Azure relacionada com Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure relacionada Cache permite que você acesse dedicado relacionada cache, gerenciada pela Microsoft. O cache é acessível de qualquer aplicativo do Microsoft Azure.

Este tópico mostra como começar com o Azure relacionada Cache usando Java.

## <a name="prerequisites"></a>Pré-requisitos

[Jedis](https://github.com/xetorthio/jedis) - cliente de Java para relacionada

Este tutorial usa Jedis, mas você pode usar qualquer cliente Java listado no [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Criar um cache relacionada no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar as teclas de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Habilitar o ponto de extremidade não SSL

Alguns clientes relacionada não oferecer suporte a SSL e por padrão, a [porta não SSL está desabilitada para novas instâncias de Cache relacionada do Azure](cache-configure.md#access-ports). No momento da redação deste artigo, o cliente de [Jedis](https://github.com/xetorthio/jedis) não dá suporte a SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar algo no cache e recuperá-la

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Próximas etapas

- [Habilitar o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do seu cache.
- Leia o oficial [relacionada documentação](http://redis.io/documentation).

