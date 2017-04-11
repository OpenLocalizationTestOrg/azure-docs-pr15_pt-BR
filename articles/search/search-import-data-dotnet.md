<properties
    pageTitle="Para carregar dados na pesquisa do Azure usando o SDK do .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Saiba como carregar dados em um índice na pesquisa do Azure usando o SDK do .NET."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Carregar dados para pesquisa de Azure usando o SDK do .NET
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTANTE](search-import-data-rest-api.md)

Este artigo mostrará como usar o [SDK do Azure pesquisa .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) para importar dados para um índice de pesquisa do Azure.

Antes de iniciar este passo a passo, você já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md). Este artigo também pressupõe que você já tiver criado uma `SearchServiceClient` objeto, conforme mostrado em [criar um índice de pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

Observe que todo o código de exemplo neste artigo escrito em c#. Você pode encontrar a fonte completa código [no GitHub](http://aka.ms/search-dotnet-howto).

Para enviar documentos para seu índice usando o SDK do .NET, você precisará:

  1. Criar um `SearchIndexClient` objeto para se conectar ao índice de pesquisa.
  2. Criar um `IndexBatch` que contêm os documentos a serem adicionadas, modificadas ou excluídas.
  3. Chamar o `Documents.Index` método de sua `SearchIndexClient` para enviar a `IndexBatch` ao seu índice de pesquisa.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>Eu. Criar uma instância da classe SearchIndexClient
Para importar dados para seu índice usando o SDK do Azure pesquisa .NET, você precisará criar uma instância do `SearchIndexClient` classe. Você pode construir esta instância por conta própria, mas é mais fácil se você já tiver um `SearchServiceClient` instância ligar para o seu `Indexes.GetClient` método. Por exemplo, aqui está como você faria obter um `SearchIndexClient` para o índice chamado "hotéis" de um `SearchServiceClient` chamado `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] Em um aplicativo de pesquisa típica, gerenciamento de índice e população é realizado por um componente separado de consultas de pesquisa. `Indexes.GetClient`é conveniente para preencher um índice porque poupa o problema de fornecer outro `SearchCredentials`. Ela faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte do seu aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você pode passar em uma chave de consulta em vez de uma chave de administrador. Isso é consistente com o [princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e ajuda a tornar seu aplicativo mais seguro. Você pode encontrar mais informações sobre chaves de administração e consulta na [referência de API de REST de pesquisa do Azure no MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`tem um `Documents` propriedade. Essa propriedade fornece todos os métodos que você precisa adicionar, modificar, excluir ou consultar documentos no seu índice.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decida qual ação indexação usar
Para importar dados usando o SDK do .NET, você precisará empacotar seus dados em um `IndexBatch` objeto. Um `IndexBatch` encapsula uma coleção de `IndexAction` objetos, cada um deles contém um documento e uma propriedade que informa Azure pesquisa qual ação a ser executada nesse documento (carregamento, mesclar, excluir, etc). Dependendo de qual do abaixo de ações que você escolher, somente determinados campos devem ser incluídos para cada documento:

Ação | Descrição | Campos necessários para cada documento | Anotações
--- | --- | --- | ---
`Upload` | Um `Upload` ação é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se existir. | chave, além de todos os demais campos que você deseja definir | Quando atualizando/substituindo um documento existente, qualquer campo que não esteja especificado na solicitação de terá seu campo definido como `null`. Isso ocorre mesmo quando o campo foi anteriormente definido como um valor não nulo.
`Merge` | Atualiza um documento existente com os campos especificados. Se o documento não existir no índice, a mesclagem falhará. | chave, além de todos os demais campos que você deseja definir | Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos de tipo `DataType.Collection(DataType.String)`. Por exemplo, se o documento contém um campo `tags` com valor `["budget"]` e você executar uma mala direta com valor `["economy", "pool"]` para `tags`, o valor final do `tags` campo será `["economy", "pool"]`. Ele não será `["budget", "economy", "pool"]`.
`MergeOrUpload` | Esta ação se comporta como `Merge` se já existe um documento com a chave especificada no índice. Se o documento não existir, ele se comporta como `Upload` com um novo documento. | chave, além de todos os demais campos que você deseja definir | -
`Delete` | Remove o índice do documento especificado. | Somente chave | Todos os campos que você especificar que o campo de chave será ignorado. Se você quiser remover um campo individual de um documento, use `Merge` em vez disso e simplesmente defina o campo explicitamente como nulo.

Você pode especificar a ação que você deseja usar com os vários métodos estáticos da `IndexBatch` e `IndexAction` classes, conforme mostrado na próxima seção.

## <a name="iii-construct-your-indexbatch"></a>III. Construir seu IndexBatch
Agora que você sabe quais ações a serem executadas em seus documentos, você está pronto para construir a `IndexBatch`. O exemplo a seguir mostra como criar um lote com algumas ações diferentes. Observe que o nosso exemplo usa uma classe personalizada chamada `Hotel` que mapeia para um documento no índice "hotéis".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Nesse caso, estamos usando `Upload`, `MergeOrUpload`, e `Delete` como nossas ações de pesquisa, conforme especificado pelos métodos chamados a `IndexAction` classe.

Suponha que esse índice de "hotéis" exemplo já está preenchido com um número de documentos. Observe como não precisamos especificar todos os campos de documento possíveis ao usar `MergeOrUpload` e como podemos especificado somente a chave de documento (`HotelId`) ao usar `Delete`.

Além disso, observe que você só pode incluir até 1000 documentos em uma única solicitação de indexação.

> [AZURE.NOTE] Neste exemplo, estamos aplicando diferentes ações para diferentes documentos. Se você quiser executar as mesmas ações em todos os documentos em lote, em vez de chamada `IndexBatch.New`, você pode usar outros métodos estáticos de `IndexBatch`. Por exemplo, você poderia criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete`. Um conjunto de documentos que esses métodos adotam (objetos do tipo `Hotel` neste exemplo), em vez de `IndexAction` objetos.

## <a name="iv-import-data-to-the-index"></a>IV. Importar dados para o índice
Agora que você tem uma inicializado `IndexBatch` objeto, você poderá enviá-lo ao índice chamando `Documents.Index` em seu `SearchIndexClient` objeto. O exemplo a seguir mostra como chamar `Index`, assim como algumas etapas adicionais que você precisará executar:

```csharp
try
{
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

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Observação a `try` / `catch` ao redor a chamada para a `Index` método. O bloco de variável manipula um caso de erro importantes para indexação. Se seu serviço de pesquisa do Azure falhar indexar alguns dos documentos em lote, um `IndexBatchException` é lançada por `Documents.Index`. Isso pode acontecer se você estiver indexando documentos enquanto seu serviço está sob carga pesada. **É altamente recomendável explicitamente lidar com esse caso em seu código.** Você pode atrasar e tente novamente a indexação os documentos que falhou, ou você pode fazer logon e continuar como a amostra faz, ou você pode fazer algo diferente dependendo de requisitos de consistência de dados do seu aplicativo.

Finalmente, o código no exemplo acima atrasos por dois segundos. Indexação acontece assíncrona no seu serviço de pesquisa do Azure, para que o aplicativo de exemplo precisa esperar alguns instantes para garantir que os documentos estão disponíveis para pesquisa. Atrasos assim apenas normalmente são necessários em demonstrações, testes e aplicativos de exemplo.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK do .NET trata documentos

Você pode estar se perguntando como o SDK do Azure pesquisa .NET é capaz de carregar instâncias de uma classe definida pelo usuário como `Hotel` ao índice. Para ajudar a responder essa pergunta, vamos examinar o `Hotel` classe, que mapeia para o esquema de índice definido em [criar um índice de pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição de índice, mas com uma diferença fundamental: O nome de cada campo começa com uma letra minúscula ("camelCase"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiuscula ("ocorrência do Pascal"). Este é um cenário comum em aplicativos .NET que executar associação de dados onde o esquema de destino está fora do controle do desenvolvedor do aplicativo. Em vez de ter violar .NET diretrizes de nomenclatura fazendo nomes de propriedade camel-caso, você pode informar o SDK para mapear os nomes de propriedade para camel-caso automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo.

> [AZURE.NOTE] O SDK do Azure pesquisa .NET usa a biblioteca de [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar seus objetos do modelo personalizado para e do JSON. Você pode personalizar essa serialização se necessário. Você pode encontrar mais detalhes [Atualizando para o SDK do Azure Search .NET versão 1.1](search-dotnet-sdk-migration.md#WhatsNew). Um exemplo é o uso do `[JsonProperty]` atributo na `DescriptionFr` propriedade no código de exemplo acima.

O segundo importante sobre a `Hotel` classe são os tipos de dados das propriedades públicos. Mapeiam os tipos de .NET dessas propriedades para seus tipos de campo equivalente na definição de índice. Por exemplo, o `Category` string mapas de propriedade para o `category` campo, que é do tipo `DataType.String`. Há mapeamentos de tipo semelhante entre `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset`, etc. As regras específicas para o mapeamento de tipo documentadas com a `Documents.Get` método no [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Essa capacidade de usar suas próprias classes como documentos funciona em ambas as direções; Você também pode recuperar os resultados de pesquisa e tem o SDK automaticamente desserializá-los para um tipo de sua escolha, como mostrado no [próximo artigo](search-query-dotnet.md).

> [AZURE.NOTE] O SDK do Azure pesquisa .NET também dá suporte a documentos digitadas dinamicamente usando o `Document` classe, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em situações onde você não souber o esquema de índice em tempo de design, ou onde seria prático de vincular a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com o `Document` classe, bem como sobrecargas digitada que recebem um parâmetro de tipo genérico. Somente o último é usado no código de exemplo neste artigo. Você pode encontrar mais informações sobre o `Document` classe [no MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Uma observação importante sobre tipos de dados**

Ao criar suas próprias classes de modelo para mapear para um índice de pesquisa do Azure, é recomendável declarando propriedades de tipos de valor como `bool` e `int` seja anulável (por exemplo, `bool?` , em vez de `bool`). Se você usar uma propriedade não anulável, é necessário **garantir** que nenhuma documentos no seu índice contêm um valor nulo para o campo correspondente. O SDK nem o serviço de pesquisa do Azure ajudará você a impor isso.

Isso não é apenas um problema hipotético: Imagine um cenário em que você adicionar um novo campo a um índice existente que é do tipo `DataType.Int32`. Depois de atualizar a definição de índice, todos os documentos terá um valor nulo para esse novo campo (desde que todos os tipos são anuláveis na pesquisa do Azure). Se você usar uma classe de modelo com um não anulável `int` propriedade para esse campo, você obterá uma `JsonSerializationException` ao tentar recuperar documentos, como esta:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, recomendamos que você use anulável nas suas classes de modelo como uma prática recomendada.

## <a name="next"></a>Próximo
Depois de preencher o índice de pesquisa do Azure, você estará pronto para começar a emitir consultas para procurar documentos. Consulte o [Índice de pesquisa do Azure sua consulta](search-query-overview.md) para obter detalhes.
