<properties
   pageTitle="Atualização para o SDK do Azure Search .NET versão 1.1 | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
   description="Atualização para o SDK do Azure Search .NET versão 1.1"
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
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Atualização para o SDK do Azure pesquisa .NET versão 2.0-preview

Se você estiver usando a versão 1.1 ou mais antiga do [Azure pesquisa .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), este artigo o ajudará a atualizar seu aplicativo para usar a próxima versão principal, visualização de 2.0.

Para obter uma explicação mais geral do SDK, incluindo exemplos, veja [como usar a pesquisa de um aplicativo .NET Azure](search-howto-dotnet-sdk.md).

Versão 2.0-visualização do Azure pesquisa .NET SDK contém algumas alterações de versões anteriores. Essas principalmente são secundárias, portanto, alterar seu código deve exigem apenas esforço mínimo. Consulte [as etapas de atualização](#UpgradeSteps) para obter instruções sobre como alterar seu código para usar a nova versão do SDK.

> [AZURE.NOTE] Se você estiver usando a versão mais antiga ou 0.13 visualização, você deve atualizar para a versão 1.1 primeiro e, em seguida, atualize a visualização de 2.0. Consulte [Apêndice: etapas para atualizar para a versão 1.1](#UpgradeStepsV1) para obter instruções.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Novidades na versão 2.0-visualização

Versão 2.0-preview é a primeira versão do SDK do .NET de pesquisa do Azure para target uma versão de visualização da API de REST de pesquisa do Azure, especificamente 2015-02-28-visualização. Isso possibilita usar muitos recursos de visualização do Azure pesquisa de um aplicativo .NET, incluindo o seguinte:

- [Analisadores personalizados](https://aka.ms/customanalyzers)
- Suporte de indexador de [Armazenamento de Blob do Azure](search-howto-indexing-azure-blob-storage.md) e [Armazenamento de tabela do Azure](search-howto-indexing-azure-tables.md)
- Personalização de indexador via [mapeamentos de campo](search-indexer-field-mappings.md)
- ETags suporte para ativar a atualização simultâneos confiáveis de definições de índice, indexadores e fontes de dados
- Suporte para .NET Core e .NET portátil perfil 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Etapas de atualização

Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o NuGet Package Manager Console ou clicando em suas referências do projeto e selecionando "Gerenciar NuGet pacotes …" no Visual Studio. Verifique se você habilitar pacotes de pré-lançamento selecionando "Incluir pré-lançamento" no NuGet "Gerenciar pacotes" janela no Visual Studio, ou usando o `-IncludePrerelease` alternar se você estiver usando o NuGet Package Manager Console.

Depois que NuGet tiver baixado os novos pacotes e suas dependências, reconstrua seu projeto. Dependendo de como seu código está estruturado, ele pode recriar com êxito. Nesse caso, você está pronto para ir!

Se sua compilação falhar, você verá um erro de compilação semelhante ao seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A próxima etapa é corrigir esse erro de compilação. Consulte [quebrar as alterações na versão 2.0-visualização](#ListOfChanges) para obter detalhes sobre o que faz com que o erro e como corrigi-lo.

Você pode ver avisos de compilação adicionais relacionados ao obsoletos métodos ou propriedades. Os avisos incluirá instruções sobre o que usar em vez do recurso preterido. Por exemplo, se seu aplicativo usa o `SearchRequestOptions.RequestId` propriedade, você deve receber um aviso dizendo que`"This property is deprecated. Please use ClientRequestId instead."`

Depois de ter corrigido os erros de compilação, você pode fazer alterações ao seu aplicativo para tirar proveito de novas funcionalidades, se desejar. Novos recursos no SDK serão detalhados nas [Novidades no versão 2.0-visualização](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Alterações significativas da versão 2.0-visualização

Há apenas uma alteração significativa na versão 2.0-visualização que pode exigir alterações de código além de recriar seu aplicativo.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno de Indexes.GetClient

O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, ele retornado `SearchIndexClient`, mas isso é alterado para `ISearchIndexClient` na versão 2.0-visualização. Isso é para clientes que desejam simulação de suporte a `GetClient` método para testes de unidade retornando uma implementação simulação de `ISearchIndexClient`.

#### <a name="example"></a>Exemplo

Se seu código tem esta aparência:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Você pode alterar para essa opção para corrigir os erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do Azure pesquisa .NET, consulte nosso atualizados recentemente [instruções](search-howto-dotnet-sdk.md).

Agradecemos seus comentários sobre o SDK. Se você tiver problemas, fique à vontade para pedir conosco ajuda no [Fórum do MSDN de pesquisa do Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Se você encontrar um erro, você pode enviar um problema no [repositório do Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixo seu título de problema com "SDK Search:".

Obrigado por usando a pesquisa do Azure!

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Apêndice: Etapas para atualizar para a versão 1.1 

> [AZURE.NOTE] Esta seção aplica-se somente aos usuários da versão Azure pesquisa .NET SDK 0.13 preview e anteriores.

Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o NuGet Package Manager Console ou clicando em suas referências do projeto e selecionando "Gerenciar NuGet pacotes …" no Visual Studio.

Depois que NuGet tiver baixado os novos pacotes e suas dependências, reconstrua seu projeto.

Se você estava usando anteriormente versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview, a compilação deve êxito e estiver pronto para ir!

Se você estava usando anteriormente versão 0.13.0-preview ou anterior, você deverá ver construir erros como o seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A próxima etapa é corrigir os erros de compilação um por vez. A maioria necessitará alterar alguns nomes de classe e método que foram renomeados no SDK. [Lista de alterações na versão 1.1 significativas](#ListOfChangesV1) contém uma lista dessas alterações de nome.

Se você estiver usando classes personalizadas para modelar seus documentos e essas classes têm propriedades de tipos primitivos não anulável (por exemplo, `int` ou `bool` em c#), há uma correção de bugs na versão 1.1 do SDK dos quais você deve estar ciente. Consulte [correções de bugs na versão 1.1](#BugFixesV1) para obter mais detalhes.

Finalmente, depois de ter corrigido os erros de compilação, você pode fazer alterações ao seu aplicativo para tirar proveito de novas funcionalidades, se desejar.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Lista de alterações significativas na versão 1.1

A lista a seguir é ordenada pela probabilidade de que a alteração afetará o código do seu aplicativo.

#### <a name="indexbatch-and-indexaction-changes"></a>Alterações IndexBatch e IndexAction

`IndexBatch.Create`foi renomeada para `IndexBatch.New` e não tem mais uma `params` argumento. Você pode usar `IndexBatch.New` para lotes que misturar diferentes tipos de ações (mesclagens, exclusões, etc.). Além disso, há novos métodos estáticos para a criação de lotes onde todas as ações são as mesmas: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`.

`IndexAction`não tem mais construtor público e suas propriedades agora são imutáveis. Você deve usar os novos métodos estáticos para criar ações para diferentes finalidades: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`. `IndexAction.Create`foi removida. Se você usou a sobrecarga que utiliza apenas um documento, verifique se usar `Upload` em vez disso.

##### <a name="example"></a>Exemplo

Se seu código tem esta aparência:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Você pode alterar para essa opção para corrigir os erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se desejar, você pode simplificá-lo ainda mais a esta:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Alterações de IndexBatchException

O `IndexBatchException.IndexResponse` propriedade foi renomeada para `IndexingResults`, e seu tipo está agora `IList<IndexingResult>`.

##### <a name="example"></a>Exemplo

Se seu código tem esta aparência:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Você pode alterar para essa opção para corrigir os erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Alterações de método de operação

Cada operação no SDK do Azure pesquisa .NET é exposta como um conjunto de sobrecargas do método para chamadores síncronos e assíncronos. As assinaturas e o cálculo dessas sobrecargas do método foi alterado na versão 1.1.

Por exemplo, a operação "Obter as estatísticas de índice" em versões mais antigas do SDK expostos essas assinaturas:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

As assinaturas de método para a mesma operação na versão 1.1 tem esta aparência:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Começando com a versão 1.1, o SDK do Azure pesquisa .NET organiza os métodos de operação diferente:

 - Parâmetros opcionais agora são modelados como padrão parâmetros preferir que sobrecargas do método adicional. Isso reduz o número de sobrecargas do método, às vezes drasticamente.
 - Agora, os métodos de extensão ocultar muitas os detalhes estranhos de HTTP do chamador. Por exemplo, versões mais antigas do SDK retornado um objeto de resposta com um código de status HTTP, que você geralmente não precisa verificar porque métodos de operação joga `CloudException` por qualquer código de status que indica um erro. Os novos métodos de extensão basta retornam objetos do modelo, economizando o problema de ter que não quebrá-las em seu código.
 - Por outro lado, o núcleo interfaces agora expõem métodos que proporcionam mais controle no nível HTTP caso necessário. Agora, você pode passar em cabeçalhos HTTP personalizados a serem incluídos no solicitações e a nova `AzureOperationResponse<T>` tipo de retorno lhe dá acesso direto para o `HttpRequestMessage` e `HttpResponseMessage` para a operação. `AzureOperationResponse`é definido na `Microsoft.Rest.Azure` namespace e substitui `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Alterações de ScoringParameters

Uma nova classe denominada `ScoringParameter` foi adicionada no SDK do mais recente para tornar mais fácil fornecer parâmetros para perfis de pontuação em uma consulta de pesquisa. Anteriormente o `ScoringProfiles` propriedade do `SearchParameters` classe foi digitada como `IList<string>`; Agora ele é digitado como `IList<ScoringParameter>`.

##### <a name="example"></a>Exemplo

Se seu código tem esta aparência:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Você pode alterar para essa opção para corrigir os erros de compilação: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Alterações de classe do modelo

Devido a assinatura altera descrito em [método de operação altera](#OperationMethodChanges), muitas classes no `Microsoft.Azure.Search.Models` namespace foram renomeados ou removidos. Por exemplo:

 - `IndexDefinitionResponse`foi substituído pelo`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`foi renomeado para`DocumentSearchResult`
 - `IndexResult`foi renomeado para`IndexingResult`
 - `Documents.Count()`agora retorna um `long` com a contagem de documento, em vez de uma`DocumentCountResponse`
 - `IndexGetStatisticsResponse`foi renomeado para`IndexGetStatisticsResult`
 - `IndexListResponse`foi renomeado para`IndexListResult`

Para resumir, `OperationResponse`-derivado classes existentes somente para quebrar um objeto de modelo foram removidos. As classes restantes tiveram sua sufixo alterado de `Response` para `Result`.

##### <a name="example"></a>Exemplo

Se seu código tem esta aparência:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Você pode alterar para essa opção para corrigir os erros de compilação:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Classes de resposta e IEnumerable

Uma alteração adicional que pode afetar o seu código é que classes de resposta que contêm coleções já não implementam `IEnumerable<T>`. Em vez disso, você pode acessar diretamente a propriedade de coleção. Por exemplo, se seu código tem esta aparência:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Você pode alterar para essa opção para corrigir os erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Observação importante para aplicativos web

Se você tiver um aplicativo web que serializa `DocumentSearchResponse` diretamente para enviar os resultados da pesquisa para o navegador, você precisará alterar seu código ou os resultados não serializará corretamente. Por exemplo, se seu código tem esta aparência:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Você pode alterá-lo obtendo o `.Results` propriedade da resposta de pesquisa para corrigir o processamento de resultado de pesquisa:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Você terá que procure nesses casos em seu código que você mesmo; **O compilador não avisará** porque `JsonResult.Data` for do tipo `object`.

#### <a name="cloudexception-changes"></a>Alterações de CloudException

O `CloudException` classe foi movido do `Hyak.Common` namespace para o `Microsoft.Rest.Azure` namespace. Além disso, sua `Error` propriedade foi renomeada para `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações SearchServiceClient e SearchIndexClient

O tipo do `Credentials` propriedade mudou de `SearchCredentials` à sua classe base, `ServiceClientCredentials`. Se você precisar acessar o `SearchCredentials` de um `SearchIndexClient` ou `SearchServiceClient`, use o novo `SearchCredentials` propriedade.

Em versões mais antigas do SDK, `SearchServiceClient` e `SearchIndexClient` tinha construtores que levaram uma `HttpClient` parâmetro. Estes foram substituídas pelos construtores que têm um `HttpClientHandler` e uma matriz de `DelegatingHandler` objetos. Isso facilita a instalar manipuladores personalizados para processar previamente solicitações HTTP, se necessário.

Por fim, os construtores que levaram uma `Uri` e `SearchCredentials` foram alteradas. Por exemplo, se você tiver o código que tem esta aparência:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Você pode alterar para essa opção para corrigir os erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observe também que o tipo do parâmetro credenciais foi alterado para `ServiceClientCredentials`. Este é provavelmente não afetam seu código desde `SearchCredentials` é derivado de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Passando uma ID de solicitação

Em versões mais antigas do SDK, você poderia definir um ID de solicitação na `SearchServiceClient` ou `SearchIndexClient` e ele seria incluído em cada solicitação para a API REST. Isso é útil para solucionar problemas com o serviço de pesquisa se você precisar contatar o suporte. Entretanto, é mais útil para definir um ID de solicitação exclusivo para cada operação em vez de usar a mesma identificação para todas as operações. Por esse motivo, a `SetClientRequestId` métodos de `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, você pode passar um ID de solicitação para cada método de operação via opcional `SearchRequestOptions` parâmetro.

> [AZURE.NOTE] Em uma versão futura do SDK, adicionaremos um novo mecanismo para objetos de configuração uma ID de solicitação globalmente no cliente que seja consistente com a abordagem usada pelos outros SDKs do Azure.

#### <a name="example"></a>Exemplo

Se você tiver o código que tem esta aparência:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Você pode alterar para essa opção para corrigir os erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Alterações de nomes de interface

Os nomes de interface de grupo de operação foram alterados para ser consistente com os nomes de propriedade correspondentes:

 - O tipo de `ISearchServiceClient.Indexes` foi renomeado de `IIndexOperations` para `IIndexesOperations`.
 - O tipo de `ISearchServiceClient.Indexers` foi renomeado de `IIndexerOperations` para `IIndexersOperations`.
 - O tipo de `ISearchServiceClient.DataSources` foi renomeado de `IDataSourceOperations` para `IDataSourcesOperations`.
 - O tipo de `ISearchIndexClient.Documents` foi renomeado de `IDocumentOperations` para `IDocumentsOperations`.

Essa alteração é provavelmente não afetam seu código, a menos que você criou simulações dessas interfaces para fins de teste.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correções de bugs na versão 1.1

Ocorreu um erro em versões mais antigas do Azure pesquisa .NET SDK relacionado à serialização de classes de modelo personalizado. O erro pode ocorrer se você tiver criado uma classe de modelo personalizado com uma propriedade de um tipo de valor não-nulo.

#### <a name="steps-to-reproduce"></a>Etapas para reproduzir

Crie uma classe de modelo personalizado com uma propriedade do tipo de valor não-nulo. Por exemplo, adicione uma pública `UnitCount` propriedade do tipo `int` em vez de `int?`.

Se você indexar um documento com o valor padrão desse tipo (por exemplo, 0 para `int`), o campo será nulo na pesquisa do Azure. Se você procurar subsequentemente esse documento, o `Search` chamada irá acionar `JsonSerializationException` reclamações que não é possível converter `null` para `int`.

Além disso, os filtros podem não funcionar como esperado como null escrita ao índice em vez do valor desejado.

#### <a name="fix-details"></a>Corrigir detalhes

Podemos ter corrigido esse problema na versão 1.1 do SDK. Agora, se você tiver uma classe de modelo como esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e você definir `IntValue` como 0, esse valor é agora corretamente serializada como 0 na conexão e armazenado como 0 no índice. Encontrando ARRED também funciona como esperado.

Há um problema potencial a serem consideradas com essa abordagem: se você usa um tipo de modelo com uma propriedade não anulável, você tem para **garantir** que nenhuma documentos no seu índice contêm um valor nulo para o campo correspondente. O SDK nem a API de REST de pesquisa do Azure ajudará você a impor isso.

Isso não é apenas um problema hipotético: Imagine um cenário em que você adicionar um novo campo a um índice existente que é do tipo `Edm.Int32`. Depois de atualizar a definição de índice, todos os documentos terá um valor nulo para esse novo campo (desde que todos os tipos são anuláveis na pesquisa do Azure). Se você usar uma classe de modelo com um não anulável `int` propriedade para esse campo, você obterá uma `JsonSerializationException` ao tentar recuperar documentos, como esta:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, ainda recomendamos que você use anulável nas suas classes de modelo como uma prática recomendada.

Para obter mais detalhes sobre esse bug e a correção, consulte [Este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
