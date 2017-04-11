<properties
    pageTitle="Criar um índice de pesquisa do Azure usando a API REST | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Crie um índice no código usando a API REST do Azure pesquisa HTTP."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Criar um índice de pesquisa do Azure usando a API REST
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTANTE](search-create-index-rest-api.md)


Este artigo o orientará durante o processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de pesquisa do Azure usando a API de REST de pesquisa do Azure.

Antes de seguir este guia e criar um índice, você deve ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).

Para criar um índice de pesquisa do Azure usando a API REST, você irá emitir uma única solicitação HTTP POST para ponto de extremidade de URL do seu serviço de pesquisa do Azure. Sua definição de índice será contida o corpo da solicitação como conteúdo JSON bem formado.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Eu. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Agora que você tiver configurado um serviço de pesquisa do Azure, você pode emitir solicitações de HTTP contra ponto de extremidade do URL do seu serviço usando a API REST. Entretanto, *todas as* API solicitações devem incluir a api-chave que foi gerada para o serviço de pesquisa que provisionado. Ter uma chave válida estabelece confiança, numa base por solicitação, entre o aplicativo enviar a solicitação e o serviço que lida com ele.

1. Para localizar teclas api do serviço você deve entrar no [Portal do Azure](https://portal.azure.com/)
2. Vá para blade do seu serviço de pesquisa do Azure
3. Clique no ícone "Teclas"

Seu serviço terá *chaves de administração* e *consulta*.

 - Suas *chaves de administrador* principal e secundário conceder direitos completos a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há dois fatores essenciais para que você pode continuar a usar a chave secundária se você decidir gerar a chave primária e vice-versa.
 - Suas *chaves de consulta* conceder acesso somente leitura para índices e documentos e são normalmente distribuídos para aplicativos cliente que emitem solicitações de pesquisa.

Para fins de criação de um índice, você pode usar a chave do administrador principal ou secundário.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Definir o índice de pesquisa do Azure usando JSON bem formado
Uma única solicitação HTTP POST seu serviço irá criar o índice. O corpo da solicitação HTTP POST conterá um único objeto JSON que define o índice de pesquisa do Azure.

1. A primeira propriedade deste objeto JSON é o nome do índice.
2. A segunda propriedade deste objeto JSON é uma matriz JSON chamada `fields` que contém um objeto JSON separado para cada campo no seu índice. Cada um desses objetos JSON contêm vários pares de nome/valor para cada um dos atributos do campo incluindo "nome", "tipo", etc.

É importante que você tenha suas necessidades de negócios e experiência de usuário pesquisa em mente ao criar o índice como cada campo deve ser atribuído os [atributos apropriados](https://msdn.microsoft.com/library/azure/dn798941.aspx). Esses controles de atributos que pesquisar recursos (filtragem, faceting, a classificação de pesquisa de texto completo, etc.) se aplicam a quais campos. Para qualquer atributo que você não especificar, o padrão será habilitar o recurso de pesquisa correspondente, a menos que você especificamente desabilitá-lo.

Para nosso exemplo, nós denominada nosso índice "hotéis" e definida nossos campos da seguinte maneira:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Cuidadosamente escolhemos os atributos de índice para cada campo com base em como podemos acha que serão usados em um aplicativo. Por exemplo, `hotelId` é uma chave exclusiva que as pessoas procurando hotéis provavelmente não saberão, portanto Desabilitaremos pesquisa de texto completo para esse campo definindo `searchable` para `false`, que economiza espaço no índice.

Observe que exatamente um campo no índice de tipo de `Edm.String` deve ser designado como o campo 'key'.

A definição de índice acima usa um analisador de idioma personalizada para o `description_fr` campo porque ele se destina a armazenar texto em francês. Consulte [o tópico no MSDN de suporte de idioma](https://msdn.microsoft.com/library/azure/dn879793.aspx) , bem como o correspondente [postagem de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obter mais informações sobre analisadores de idioma.

## <a name="iii-issue-the-http-request"></a>III. Emitiu a solicitação de HTTP
1. Usando sua definição de índice como o corpo da solicitação, emita uma solicitação HTTP POST para sua URL de ponto de extremidade do serviço de pesquisa do Azure. Na URL, certifique-se de usar seu nome de serviço como o nome do host e colocar o PRI `api-version` como um parâmetro de cadeia de caracteres de consulta (a versão atual do API é `2015-02-28` no momento da publicação deste documento).
2. Nos cabeçalhos da solicitação, especifique o `Content-Type` como `application/json`. Você também precisará fornecer a chave de administração do seu serviço identificado na etapa I no `api-key` cabeçalho.


Você terá que fornecer sua própria chave de nome e api do serviço para emitir a solicitação abaixo:


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Para uma solicitação bem-sucedido, você deve ver o código de status 201 (criado). Para obter mais informações sobre como criar um índice através da API REST, visite a referência à API no [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Para obter mais informações sobre outros códigos de status HTTP que poderia ser retornado no caso de falha, consulte [códigos de status HTTP (Azure pesquisa)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Quando você terminar com um índice e deseja excluí-lo, basta emita uma solicitação de HTTP DELETE. Por exemplo, isso é como podemos seria excluir o índice "hotéis":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Próximo
Depois de criar um índice de pesquisa do Azure, você estará pronto para [carregar seu conteúdo no índice](search-what-is-data-import.md) para poder começar a pesquisar seus dados.
