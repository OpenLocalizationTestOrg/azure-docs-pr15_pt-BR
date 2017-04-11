<properties
    pageTitle="Provedor de estado de sessão do cache ASP.NET | Microsoft Azure"
    description="Saiba como armazenar o estado da sessão ASP.NET usando Cache relacionada do Azure"
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
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Provedor de estado de sessão ASP.NET para Azure relacionada Cache

Cache de relacionada Azure fornece um provedor de estado de sessão que você pode usar para armazenar seu estado de sessão em um cache em vez de na memória ou em um banco de dados do SQL Server. Para usar o provedor de estado de sessão em cache, primeiro configure seu cache e configure seu aplicativo ASP.NET para usar o pacote de relacionada NuGet de estado de sessão de Cache de cache.

Muitas vezes não é prático em um aplicativo de nuvem do mundo real para evitar armazenar alguma forma de estado para uma sessão de usuário, mas algumas abordagens afetam o desempenho e escalabilidade mais que outras pessoas. Se você tiver que armazenar o estado, a melhor solução é manter a quantidade de estado pequeno e armazená-lo em cookies. Se o que não seja viável, a melhor solução próxima é usar o estado da sessão ASP.NET com um provedor de cache distribuído, na memória. A solução pior de um ponto de vista de escalabilidade e desempenho é usar um banco de dados feito o provedor de estado de sessão. Este tópico fornece orientação sobre como usar o provedor de estado de sessão do ASP.NET para Azure relacionada Cache. Para obter informações sobre outras opções de estado de sessão, consulte [Opções de estado de sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote relacionada NuGet de estado de sessão de Cache, clique com botão direito do projeto no **Solution Explorer** e escolha **Gerenciar pacotes do NuGet**.

![Cache relacionada Azure gerenciar pacotes do NuGet](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Digite **RedisSessionStateProvider** na caixa de texto Pesquisar, selecione-os resultados e clique em **instalar**.

>[AZURE.IMPORTANT] Se você estiver usando o recurso de agrupamento da camada de premium, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Esta é uma alteração de ruptura; Para obter mais informações, consulte [v2.0.0 interrompendo alterar detalhes](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![Provedor de estado de sessão de Cache relacionada do Azure](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

O pacote relacionada NuGet de provedor de estado de sessão tem uma dependência no pacote StackExchange.Redis.StrongName. Se o pacote de StackExchange.Redis.StrongName não está presente em seu projeto, que ele será instalado. Observe que além do pacote de StackExchange.Redis.StrongName fortes também há a versão de não-fortes StackExchange.Redis. Se seu projeto está usando a versão de StackExchange.Redis não-fortes, que você deve desinstalá-lo, antes ou depois de instalar o pacote relacionada NuGet de provedor de estado de sessão, caso contrário, você vai obter conflitos de nomes em seu projeto. Para obter mais informações sobre esses pacotes, consulte [clientes de cache de configurar .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote NuGet downloads e adiciona o assembly necessário referências e adiciona que a seguir adiciona a seção a seguir no seu arquivo Web. config que contém a configuração necessária para seu aplicativo ASP.NET usar o provedor de estado de sessão de Cache relacionada.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

Na seção comentário fornece um exemplo dos atributos e configurações de amostra para cada atributo.

Configure os atributos com os valores da sua blade de cache no Portal do Microsoft Azure e os outros valores conforme desejado. Para obter instruções sobre como acessar as propriedades de cache, consulte [Configurar relacionada configurações de cache](cache-configure.md#configure-redis-cache-settings).

-   **host** – especifique seu ponto de extremidade do cache.
-   **porta** – usar a porta não SSL ou seu SSL porta, dependendo das configurações de ssl.
-   **accessKey** – usar a chave primária ou secundária para o cache.
-   **ssl** – verdadeiro se você deseja proteger comunicações de cliente/cache com ssl; Caso contrário, false. Certifique-se de especificar a porta correta.
    -   A porta não SSL é desabilitada por padrão para novos caches. Especifique true para esta configuração para usar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte a seção de [Portas de acesso](cache-configure.md#access-ports) no tópico [Configurar um cache](cache-configure.md) .
-   **throwOnError** – true se você quiser uma exceção a ser lançada em caso de uma falha ou falso se quiser que a operação falha silenciosamente. Você pode verificar uma falha verificando a propriedade de Microsoft.Web.Redis.RedisSessionStateProvider.LastException estática. O padrão é verdadeiro.
-   **retryTimeoutInMilliseconds** – operações que falham são repetidas durante esse intervalo, especificado em milissegundos. A primeira tentativa ocorre após 20 milissegundos e, em seguida, tentativas ocorrerem cada segundo até que o intervalo de retryTimeoutInMilliseconds expira. Imediatamente após esse intervalo, a operação será repetida uma vez final. Se a operação ainda falhar, a exceção é lançada de volta ao chamador, dependendo da configuração de throwOnError. O valor padrão é 0 que significa sem repetições.
-   **databaseId** – Especifica qual banco de dados para usar para cache saída de dados. Se não especificado, o valor padrão de 0 será usado.
-   **applicationName** – teclas são armazenados em relacionada como `{<Application Name>_<Session ID>}_Data`. Isso permite que vários aplicativos compartilhem a mesma chave. Este parâmetro é opcional e se você não fornecer um valor padrão será usado.
-   **connectionTimeoutInMilliseconds** – esta configuração permite substituir a configuração de connectTimeout no cliente StackExchange.Redis. Se não especificado, a configuração de connectTimeout padrão de 5000 é usada. Para obter mais informações, consulte [modelo de configuração de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – esta configuração permite substituir a configuração de syncTimeout no cliente StackExchange.Redis. Se não especificado, a configuração de syncTimeout padrão de 1000 é usada. Para obter mais informações, consulte [modelo de configuração de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Para obter mais informações sobre essas propriedades, consulte o anúncio de postagem de blog original em [Anunciando provedor de estado do ASP.NET sessão para relacionada](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar padrão InProc sessão estado provedor seção na Web. config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Depois que essas etapas são executadas, seu aplicativo está configurado para usar o provedor de estado de sessão de Cache relacionada. Quando você usa o estado de sessão em seu aplicativo, ele será armazenado em uma instância de Cache relacionada do Azure.

>[AZURE.NOTE] Observe que dados armazenados em cache devem ser serializáveis, ao contrário dos dados que podem ser armazenados em um padrão de sessão do ASP.NET na memória estado provedor. Quando o provedor de estado de sessão para relacionada é usado, certifique-se de que tipos de dados que estão sendo armazenados no estado de sessão são serializáveis.

## <a name="aspnet-session-state-options"></a>Opções de estado de sessão do ASP.NET

- Provedor de estado de sessão de memória - este provedor armazena o estado da sessão na memória. A vantagem de usar esse provedor é simples e rápida. No entanto, você não pode dimensionar seus aplicativos Web se você estiver usando no provedor de memória, desde que ele não seja distribuído.

- Estado de sessão do SQL Server - este provedor armazena o estado da sessão no Sql Server. Se você quiser manter o estado de sessão em um armazenamento persistente, você deve usar este provedor. Você pode dimensionar seu aplicativo Web, mas usando o Sql Server para sessão terá um impacto de desempenho em seu aplicativo Web.

- Distribuído na memória provedor estado de sessão como relacionada Cache sessão estado provedor - este oferece o melhor das duas soluções. O aplicativo Web pode ter um provedor de estado de sessão simples, rápida e flexível. No entanto, você precisa manter em consideração que este provedor armazena o estado da sessão em um Cache e seu aplicativo precisa levar em consideração todas as características associadas ao conversar com um distribuído no Cache de memória como falhas de rede temporárias. De práticas recomendadas sobre como usar o Cache, consulte [diretrizes de cache](../best-practices-caching.md) da Microsoft Patterns & práticas [orientação de implementação e Design de aplicativo de nuvem do Azure](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado da sessão e outras práticas recomendadas, consulte [Práticas recomendadas de desenvolvimento de Web (construção reais nuvem Apps com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Próximas etapas

Confira o [Provedor de Cache de saída do ASP.NET para Azure relacionada Cache](cache-aspnet-output-cache-provider.md).
