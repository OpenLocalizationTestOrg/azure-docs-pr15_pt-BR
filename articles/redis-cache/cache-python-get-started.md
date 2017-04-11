<properties
    pageTitle="Como usar o Cache do Azure relacionada com Python | Microsoft Azure"
    description="Começar a usar o Cache de relacionada de Azure usando Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Como usar o Cache do Azure relacionada com Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tópico mostra como começar com o Azure relacionada Cache usando Python.


## <a name="prerequisites"></a>Pré-requisitos

Instale [Aj relacionada](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Criar um cache relacionada no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar as teclas de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Habilitar o ponto de extremidade não SSL

Alguns clientes relacionada não oferecer suporte a SSL e por padrão, a [porta não SSL está desabilitada para novas instâncias de Cache relacionada do Azure](cache-configure.md#access-ports). No momento da redação deste artigo, o cliente de [Aj relacionada](https://github.com/andymccurdy/redis-py) não dá suporte a SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar algo no cache e recuperá-la


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Substituir `<name>` com seu nome de cache e `key` com a chave de acesso.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
