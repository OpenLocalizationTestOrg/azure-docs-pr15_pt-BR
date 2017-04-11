<properties
 pageTitle="Como gerenciar a expiração de conteúdo de serviços de aplicativos/nuvem do Azure Web, ASP.NET e IIS no Azure CDN | Microsoft Azure"
 description="Descreve como gerenciar a expiração de conteúdo de serviço de nuvem no Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Como gerenciar a expiração de conteúdo de serviços de aplicativos/nuvem do Azure Web, ASP.NET ou IIS no Azure CDN

> [AZURE.SELECTOR]
- [Serviços de nuvem/aplicativos do Azure Web, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure serviço de armazenamento de blob](cdn-manage-expiration-of-blob-content.md)

Arquivos em qualquer servidor da web de origem publicamente acessíveis podem ser armazenados em cache Azure CDN até ociosa seu time to live (TTL).  O TTL é determinado pelo [cabeçalho *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do servidor de origem.  Este artigo descreve como configurar `Cache-Control` cabeçalhos para Azure Web Apps, serviços de nuvem do Azure, aplicativos ASP.NET e sites de serviços de informações da Internet, todas elas são configuradas da mesma forma.

>[AZURE.TIP] Você pode optar por não definir nenhum TTL em um arquivo.  Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
>
>Para obter mais informações sobre o funcionamento do Azure CDN para acelerar acesso a arquivos e outros recursos, consulte [Visão geral do Azure CDN](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Definir cabeçalhos Cache-Control na configuração

Para conteúdo estático, como imagens e folhas de estilos, você pode controlar a frequência de atualização modificando os arquivos **applicationHost** . **config** ou para o aplicativo web.  O elemento **system.webServer\staticContent\clientCache** no arquivo de configuração definirá o `Cache-Control` cabeçalho para o seu conteúdo. Para **Web. config**, as configurações afetarão tudo na pasta e todas as subpastas, a menos que substituído no nível de subpasta.  Por exemplo, você pode definir um padrão para vida na raiz para que todo o conteúdo estático armazenados em cache para 3 dias, mas tem uma subpasta que tem o conteúdo mais variável com uma configuração de cache de 6 horas.  Para **applicationHost**, todos os aplicativos do site serão afetados, mas podem ser substituídos em arquivos **Web. config** nos aplicativos.

O XML mostra e exemplo de configuração **clientCache** para especificar uma idade máxima de 3 dias a seguir:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** adiciona um `Cache-Control: max-age=<nnn>` cabeçalho à resposta baseado no valor especificado no atributo **CacheControlMaxAge** . O formato do período de tempo é para o atributo **cacheControlMaxAge** <days>. <hours>:<min>:<sec>. Para obter mais informações sobre o nó **clientCache** , consulte [Cache cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Definir cabeçalhos Cache-Control no código

Para aplicativos ASP.NET, você pode definir a CDN comportamento do cache programaticamente definindo a propriedade **HttpResponse.Cache** . Para obter mais informações sobre a propriedade de **HttpResponse.Cache** , consulte [HttpResponse.Cache propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se você quiser programaticamente cache conteúdo do aplicativo no ASP.NET, certifique-se de que o conteúdo está marcado como armazenáveis em cache definindo HttpCacheability como *público*. Além disso, certifique-se de que uma validação de cache está definida. A validação de cache pode ser uma última modificação carimbo de hora definir chamando SetLastModified ou um valor de etag definir chamando SetETag. Opcionalmente, você também pode especificar um tempo de expiração de cache chamando SetExpires ou você pode depender os heurísticos de cache padrão descritos anteriormente neste documento.  

Por exemplo, cache do conteúdo por uma hora, adicione o seguinte:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Próximas etapas

- [Leia os detalhes sobre o elemento de **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Leia a documentação para a propriedade **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Ler a documentação para a **Classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
