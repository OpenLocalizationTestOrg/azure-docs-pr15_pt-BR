<properties
    pageTitle="Criar um índice de pesquisa do Azure usando o SDK do .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Crie um índice no código usando o SDK do .NET de pesquisa do Azure."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Criar um índice de pesquisa do Azure usando o SDK do .NET
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTANTE](search-create-index-rest-api.md)


Este artigo o orientará durante o processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de pesquisa do Azure usando o [SDK do Azure pesquisa .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de seguir este guia e criar um índice, você deve ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).

Observe que todo o código de exemplo neste artigo escrito em c#. Você pode encontrar a fonte completa código [no GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Eu. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Agora que você tiver configurado um serviço de pesquisa do Azure, você está quase pronto para emitir solicitações contra seu ponto de extremidade do serviço usando o SDK do .NET. Primeiro, você precisará obter uma das administração api-chaves que foi gerado para o serviço de pesquisa que provisionado. O SDK do .NET enviará essa api-chave em cada solicitação ao seu serviço. Ter uma chave válida estabelece confiança, numa base por solicitação, entre o aplicativo enviar a solicitação e o serviço que lida com ele.

1. Para localizar teclas api do serviço você deve entrar no [Portal do Azure](https://portal.azure.com/)
2. Vá para blade do seu serviço de pesquisa do Azure
3. Clique no ícone "Teclas"

Seu serviço terá *chaves de administração* e *consulta*.

  - Suas *chaves de administrador* principal e secundário conceder direitos completos a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há dois fatores essenciais para que você pode continuar a usar a chave secundária se você decidir gerar a chave primária e vice-versa.
  - Suas *chaves de consulta* conceder acesso somente leitura para índices e documentos e são normalmente distribuídos para aplicativos cliente que emitem solicitações de pesquisa.

Para fins de criação de um índice, você pode usar a chave do administrador principal ou secundário.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Criar uma instância da classe SearchServiceClient
Para começar a usar o SDK do Azure pesquisa .NET, você precisará criar uma instância do `SearchServiceClient` classe. Essa classe tem vários construtores. Aquele que você deseja leva seu nome de serviço de pesquisa e um `SearchCredentials` objeto como parâmetros. `SearchCredentials`quebra sua chave de api.

O código a seguir cria um novo `SearchServiceClient` usando valores para o nome do serviço de pesquisa e a api-chave que estão armazenados no arquivo de configuração do aplicativo (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`tem um `Indexes` propriedade. Essa propriedade fornece todos os métodos que você precisa para criar, lista, atualizar ou excluir índices de pesquisa do Azure.

> [AZURE.NOTE] O `SearchServiceClient` classe gerencia conexões com o seu serviço de pesquisa. Para evitar abrindo muitas conexões, você deve tentar compartilhar uma única instância do `SearchServiceClient` em seu aplicativo se possível. Seus métodos são thread-safe para habilitar o compartilhamento de tal.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Definir seu índice de pesquisa do Azure usando o `Index` classe
Uma única chamada para o `Indexes.Create` método criará o índice. Este método recebe como um parâmetro um `Index` objeto que define o índice de pesquisa do Azure. Você precisa criar um `Index` objeto e inicializá-lo da seguinte maneira:

1. Definir o `Name` propriedade do `Index` objeto para o nome do seu índice.
2. Definir o `Fields` propriedade do `Index` objeto para uma matriz de `Field` objetos. Cada um do `Field` objetos define o comportamento de um campo no seu índice. Você pode fornecer o nome do campo ao qual o construtor, juntamente com o tipo de dados (ou analisador para campos de cadeia de caracteres). Você também pode definir outras propriedades como `IsSearchable`, `IsFilterable`, etc.

É importante que você tenha suas necessidades de negócios e experiência de usuário pesquisa em mente ao criar o índice como cada `Field` deve ser atribuída as [propriedades adequadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estas propriedades de controle que pesquisar recursos (filtragem, faceting, a classificação de pesquisa de texto completo, etc.) se aplicam a quais campos. Para qualquer propriedade que você não definir explicitamente, o `Field` padrões para desabilitar o recurso de pesquisa correspondente, a menos que você ativá-la especificamente de classe.

Para nosso exemplo, nós denominada nosso índice "hotéis" e definida nossos campos da seguinte maneira:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Cuidadosamente escolhemos os valores de propriedade para cada `Field` com base em como podemos acha que serão usados em um aplicativo. Por exemplo, é provável que as pessoas pesquisando hotéis estará interessadas a correspondência de palavra-chave na `description` campo, portanto podemos habilitar a pesquisa de texto completo para esse campo definindo `IsSearchable` para `true`.

Observe que exatamente um campo no índice de tipo de `DataType.String` deve ser designado como o campo de _chave_ definindo `IsKey` para `true` (consulte `hotelId` no exemplo acima).

A definição de índice acima usa um analisador de idioma personalizada para o `description_fr` campo porque ele se destina a armazenar texto em francês. Consulte [o tópico no MSDN de suporte de idioma](https://msdn.microsoft.com/library/azure/dn879793.aspx) , bem como o correspondente [postagem de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obter mais informações sobre analisadores de idioma.

> [AZURE.NOTE]  Observe que, passando `AnalyzerName.FrLucene` no construtor, a `Field` automaticamente será do tipo `DataType.String` e terão `IsSearchable` definida como `true`.

## <a name="iv-create-the-index"></a>IV. Criar o índice
Agora que você tem uma inicializado `Index` objeto, você pode criar o índice simplesmente chamando `Indexes.Create` em seu `SearchServiceClient` objeto:

```csharp
serviceClient.Indexes.Create(definition);
```

Para uma solicitação bem-sucedida, o método retornará normalmente. Se houver um problema com a solicitação como um parâmetro inválido, o método irá acionar `CloudException`.

Quando você terminar com um índice e deseja excluí-lo, basta chamar o `Indexes.Delete` método no seu `SearchServiceClient`. Por exemplo, isso é como podemos seria excluir o índice "hotéis":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] O código de exemplo neste artigo usa os métodos síncronos do Azure pesquisa .NET SDK para simplificar. Recomendamos que você usa os métodos assíncronos em seus próprios aplicativos para mantê-los scalable e ágil. Por exemplo, nos exemplos acima você poderia usar `CreateAsync` e `DeleteAsync` , em vez de `Create` e `Delete`.

## <a name="next"></a>Próximo
Depois de criar um índice de pesquisa do Azure, você estará pronto para [carregar seu conteúdo no índice](search-what-is-data-import.md) para poder começar a pesquisar seus dados.
