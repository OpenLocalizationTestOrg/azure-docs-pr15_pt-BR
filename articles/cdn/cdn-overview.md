<properties
    pageTitle="Visão geral do Azure CDN | Microsoft Azure"
    description="Saiba o que é a rede de entrega de conteúdo (CDN) do Azure e como usá-lo para fornecer conteúdo de largura de banda alta armazenando blobs e conteúdo estático."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Visão geral da rede Azure fornecimento de conteúdo (CDN)

> [AZURE.NOTE] Este documento descreve o que é a rede de entrega de conteúdo (CDN) do Azure, como ele funciona e os recursos de cada produto CDN do Azure.  Se desejar ignorar essas informações e ir direto para um tutorial sobre como criar um ponto de extremidade CDN, consulte [Usando CDN do Azure](cdn-create-new-endpoint.md).  Se você quiser ver uma lista de locais de nó CDN atuais, consulte [Azure CDN POP locais](cdn-pop-locations.md).

A rede de entrega de conteúdo (CDN) do Azure armazena conteúdo da web estática em locais estratégico inseridas para fornecer transferência máxima para fornecer conteúdo aos usuários.  A CDN oferece aos desenvolvedores uma solução global para fornecer o conteúdo de banda larga armazenando o conteúdo em nós físicos em todo o mundo. 

Os benefícios de usar a CDN ativos de site da web do cache incluem:

- Melhor desempenho e experiência de usuário para usuários finais, especialmente quando usando aplicativos onde vários ida e volta é necessárias para carregar o conteúdo.
- Grande escala para melhor manipular instantânea alta carga, como no início de um evento de lançamento do produto.
- Distribuir solicitações de usuário e servir conteúdo de servidores de borda, menos tráfego é enviado para a origem.


## <a name="how-it-works"></a>Como ele funciona

![Visão geral CDN](./media/cdn-overview/cdn-overview.png)

1. Um usuário (Alice) solicita um arquivo (também chamado de um ativo) usando uma URL com um nome de domínio especial, tais como `<endpointname>.azureedge.net`.  DNS roteia a solicitação para o melhor desempenho local de ponto de presença (POP).  Normalmente, esse é o POP geograficamente mais próximo ao usuário.

2. Se os servidores de borda na POP não tiver o arquivo em seu cache, o servidor de borda solicita o arquivo de origem.  A origem pode ser um aplicativo Web do Azure, serviço de nuvem do Azure, conta de armazenamento do Azure ou qualquer servidor web acessíveis publicamente.

3. A origem retorna o arquivo para o servidor de borda, incluindo os cabeçalhos HTTP opcionais descrevendo Time-to-Live do arquivo (TTL).

4. O servidor de borda armazena o arquivo e retorna o arquivo ao solicitante original (Alice).  O arquivo permanece em cache no servidor de borda até que o TTL expira.  Se a origem não especificar um TTL, o TTL padrão é sete dias.

5. Usuários adicionais podem solicitar o mesmo arquivo usando essa mesma URL e também podem ser direcionados para esse mesmo POP.

6. Se o TTL para o arquivo não tiver expirado, o servidor de borda retorna o arquivo do cache.  Isso resulta em uma experiência de usuário mais rápida e mais responde.


## <a name="azure-cdn-features"></a>Recursos do Azure CDN

Há três produtos do Azure CDN: **Azure CDN padrão do Akamai**, **Azure CDN padrão da Verizon**e **Azure CDN Premium da Verizon**.  A tabela a seguir lista os recursos disponíveis com cada produto.

|       | Akamai padrão | Verizon padrão | Verizon Premium |
|-------|-----------------|------------------|-----------------|
| Fácil integração com o Azure serviços como o [armazenamento](cdn-create-a-storage-account-with-cdn.md), [Os serviços de nuvem](cdn-cloud-service-with-cdn.md), [Aplicativos Web](../app-service-web/cdn-websites-with-cdn.md)e [Serviços de mídia](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;**|
| Gerenciamento via [API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node](./cdn-app-dev-node.md)ou [PowerShell](./cdn-manage-powershell.md). | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Suporte HTTPS | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Balanceamento de carga | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Proteção de [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Pilha dupla IPv4/IPv6 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Suporte de nome de domínio personalizado](cdn-map-content-to-custom-domain.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Cache de cadeia de caracteres de consulta](cdn-query-string.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Filtragem de localização geográfica](cdn-restrict-access-by-country.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Limpar rápida](cdn-purge-endpoint.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Análise de núcleo](cdn-analyze-usage-patterns.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Suporte HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713;**  |  |  |
| [Relatórios HTTP avançados](cdn-advanced-http-reports.md) | | | **& #x 2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md) | | | **& #x 2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md) | | | **& #x 2713;** |
| [Mecanismo de personalizáveis baseados em regra, distribuição de conteúdo](cdn-rules-engine.md) | | | **& #x 2713;** |
| Configurações de cache/cabeçalho (usando o [mecanismo de regras](cdn-rules-engine.md))  | | | **& #x 2713;** |
| Redirecionamento de URL/reconfiguração (usando o [mecanismo de regras](cdn-rules-engine.md)) | | | **& #x 2713;** |
| Regras de dispositivo móvel (usando o [mecanismo de regras](cdn-rules-engine.md))  | | | **& #x 2713;** |

>[AZURE.TIP] Existe um recurso que você gostaria de ver no Azure CDN?  [Envie seus comentários](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Próximas etapas

Para começar a usar CDN, consulte [Usando CDN do Azure](./cdn-create-new-endpoint.md).

Se você for um cliente CDN existente, agora você pode gerenciar seus pontos de extremidade CDN por meio do [portal do Microsoft Azure](https://portal.azure.com) ou com o [PowerShell](cdn-manage-powershell.md).

Para ver a CDN em ação, confira o [vídeo de nossa sessão de compilação 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Saiba como automatizar Azure CDN com [.NET](./cdn-app-dev-net.md) ou [Node](./cdn-app-dev-node.md).

Para informações sobre preços, consulte [CDN preços](https://azure.microsoft.com/pricing/details/cdn/).
