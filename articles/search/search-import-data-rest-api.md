<properties
    pageTitle="Para carregar dados na pesquisa do Azure usando a API REST | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Saiba como carregar dados em um índice em pesquisa de Azure usando a API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Carregar dados para pesquisa de Azure usando a API REST
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTANTE](search-import-data-rest-api.md)

Este artigo mostrará como usar a [API de REST de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para importar dados para um índice de pesquisa do Azure.

Antes de iniciar este passo a passo, você já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md).

Para enviar documentos para seu índice usando a API REST, você irá emitir uma solicitação HTTP POST para ponto de extremidade de URL do seu índice. O corpo do corpo da solicitação HTTP é um objeto JSON que contém os documentos para ser adicionadas, modificadas ou excluídas.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Eu. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Ao emitir solicitações de HTTP contra seu serviço usando a API REST, *cada* API solicitação deve incluir a api-chave que foi gerada para o serviço de pesquisa que provisionado. Ter uma chave válida estabelece confiança, numa base por solicitação, entre o aplicativo enviar a solicitação e o serviço que lida com ele.

1. Para localizar teclas api do serviço você deve entrar no [Portal do Azure](https://portal.azure.com/)
2. Vá para blade do seu serviço de pesquisa do Azure
3. Clique no ícone "Teclas"

Seu serviço terá *chaves de administração* e *consulta*.

  - Suas *chaves de administrador* principal e secundário conceder direitos completos a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há dois fatores essenciais para que você pode continuar a usar a chave secundária se você decidir gerar a chave primária e vice-versa.
  - Suas *chaves de consulta* conceder acesso somente leitura para índices e documentos e são normalmente distribuídos para aplicativos cliente que emitem solicitações de pesquisa.

Para fins de importação de dados para um índice, você pode usar a chave do administrador principal ou secundário.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decida qual ação indexação usar
Ao usar a API REST, você irá emitir solicitações HTTP POST com corpos de solicitação JSON a URL de ponto de extremidade do índice de pesquisa do Azure. O objeto JSON em seu corpo da solicitação HTTP conterá uma única matriz JSON denominada "valor", que contém objetos JSON representando documentos que você gostaria de adicionar ao seu índice, atualizar ou excluir.

Cada objeto JSON na matriz "valor" representa um documento para ser indexadas. Cada um desses objetos contém chave do documento e especifica a ação desejada de indexação (carregamento, mesclar, excluir, etc). Dependendo de qual do abaixo de ações que você escolher, somente determinados campos devem ser incluídos para cada documento:

@search.action | Descrição | Campos necessários para cada documento | Anotações
--- | --- | --- | ---
`upload` | Um `upload` ação é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se existir. | chave, além de todos os demais campos que você deseja definir | Quando atualizando/substituindo um documento existente, qualquer campo que não esteja especificado na solicitação de terá seu campo definido como `null`. Isso ocorre mesmo quando o campo foi anteriormente definido como um valor não nulo.
`merge` | Atualiza um documento existente com os campos especificados. Se o documento não existir no índice, a mesclagem falhará. | chave, além de todos os demais campos que você deseja definir | Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos de tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com valor `["budget"]` e você executar uma mala direta com valor `["economy", "pool"]` para `tags`, o valor final do `tags` campo será `["economy", "pool"]`. Ele não será `["budget", "economy", "pool"]`.
`mergeOrUpload` | Esta ação se comporta como `merge` se já existe um documento com a chave especificada no índice. Se o documento não existir, ele se comporta como `upload` com um novo documento. | chave, além de todos os demais campos que você deseja definir | -
`delete` | Remove o índice do documento especificado. | Somente chave | Todos os campos que você especificar que o campo de chave será ignorado. Se você quiser remover um campo individual de um documento, use `merge` em vez disso e simplesmente defina o campo explicitamente como nulo.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Construir sua solicitação HTTP e solicite que o corpo
Agora que você reuniu os valores de campo necessário para suas ações de índice, você está pronto para construir a solicitação HTTP real e o corpo da solicitação JSON para importar os dados.

#### <a name="request-and-request-headers"></a>Solicitação e cabeçalhos de solicitação
Na URL, você precisará fornecer o nome do serviço, o nome de índice ("hotéis" neste caso), bem como a versão de API apropriada (a versão atual do API é `2015-02-28` no momento da publicação deste documento). Você precisará definir o `Content-Type` e `api-key` solicitações de cabeçalhos. Para o último, use uma das chaves de administração do serviço.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corpo da solicitação

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Nesse caso, estamos usando `upload`, `mergeOrUpload`, e `delete` como nossas ações de pesquisa.

Suponha que esse índice de "hotéis" exemplo já está preenchido com um número de documentos. Observe como não precisamos especificar todos os campos de documento possíveis ao usar `mergeOrUpload` e como podemos especificado somente a chave de documento (`hotelId`) ao usar `delete`.

Além disso, observe que você só pode incluir até 1000 documentos (ou 16 MB) em uma única solicitação de indexação.

## <a name="iv-understand-your-http-response-code"></a>IV. Entender o código de resposta HTTP
#### <a name="200"></a>200
Depois de enviar uma solicitação de indexação bem-sucedida, você receberá uma resposta HTTP com código de status de `200 OK`. Corpo JSON de resposta HTTP será da seguinte maneira:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Um código de status `207` será retornado quando pelo menos um item não foi indexado com êxito. Corpo JSON de resposta HTTP conterá informações sobre os documentos malsucedidas.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Isso geralmente significa que a carga no seu serviço de pesquisa está tentando alcançar um ponto onde indexação solicitações começará para retornar `503` respostas. Nesse caso, altamente recomendamos que o código do seu cliente retirada e aguarda antes de tentar novamente. Isso lhe dará o sistema algum tempo para recuperar, aumentar as chances de solicitações futuras terá êxito. Repetir rapidamente suas solicitações só serão prolongar a situação.

#### <a name="429"></a>429
Um código de status `429` será retornado quando você tiver excedido sua cota no número de documentos por índice.

#### <a name="503"></a>503
Um código de status `503` será retornado se nenhum dos itens na solicitação foram indexados com êxito. Esse erro significa que o sistema está sob carga pesada e sua solicitação não pode ser processada neste momento.

> [AZURE.NOTE] Nesse caso, altamente recomendamos que o código do seu cliente retirada e aguarda antes de tentar novamente. Isso lhe dará o sistema algum tempo para recuperar, aumentar as chances de solicitações futuras terá êxito. Repetir rapidamente suas solicitações só serão prolongar a situação.

Para obter mais informações sobre ações do documento e respostas de sucesso/erro, consulte [Adicionar, atualizar ou excluir documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). Para obter mais informações sobre outros códigos de status HTTP que poderia ser retornado no caso de falha, consulte [códigos de status HTTP (Azure pesquisa)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Próximo
Depois de preencher o índice de pesquisa do Azure, você estará pronto para começar a emitir consultas para procurar documentos. Consulte o [Índice de pesquisa do Azure sua consulta](search-query-overview.md) para obter detalhes.
