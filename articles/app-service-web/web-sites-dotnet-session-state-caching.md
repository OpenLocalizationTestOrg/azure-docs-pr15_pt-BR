<properties 
    pageTitle="Estado de sessão com cache Azure relacionada no serviço de aplicativo do Azure" 
    description="Saiba como usar o serviço de Cache do Azure para o cache de estado de sessão ASP.NET de suporte." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Estado de sessão com cache Azure relacionada no serviço de aplicativo do Azure


Este tópico explica como usar o serviço de Cache relacionada do Azure para estado de sessão.

Se seu aplicativo web do ASP.NET usa estado de sessão, você precisará configurar um provedor de estado de sessão externos (o serviço de Cache relacionada ou um provedor de estado de sessão do SQL Server). Se você usa o estado da sessão e não use um provedor de serviços externo, você será limitado a uma instância de seu aplicativo web. O serviço de Cache relacionada é a rápida e simples habilitar.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Criar o Cache
Siga [estas instruções](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para criar o cache.

##<a id="configureproject"></a>Adicionar o pacote RedisSessionStateProvider NuGet ao seu aplicativo web
Instalar o NuGet `RedisSessionStateProvider` pacote.  Use o seguinte comando para instalar a partir do console do Gerenciador de pacote (**Ferramentas** > **NuGet Package Manager** > **Package Manager Console**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Para instalar a partir de **Ferramentas** > **NuGet Package Manager** > **Gerenciar pacotes preciosidade para solução**, procure `RedisSessionStateProvider`.

Para obter mais informações, consulte a [página de NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [Configurar o cliente de cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificar o arquivo Web. config
Além de fazer referências assembly para Cache, o pacote NuGet adiciona entradas de esboço no arquivo *Web. config* . 

1. Abra o *Web. config* e localize o elemento **sessionState** .

1. Insira os valores para `host`, `accessKey`, `port` (a porta SSL deve ser 6380) e defina `SSL` para `true`. Esses valores podem ser obtidos da lâmina do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para a sua instância de cache. Para obter mais informações, consulte [conectar ao cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Observe que a porta não SSL é desabilitada por padrão para novos caches. Para obter mais informações sobre como ativar a porta não SSL, consulte a seção de [Portas de acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) no tópico [Configurar um cache no Cache relacionada do Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx) . A marcação a seguir mostra as alterações no arquivo *Web. config* , especificamente as alterações para *porta*, *host*, accessKey*, e *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Usar o objeto da sessão no código
A etapa final é começar a usar o objeto da sessão em seu código ASP.NET. Você pode adicionar objetos ao estado de sessão, usando o método **Session.Add** . Este método usa pares chave-valor para armazenar itens no cache de estado da sessão.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

O código a seguir recupera esse valor de estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Você também pode usar o Cache relacionada aos objetos de cache em seu aplicativo web. Para obter mais informações, consulte [MVC filme aplicativo com Azure relacionada Cache em 15 minutos](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obter mais detalhes sobre como usar o estado da sessão ASP.NET, consulte [Visão geral de estado de sessão do ASP.NET][].

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Por [Rich Marques](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Visão geral de estado de sessão do ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
