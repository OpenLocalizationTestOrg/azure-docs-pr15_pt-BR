<properties
    pageTitle="Índice de pesquisa do Azure usando o SDK do .NET da consulta | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Criar uma consulta de pesquisa na pesquisa Azure e use os parâmetros de pesquisa para filtrar e classificar resultados de pesquisa."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Índice de pesquisa do Azure usando o SDK do .NET da consulta
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTANTE](search-query-rest-api.md)

Este artigo mostrará como consultar um índice usando o [SDK do Azure pesquisa .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de iniciar este passo a passo, você já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md) e [preenchida com dados](search-what-is-data-import.md).

Observe que todo o código de exemplo neste artigo escrito em c#. Você pode encontrar a fonte completa código [no GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Eu. Identificar api de consulta-chave do seu serviço de pesquisa do Azure
Agora que você criou um índice de pesquisa do Azure, você está quase pronto para emitir consultas usando o SDK do .NET. Primeiro, você precisará obter uma das consulta api-chaves que foi gerado para o serviço de pesquisa que provisionado. O SDK do .NET enviará essa api-chave em cada solicitação ao seu serviço. Ter uma chave válida estabelece confiança, numa base por solicitação, entre o aplicativo enviar a solicitação e o serviço que lida com ele.

1. Para localizar teclas api do serviço você deve entrar no [Portal do Azure](https://portal.azure.com/)
2. Vá para blade do seu serviço de pesquisa do Azure
3. Clique no ícone "Teclas"

Seu serviço terá *chaves de administração* e *consulta*.

  - Suas *chaves de administrador* principal e secundário conceder direitos completos a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há dois fatores essenciais para que você pode continuar a usar a chave secundária se você decidir gerar a chave primária e vice-versa.
  - Suas *chaves de consulta* conceder acesso somente leitura para índices e documentos e são normalmente distribuídos para aplicativos cliente que emitem solicitações de pesquisa.

Para fins de consultar um índice, você pode usar uma das suas chaves de consulta. Suas chaves de administrador também podem ser usados para consultas, mas você deve usar uma chave de consulta em seu código de aplicativo como isso melhor segue o [princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Criar uma instância da classe SearchIndexClient
Para emitir consultas com o Azure pesquisa .NET SDK, você precisará criar uma instância do `SearchIndexClient` classe. Essa classe tem vários construtores. Aquele que deseja leva seu nome de serviço de pesquisa, o nome de índice e um `SearchCredentials` objeto como parâmetros. `SearchCredentials`quebra sua chave de api.

O código a seguir cria um novo `SearchIndexClient` para o índice de "hotéis" (criado em [criar um índice de pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md)) usando valores para o nome do serviço de pesquisa e a api-chave que estão armazenados no arquivo de configuração do aplicativo (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`tem um `Documents` propriedade. Essa propriedade fornece todos os métodos que você precisa consultar índices de pesquisa do Azure.

## <a name="iii-query-your-index"></a>III. O índice da consulta
Pesquisando com o SDK do .NET é tão simple quanto chamar o `Documents.Search` método no seu `SearchIndexClient`. Este método usa alguns parâmetros, incluindo o texto de pesquisa, junto com uma `SearchParameters` objeto que pode ser usado para refinar a consulta.

#### <a name="types-of-queries"></a>Tipos de consultas
Os dois principais [tipos de consulta](search-query-overview.md#types-of-queries) que você usará são `search` e `filter`. A `search` procura um ou mais termos em todos os campos _pesquisável_ no seu índice de consulta. A `filter` consulta avalia uma expressão booliana sobre todas _filtráveis_ campos em um índice.

Filtros e pesquisas são executados usando o `Documents.Search` método. Uma consulta de pesquisa pode ser passada a `searchText` parâmetro, enquanto uma expressão de filtro pode ser passada a `Filter` propriedade da `SearchParameters` classe. Para filtrar sem pesquisando, basta passar `"*"` para o `searchText` parâmetro. Para pesquisar sem filtragem, basta deixar o `Filter` propriedade desproteger, ou não passar em um `SearchParameters` instância em todos os.

#### <a name="example-queries"></a>Consultas de exemplo

O código de exemplo a seguir mostra algumas maneiras diferentes para consultar o índice de "hotéis" definido em [criar um índice de pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#DefineIndex). Observe que os documentos retornados com os resultados da pesquisa são instâncias do `Hotel` classe, que foi definida na [Importação de dados na pesquisa do Azure usando o SDK do .NET](search-import-data-dotnet.md#HotelClass). O código de exemplo utiliza um `WriteDocuments` método para os resultados da pesquisa no console de saída. Este método é descrito na próxima seção.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Lidar com os resultados da pesquisa
O `Documents.Search` método retorna um `DocumentSearchResult` objeto que contém os resultados da consulta. O exemplo na seção anterior usado um método chamado `WriteDocuments` para os resultados da pesquisa no console de saída:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Veja aqui o que a aparência dos resultados para as consultas na seção anterior, considerando que o índice "hotéis" é preenchido com os dados de exemplo na [Importação de dados na pesquisa do Azure usando o SDK do .NET](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

O código de exemplo acima usa o console para os resultados da pesquisa de saída. Da mesma forma, você precisará exibir resultados de pesquisa em seu próprio aplicativo. Consulte [Este exemplo no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) para obter um exemplo de como renderizar resultados de pesquisa em um aplicativo web baseado em ASP.NET MVC.
