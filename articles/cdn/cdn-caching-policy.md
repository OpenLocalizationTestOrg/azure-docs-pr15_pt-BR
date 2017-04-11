<properties
    pageTitle="CDN diretiva em extensão de serviços de mídia de cache"
    description="Este tópico fornece uma visão geral de uma CDN diretiva em extensão de serviços de mídia de cache."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>CDN diretiva em extensão de serviços de mídia de cache

Serviços de mídia do Azure fornece baseado em HTTP Streaming adaptativa e download progressivo. HTTP com base streaming é altamente escalável com benefícios do cache no proxy e camadas CDN, bem como o cache do cliente. Fluxo de extremidade de fornece recursos de streaming gerais e também a configuração de cabeçalhos de cache HTTP. Fluxo de extremidade de define HTTP Cache-Control: idade máxima e expira. Você pode obter mais informações para cabeçalhos de cache HTTP de [w3](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>Cabeçalhos de cache padrão

Por padrão, pontos de extremidade streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming sob demanda (fragmentos de mídia real/blocos) e manifest(playlist). Para transmitir ao vivo, pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para dados (fragmentos de mídia real/blocos) e cabeçalho para solicitações de manifest(playlist) de cache de 2 segundos. Quando o programa ao vivo vira sob demanda (arquivamento dinâmico) aplicam cabeçalhos de cache streaming sob demanda.

##<a name="azure-cdn-integration"></a>Integração do Azure CDN

Serviços de mídia do Azure fornece [CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de extremidade streaming. Cabeçalhos cache-control aplica-se da mesma forma como streaming pontos de extremidade para CDN habilitados streaming de pontos de extremidade. Azure usa CDN streaming ponto de extremidade configurado valores de cache para definir o tempo de vida dos objetos armazenados em cache internamente e também usa esse valor para definir a entrega cabeçalhos de cache. Quando usar CDN habilitado pontos de extremidade de streaming não é recomendável para definir valores de cache pequeno. Definir valores pequenas diminuir o desempenho e reduzir o benefício de CDN. Não é permitido definir cabeçalhos de cache menor que 600 segundos para CDN habilitado streaming de pontos de extremidade.

>[AZURE.IMPORTANT] Integração de serviços de mídia Azure com o Azure CDN é implementada em **Azure CDN da Verizon**.  Se desejar usar **Azure CDN do Akamai** para serviços de mídia do Azure, você deve [Configurar o ponto de extremidade manualmente](cdn-create-new-endpoint.md).  Para obter mais informações sobre os recursos de CDN Azure, consulte [Visão geral CDN](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configurando cabeçalhos de cache com serviços de mídia do Azure

Você pode usar o portal de gerenciamento do Azure ou APIs de serviços de mídia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar cabeçalhos de cache usando o gerenciamento de portal consulte a seção de [como gerenciar pontos de extremidade de Streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) Configurando o ponto de extremidade de Streaming.
2. API REST, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)de serviços de mídia do Azure.
3. .NET SDK, [StreamingEndpointCacheControl propriedades](http://go.microsoft.com/fwlink/?LinkId=615302)de serviços de mídia do Azure.

##<a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração de cache

1. Valor de cache de serviços de mídia configurados Azure substitui o valor padrão.
2. Se não houver nenhuma configuração manual, os valores padrão se aplica.
3. Por cache de 2 segundos padrão cabeçalhos aplica para dinâmicos manifest(playlist) streaming, independentemente da configuração de mídia do Azure ou armazenamento do Azure e substituindo desse valor não está disponível.
