<properties
    pageTitle="Provedor de Cache do cache de saída do ASP.NET"
    description="Saiba como armazenar em cache de saída de página do ASP.NET usando Cache relacionada do Azure"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Provedor de Cache de saída do ASP.NET para Azure relacionada Cache

O provedor de Cache de saída relacionada é um mecanismo de armazenamento de fora de processo para dados de cache de saída. Esses dados são especificamente para respostas HTTP completas (página de cache de saída). O provedor conecta-se para o nova saída cache provedor ponto de extensão foi introduzido em ASP.NET 4.

Para usar o provedor de Cache de saída relacionada, primeiro configure seu cache e configure seu aplicativo ASP.NET usando o pacote de relacionada NuGet de provedor de Cache de saída. Este tópico fornece orientação sobre como configurar seu aplicativo para usar o provedor de Cache de saída relacionada. Para obter mais informações sobre como criar e configurar uma instância do Azure relacionada Cache, consulte [criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída de página do ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote relacionada NuGet de provedor de Cache de saída, clique com botão direito do projeto no **Solution Explorer** e escolha **Gerenciar pacotes do NuGet**.

![Cache relacionada Azure gerenciar pacotes do NuGet](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Digite **RedisOutputCacheProvider** na caixa de texto Pesquisar, selecione-os resultados e clique em **instalar**.

![Azure relacionada provedor de Cache de saída de Cache](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

O pacote relacionada NuGet de provedor de Cache de saída tem uma dependência no pacote StackExchange.Redis.StrongName. Se o pacote de StackExchange.Redis.StrongName não está presente em seu projeto, que ele será instalado. Observe que além do pacote de StackExchange.Redis.StrongName fortes também há a versão de não-fortes StackExchange.Redis. Se seu projeto está usando a versão de StackExchange.Redis não-fortes, que você deve desinstalá-lo, antes ou depois de instalar o pacote relacionada NuGet de provedor de Cache de saída, caso contrário, você vai obter conflitos de nomes em seu projeto. Para obter mais informações sobre esses pacotes, consulte [clientes de cache de configurar .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote NuGet downloads e adiciona as referências de assembly necessário e adiciona a seção a seguir no seu arquivo Web. config que contém a configuração necessária para seu aplicativo ASP.NET usar o provedor de Cache de saída relacionada.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

Na seção comentário fornece um exemplo dos atributos e configurações de amostra para cada atributo.

Configure os atributos com os valores da sua blade de cache no portal do Microsoft Azure e os outros valores conforme desejado. Para obter instruções sobre como acessar as propriedades de cache, consulte [Configurar relacionada configurações de cache](cache-configure.md#configure-redis-cache-settings).

-   **host** – especifique seu ponto de extremidade do cache.
-   **porta** – usar a porta não SSL ou seu SSL porta, dependendo das configurações de ssl.
-   **accessKey** – usar a chave primária ou secundária para o cache.
-   **ssl** – verdadeiro se você deseja proteger comunicações de cliente/cache com ssl; Caso contrário, false. Certifique-se de especificar a porta correta.
    -   A porta não SSL é desabilitada por padrão para novos caches. Especifique true para esta configuração para usar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte a seção de [Portas de acesso](cache-configure.md#access-ports) no tópico [Configurar um cache](cache-configure.md) .
-   **databaseId** – especificado qual banco de dados a ser usado para dados de saída do cache. Se não especificado, o valor padrão de 0 será usado.
-   **applicationName** – teclas são armazenados em relacionada como <AppName>_<SessionId>Data. Isso permite que vários aplicativos compartilhem a mesma chave. Este parâmetro é opcional e se você não fornecer um valor padrão será usado.
-   **connectionTimeoutInMilliseconds** – esta configuração permite substituir a configuração de connectTimeout no cliente StackExchange.Redis. Se não especificado, a configuração de connectTimeout padrão de 5000 é usada. Para obter mais informações, consulte [modelo de configuração de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – esta configuração permite substituir a configuração de syncTimeout no cliente StackExchange.Redis. Se não especificado, a configuração de syncTimeout padrão de 1000 é usada. Para obter mais informações, consulte [modelo de configuração de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache para cada página para a qual você deseja armazenar em cache a saída.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Neste exemplo os dados de página em cache permanecerão no cache por 60 segundos e uma versão diferente da página é armazenadas em cache para cada combinação de parâmetro. Para obter mais informações sobre a diretiva OutputCache, consulte [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Depois que essas etapas são executadas, seu aplicativo está configurado para usar o provedor de Cache de saída relacionada.

## <a name="next-steps"></a>Próximas etapas

Confira o [Provedor de estado de sessão do ASP.NET para Azure relacionada Cache](cache-aspnet-session-state-provider.md).
