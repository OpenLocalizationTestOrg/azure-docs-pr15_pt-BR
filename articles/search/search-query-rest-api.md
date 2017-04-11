<properties
    pageTitle="Índice de pesquisa do Azure usando a API REST da consulta | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Criar uma consulta de pesquisa na pesquisa Azure e use os parâmetros de pesquisa para filtrar e classificar resultados de pesquisa."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Índice de pesquisa do Azure usando a API REST da consulta
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTANTE](search-query-rest-api.md)

Este artigo mostrará como consultar um índice usando a [API de REST de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Antes de iniciar este passo a passo, você já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md) e [preenchida com dados](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Eu. Identificar api de consulta-chave do seu serviço de pesquisa do Azure
Um componente-chave de cada operação de pesquisa em relação a API de REST de pesquisa do Azure é a *chave de api* que foi gerado para o serviço que você provisionado. Ter uma chave válida estabelece confiança, numa base por solicitação, entre o aplicativo enviar a solicitação e o serviço que lida com ele.

1. Para localizar teclas api do serviço você deve entrar no [Portal do Azure](https://portal.azure.com/)
2. Vá para blade do seu serviço de pesquisa do Azure
3. Clique no ícone "Teclas"

Seu serviço terá *chaves de administração* e *consulta*.

 - Suas *chaves de administrador* principal e secundário conceder direitos completos a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há dois fatores essenciais para que você pode continuar a usar a chave secundária se você decidir gerar a chave primária e vice-versa.
 - Suas *chaves de consulta* conceder acesso somente leitura para índices e documentos e são normalmente distribuídos para aplicativos cliente que emitem solicitações de pesquisa.

Para fins de consultar um índice, você pode usar uma das suas chaves de consulta. Suas chaves de administrador também podem ser usados para consultas, mas você deve usar uma chave de consulta em seu código de aplicativo como isso melhor segue o [princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Formula sua consulta
Há duas maneiras de [Pesquisar seu índice usando a API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). Uma maneira é emitir uma solicitação HTTP POST onde os parâmetros de consulta serão definidos em um objeto JSON no corpo da solicitação. A outra maneira é emitir uma solicitação GET HTTP onde os parâmetros de consulta serão definidos dentro de URL da solicitação. Observe que POSTAGEM tem mais [relaxada limites](https://msdn.microsoft.com/library/azure/dn798927.aspx) no tamanho de parâmetros de consulta que GET. Por esse motivo, é recomendável usar POSTAGEM, a menos que tenha circunstâncias especiais onde usando GET seria mais conveniente.

Para postar e GET, precisará fornecer o *nome do serviço*, *nome do índice*e a *versão da API* de apropriada (a versão atual do API é `2015-02-28` no momento da publicação deste documento) na URL da solicitação. Para obter, a *cadeia de caracteres de consulta* no final da URL será onde você fornecer os parâmetros de consulta. Veja abaixo para o formato de URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

O formato do POST é o mesmo, mas com apenas api versão nos parâmetros de cadeia de caracteres de consulta.



#### <a name="example-queries"></a>Consultas de exemplo

Aqui estão algumas consultas de exemplo em um índice denominado "hotéis". Essas consultas são mostradas no formato GET e POST.

Pesquisar o índice inteiro para o termo 'orçamento' e retornar somente o `hotelName` campo:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplicar um filtro ao índice para localizar hotéis mais barato do que r $150 por noite e retornar o `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Pesquisar o índice inteiro, ordem por um campo específico (`lastRenovationDate`) em ordem decrescente, faça os dois primeiros resultados e mostrar apenas `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Envie sua solicitação HTTP
Agora que você tiver formulado sua consulta como parte da URL de solicitação HTTP (para obter) ou corpo (para POST), você pode definir seus cabeçalhos de solicitação e enviar sua consulta.

#### <a name="request-and-request-headers"></a>Solicitação e cabeçalhos de solicitação
Você deve definir dois cabeçalhos de solicitação para obter ou três para POSTAGEM:
1. O `api-key` cabeçalho deve ser definido como a chave de consulta que você encontrou na etapa eu acima. Observe que você também pode usar uma chave de administração como o `api-key` cabeçalho, mas é recomendável que você use uma chave de consulta porque exclusivamente concede acesso somente leitura para índices e documentos.
2. O `Accept` cabeçalho deve ser definido como `application/json`.
3. Para postar apenas, o `Content-Type` cabeçalho também deve ser definido como `application/json`.

Veja abaixo de uma solicitação GET HTTP pesquisar o índice de "hotéis" usar a API do REST de pesquisa do Azure, usando uma consulta simples que procura o termo "bichos":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Aqui está a mesma consulta de exemplo, desta vez usando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Uma solicitação de consulta bem-sucedida resultará em um código de Status `200 OK` e os resultados da pesquisa são retornados como JSON no corpo da resposta. Veja aqui os resultados para a aparência de consulta acima como, considerando que o índice "hotéis" é preenchido com os dados de exemplo na [Importação de dados na pesquisa do Azure usando a API REST](search-import-data-rest-api.md) (Observe que o JSON foi formatado para clareza).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para saber mais, visite a seção "Resposta" de [Documentos de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx). Para obter mais informações sobre outros códigos de status HTTP que poderia ser retornado no caso de falha, consulte [códigos de status HTTP (Azure pesquisa)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
