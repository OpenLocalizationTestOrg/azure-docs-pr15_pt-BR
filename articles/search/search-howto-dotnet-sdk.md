<properties
   pageTitle="Como usar a pesquisa de um aplicativo .NET Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
   description="Como usar a pesquisa do Azure de um aplicativo .NET"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/06/2016"
   ms.author="brjohnst"/>

# <a name="how-to-use-azure-search-from-a-net-application"></a>Como usar a pesquisa do Azure de um aplicativo .NET

Este artigo é um passo a passo para ajudá-lo em funcionamento com o [SDK do Azure pesquisa .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx). Você pode usar o SDK do .NET para implementar uma experiência de pesquisa avançada no seu aplicativo usando a pesquisa do Azure.

## <a name="whats-in-the-azure-search-sdk"></a>O que há no Azure pesquisar SDK

O SDK consiste em uma biblioteca de cliente, `Microsoft.Azure.Search`. Ele permite que você gerencie seus índices, fontes de dados e indexadores, bem como carregar e gerenciar documentos e executar consultas, tudo sem ter de lidar com os detalhes de HTTP e JSON.

A biblioteca de cliente define classes como `Index`, `Field`, e `Document`, bem como as operações, como `Indexes.Create` e `Documents.Search` no `SearchServiceClient` e `SearchIndexClient` classes. Essas classes são organizadas nos namespaces a seguir:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

A versão atual do Azure pesquisa .NET SDK agora está disponível em geral. Se você quiser fornecer comentários para que possamos incorporar na próxima versão, visite nossa [página de comentários](https://feedback.azure.com/forums/263029-azure-search/).

O SDK do .NET dá suporte à versão `2015-02-28` da API do REST de pesquisa do Azure, documentadas no [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Esta versão agora inclui suporte para a sintaxe de consulta Lucene e analisadores de idioma do Microsoft. Novos recursos que *não* fazem parte nesta versão, como o suporte para o `moreLikeThis` parâmetro de pesquisa, são na [visualização](search-api-2015-02-28-preview.md) e ainda não está disponível no SDK. Você pode verificar novamente no [controle de versão de serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn864560.aspx) para atualizações de status sobre o recurso.

Outros recursos que não têm suportados neste SDK incluem:

  - [Operações de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx). Operações de gerenciamento incluem provisionamento de serviços de pesquisa do Azure e gerenciamento de chaves de API. Esses serão suportados no SDK de gerenciamento de .NET separada Azure pesquisa no futuro.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualização para a versão mais recente do SDK

Se você já estiver usando uma versão mais antiga do Azure pesquisa .NET SDK e você gostaria de atualizar para a nova versão estará disponível, [Este artigo](search-dotnet-sdk-migration.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK

1. Visual Studio 2013 ou Visual Studio 2015.

2. Seu próprio serviço de pesquisa do Azure. Para poder usar o SDK, será necessário o nome do seu serviço e uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você pelas seguintes etapas.

3. Baixe o SDK do Azure pesquisa .NET [pacote NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) usando "Gerenciar pacotes do NuGet" no Visual Studio. Basta procurar o nome do pacote `Microsoft.Azure.Search` em NuGet.org.

O SDK do Azure pesquisa .NET suporta aplicativos direcionamento do .NET Framework 4,5, bem como aplicativos da Windows Store direcionamento Windows 8.1 e Windows Phone 8.1. Não há suporte para o Silverlight.

## <a name="core-scenarios"></a>Cenários principais

Há várias coisas que você precisará fazer em seu aplicativo de pesquisa. Neste tutorial, vamos abordar esses cenários principais:

- Criar um índice
- Preenchendo o índice com documentos
- Procurando documentos usando filtros e pesquisa de texto completo

O código de exemplo que segue ilustra cada um deles. Fique à vontade para usar os trechos de código em seu próprio aplicativo.

### <a name="overview"></a>Visão geral

Vamos explorar o aplicativo de exemplo cria um novo índice chamado "hotéis", preenche com alguns documentos, em seguida, executa algumas consultas de pesquisa. Aqui está o programa principal, mostrando o fluxo geral:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Examinaremos esse passo a passo. Primeiro, precisamos criar um novo `SearchServiceClient`. Esse objeto permite que você gerencie índices. Para construir um, você precisa fornecer o nome do serviço de pesquisa do Azure bem como uma chave de API de administração.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] Se você fornecer uma tecla incorreta (por exemplo, uma consulta chave onde uma chave de administrador era necessária), o `SearchServiceClient` irá acionar uma `CloudException` com o erro mensagem "Proibido" na primeira vez que você chamar um método de operação, como `Indexes.Create`. Se isso acontecer para você, confira nossa chave API.

As próximas linhas chamam métodos para criar um índice denominado "hotéis", excluí-lo primeiro se ele já existir. Vamos percorrer esses métodos um pouco mais tarde.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Em seguida, o índice precisa ser preenchido. Para fazer isso, será necessário um `SearchIndexClient`. Há duas maneiras de obter um: Criando-la, ou chamando `Indexes.GetClient` na `SearchServiceClient`. Usamos o último para sua conveniência.

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] Em um aplicativo de pesquisa típica, gerenciamento de índice e população é realizado por um componente separado de consultas de pesquisa. `Indexes.GetClient`é conveniente para preencher um índice porque poupa o problema de fornecer outro `SearchCredentials`. Ela faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte do seu aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você pode passar em uma chave de consulta em vez de uma chave de administrador. Isso é consistente com o princípio do privilégio mínimo e ajuda a tornar seu aplicativo mais seguro. Você pode saber mais sobre chaves de administração e consulta [aqui](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Agora que temos um `SearchIndexClient`, podemos pode preencher o índice. Isso é feito por outro método, vamos percorrer mais tarde.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Por fim, podemos executar algumas consultas de pesquisa e exibir os resultados, novamente usando o `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Se você executar esse aplicativo com um nome de serviço válido e chave API, a saída deve ter esta aparência:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

O código-fonte completo do aplicativo é fornecido no final deste artigo.

Em seguida, podemos terão uma visualização detalhada em cada um dos métodos chamados `Main`.

### <a name="creating-an-index"></a>Criar um índice

Depois de criar um `SearchServiceClient`, a próxima coisa `Main` faz é excluir o índice de "hotéis" se ele já existir. Que é feito com o seguinte método:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Este método usa a determinada `SearchServiceClient` para verificar se o índice existe e em caso afirmativo, excluí-la.

> [AZURE.NOTE] O código de exemplo neste artigo usa os métodos síncronos do Azure pesquisa .NET SDK para simplificar. Recomendamos que você usa os métodos assíncronos em seus próprios aplicativos para mantê-los scalable e ágil. Por exemplo, no método acima você poderia usar `ExistsAsync` e `DeleteAsync` , em vez de `Exists` e `Delete`.

Próximo, `Main` cria um novo índice de "hotéis" chamando este método:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Este método cria um novo `Index` objeto com uma lista de `Field` objetos que define o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. Além dos campos, você também pode adicionar perfis de pontuação, suggesters ou CORS opções para o índice (esses são omitidos de amostra para abreviar). Você pode encontrar mais informações sobre o objeto de índice e suas partes constituintes na referência do SDK no [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), bem como na [referência de API de REST de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### <a name="populating-the-index"></a>Preenchendo o índice

A próxima etapa no `Main` é preencher o índice recém-criado. Isso é feito o seguinte método:

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Este método tem quatro partes. O primeiro cria uma matriz de `Hotel` objetos que servirão como nossos dados de entrada para carregar o índice. Esses dados serão codificada para simplicidade. Em seu próprio aplicativo, seus dados provavelmente se torne uma fonte de dados externos como um banco de dados do SQL.

A segunda parte cria um `IndexBatch` que contêm os documentos. Você especifique a operação que você deseja aplicar ao lote no momento criá-lo, nesse caso, chamando `IndexBatch.Upload`. O lote, em seguida, é carregado para o índice de pesquisa do Azure pela `Documents.Index` método.

> [AZURE.NOTE] Neste exemplo, vamos apenas estão carregando documentos. Se quiser mesclar as alterações em documentos existentes ou excluir documentos, você poderia criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete` em vez disso. Você também pode misturar operações diferentes em um único lote chamando `IndexBatch.New`, que usa uma coleção de `IndexAction` objetos, cada um deles informa que a pesquisa do Azure para executar uma operação específica em um documento. Você pode criar cada `IndexAction` com sua própria operação chamando o método correspondente como `IndexAction.Merge`, `IndexAction.Upload`e assim por diante.

A terceira parte desse método é um bloco de variável que manipula um caso de erro importantes para indexação. Se seu serviço de pesquisa do Azure falhar indexar alguns dos documentos em lote, um `IndexBatchException` é lançada por `Documents.Index`. Isso pode acontecer se você estiver indexando documentos enquanto seu serviço está sob carga pesada. **É altamente recomendável explicitamente lidar com esse caso em seu código.** Você pode atrasar e tente novamente a indexação os documentos que falhou, ou você pode fazer logon e continuar como a amostra faz, ou você pode fazer algo diferente dependendo de requisitos de consistência de dados do seu aplicativo.

Finalmente, os método os atrasos por dois segundos. Indexação acontece assíncrona no seu serviço de pesquisa do Azure, para que o aplicativo de exemplo precisa esperar alguns instantes para garantir que os documentos estão disponíveis para pesquisa. Atrasos assim apenas normalmente são necessários em demonstrações, testes e aplicativos de exemplo.

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK do .NET trata documentos

Você pode estar se perguntando como o SDK do Azure pesquisa .NET é capaz de carregar instâncias de uma classe definida pelo usuário como `Hotel` ao índice. Para ajudar a responder essa pergunta, vamos examinar o `Hotel` classe:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição de índice, mas com uma diferença fundamental: O nome de cada campo começa com uma letra minúscula ("camelCase"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiuscula ("ocorrência do Pascal"). Este é um cenário comum em aplicativos .NET que executar associação de dados onde o esquema de destino está fora do controle do desenvolvedor do aplicativo. Em vez de ter violar .NET diretrizes de nomenclatura fazendo nomes de propriedade camel-caso, você pode informar o SDK para mapear os nomes de propriedade para camel-caso automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo.

> [AZURE.NOTE] O SDK do Azure pesquisa .NET usa a biblioteca de [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar seus objetos do modelo personalizado para e do JSON. Você pode personalizar essa serialização se necessário. Para obter mais detalhes, consulte [Serialização personalizada com JSON.NET](#JsonDotNet).
 
O segundo importante sobre a `Hotel` classe são os tipos de dados das propriedades públicos. Mapeiam os tipos de .NET dessas propriedades para seus tipos de campo equivalente na definição de índice. Por exemplo, o `Category` string mapas de propriedade para o `category` campo, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhante entre `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset`, etc. As regras específicas para o mapeamento de tipo documentadas com a `Documents.Get` método no [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Essa capacidade de usar suas próprias classes como documentos funciona em ambas as direções; Você também pode recuperar os resultados de pesquisa e tiver o SDK automaticamente desserializá-los para um tipo de sua escolha, como veremos na próxima seção.

> [AZURE.NOTE] O SDK do Azure pesquisa .NET também dá suporte a documentos digitadas dinamicamente usando o `Document` classe, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em situações onde você não souber o esquema de índice em tempo de design, ou onde seria prático de vincular a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com o `Document` classe, bem como sobrecargas digitada que recebem um parâmetro de tipo genérico. Somente o último é usado no código de exemplo neste tutorial. Você pode encontrar mais informações sobre o `Document` classe [aqui](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Uma observação importante sobre tipos de dados**

Ao criar suas próprias classes de modelo para mapear para um índice de pesquisa do Azure, é recomendável declarando propriedades de tipos de valor como `bool` e `int` seja anulável (por exemplo, `bool?` , em vez de `bool`). Se você usar uma propriedade não anulável, é necessário **garantir** que nenhuma documentos no seu índice contêm um valor nulo para o campo correspondente. O SDK nem o serviço de pesquisa do Azure ajudará você a impor isso.

Isso não é apenas um problema hipotético: Imagine um cenário em que você adicionar um novo campo a um índice existente que é do tipo `Edm.Int32`. Depois de atualizar a definição de índice, todos os documentos terá um valor nulo para esse novo campo (desde que todos os tipos são anuláveis na pesquisa do Azure). Se você usar uma classe de modelo com um não anulável `int` propriedade para esse campo, você obterá uma `JsonSerializationException` ao tentar recuperar documentos, como esta:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, recomendamos que você use anulável nas suas classes de modelo como uma prática recomendada.

<a name="JsonDotNet"></a>
#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET

O SDK usa JSON.NET para serializar e desserializar documentos. Você pode personalizar serialização e desserialização se necessário, definindo seus próprios `JsonConverter` ou `IContractResolver` (consulte a [documentação de JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes). Isso pode ser útil quando você quer adaptar uma classe de modelo existente de seu aplicativo para uso com a pesquisa do Azure e outros cenários mais avançados. Por exemplo, com serialização personalizada, você pode:

 - Incluir ou excluir determinadas propriedades da sua classe de modelo sejam armazenados como campos de documento.
 - Mapeie entre os nomes de propriedade no seu código e nomes de campo no seu índice.
 - Crie atributos personalizados que podem ser usados para tanto mapear propriedades para os campos de documento, bem como criar a definição de índice correspondente.

Você pode encontrar exemplos de implementar serialização personalizada os testes de unidade para o SDK do Azure pesquisa .NET no GitHub. Um bom ponto de partida é [esta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Ele contém classes que são usadas pelos testes serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>Procurando documentos no índice

A última etapa no aplicativo de amostra é pesquisar para alguns documentos no índice. O seguinte método faz isso:

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

Primeiro, este método cria um novo `SearchParameters` objeto. Isso é usado para especificar opções adicionais para a consulta como classificação, filtragem, paginação e faceting. Neste exemplo, vamos apenas configura o `Filter` propriedade.

A próxima etapa é realmente executar a consulta de pesquisa. Isso é feito usando o `Documents.Search` método. Nesse caso, podemos passar o texto de pesquisa para usar como uma cadeia de caracteres, além dos parâmetros de pesquisa que criou anteriormente. Nós também especificar `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa o SDK desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

Finalmente, este método itera em todas as correspondências nos resultados da pesquisa, cada documento no console de impressão.

Vamos dar uma olhada mais atenta como este método é chamado:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Na primeira chamada, estamos procurando todos os documentos que contém a consulta termos "sofisticados" ou "wifi". Na segunda chamada, o texto de pesquisa é definido como "*", que significa "Localizar tudo". Você pode encontrar mais informações sobre a sintaxe de expressões de consulta de pesquisa [aqui](https://msdn.microsoft.com/library/azure/dn798920.aspx).

A segunda chamada usa um OData `$filter` expressão, `category eq 'Luxury'`. Isso restringe a pesquisa para retornar somente os documentos onde o `category` campo corresponde exatamente a cadeia de caracteres "Luxo". Você pode saber mais sobre a sintaxe de OData que dá suporte a pesquisa do Azure [aqui](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Agora que você sabe o que fazem essas duas chamadas, ele deve ser mais fáceis de ver por sua saída tem esta aparência:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

A primeira pesquisa retorna dois documentos. O primeiro tem "Decorativo" no nome, enquanto o segundo tem "wifi" `tags` campo. Na segunda pesquisa retorna dois documentos, o que acontecer sejam os documentos somente no índice que têm o `category` campo definido como "Luxo".

Essa etapa conclui o tutorial, mas não parar aqui. **Próximas etapas** fornece recursos adicionais para aprender mais sobre pesquisa do Azure.

## <a name="next-steps"></a>Próximas etapas

- Procure as referências a [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN e [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) .
- Aprofunde o seu conhecimento por meio de [vídeos e outros exemplos e tutoriais](search-video-demo-tutorial-list.md).
- Leia sobre os recursos e os recursos nesta versão do SDK de pesquisa do Azure: [Visão geral de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Examine [as convenções de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber as regras para nomear vários objetos.
- Examine [os tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) na pesquisa do Azure.


## <a name="sample-application-source-code"></a>Código de origem do aplicativo de amostra

Veja aqui o código-fonte completo do exemplo de aplicativo usado neste passo a passo. Observe que você precisará substituir o nome de serviço e espaços reservados de chave de API no Program.cs com seus próprios valores se desejar criar e executar a amostra.

Program.cs:

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
                indexClient.Documents.Index(batch);
            }
            catch (IndexBatchException e)
            {
                // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                // the batch. Depending on your application, you can take compensating actions like delaying and
                // retrying. For this simple demo, we just log the failed document keys and continue.
                Console.WriteLine(
                    "Failed to index some of the documents: {0}",
                    String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
            }

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.cs:

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```
