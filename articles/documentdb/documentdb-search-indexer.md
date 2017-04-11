<properties
    pageTitle="Conectando DocumentDB com pesquisa de Azure usando indexadores | Microsoft Azure"
    description="Este artigo mostra como usar para indexador de pesquisa do Azure com DocumentDB como uma fonte de dados."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Conectando DocumentDB com pesquisa de Azure usando indexadores

Se você estiver procurando para implementar pesquisa excelentes experiências seus dados de DocumentDB, use o indexador de pesquisa do Azure para DocumentDB! Neste artigo, mostraremos como integrar Azure DocumentDB com a pesquisa do Azure sem precisar escrever qualquer código para manter a infraestrutura de indexação!

Para configurar isso, você precisa [Configurar uma conta do Azure pesquisa](../search/search-create-service-portal.md) (não é necessário atualizar para pesquisa padrão) e, em seguida, chamar a [API de REST de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para criar uma **fonte de dados** do DocumentDB e um **indexador** fonte de dados.

Em solicitações de envio do pedido para interagir com as APIs REST, você pode usar [carteiro](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)ou qualquer ferramenta de sua preferência.


##<a id="Concepts"></a>Azure conceitos de indexador de pesquisa

Azure suporta a pesquisa a criação e o gerenciamento de dados de fontes (incluindo DocumentDB) e indexadores que operam contra as fontes de dados.

Uma **fonte de dados** Especifica que dados precisam ser indexadas, credenciais para acessar os dados e políticas para habilitar a pesquisa do Azure com eficiência identificar alterações nos dados (tais como modificados ou excluídos documentos dentro de sua coleção). A fonte de dados é definida como um recurso independente, para que ele pode ser usado por vários indexadores.

Um **indexador** descreve como os dados fluem de sua fonte de dados em um índice de pesquisa de destino. Você deve planejar a criação de um indexador para cada combinação de origem de índice e dados de destino. Embora você possa ter vários indexadores escrevendo para o mesmo índice, um indexador pode apenas gravar em um único índice. Um indexador é usado para:

- Execute uma única cópia dos dados para preencher um índice.
- Sincronize um índice com alterações na fonte de dados em uma agenda. O agendamento é parte da definição do indexador.
- Invoca atualizações sob demanda para um índice conforme necessário.

##<a id="CreateDataSource"></a>Etapa 1: Criar uma fonte de dados

Emita uma solicitação de HTTP POST para criar uma nova fonte de dados no seu serviço de pesquisa do Azure, incluindo os cabeçalhos de solicitação a seguir.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

O `api-version` é necessária. Os valores válidos incluem `2015-02-28` ou uma versão posterior. Visite [as versões de API na pesquisa do Azure](../search/search-api-versions.md) para ver todas as versões de API de pesquisa.

O corpo da solicitação contém a definição de fonte de dados, que deve incluir os seguintes campos:

- **nome**: escolha qualquer nome para representar o banco de dados de DocumentDB.

- **tipo**: uso `documentdb`.

- **credenciais**:

    - **connectionString**: obrigatório. Especifica as informações de conexão para o banco de dados do Azure DocumentDB no seguinte formato:`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contêiner**:

    - **nome**: obrigatório. Especifica a identificação da coleção DocumentDB para ser indexadas.

    - **consulta**: opcional. Você pode especificar uma consulta para nivelar um documento JSON aleatório em um esquema simples que Azure Search pode indexar.

- **dataChangeDetectionPolicy**: opcional. Consulte a [política de detecção de alterar dados](#DataChangeDetectionPolicy) abaixo.

- **dataDeletionDetectionPolicy**: opcional. Consulte a [política de detecção de exclusão de dados](#DataDeletionDetectionPolicy) abaixo.

Veja abaixo de [corpo da solicitação de exemplo](#CreateDataSourceExample).

###<a id="DataChangeDetectionPolicy"></a>Capturar documentos alterados

A finalidade de uma política de detecção de alteração de dados é identificar com eficiência os itens de dados alterados. Atualmente, é a única diretiva com suporte a `High Water Mark` diretiva usando o `_ts` propriedade de carimbo de hora última modificação fornecida pelo DocumentDB - que é especificado da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Você também precisará adicionar `_ts` na projeção e `WHERE` cláusula para sua consulta. Por exemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capturar documentos excluídos

Quando linhas são excluídas da tabela de origem, você deve excluir essas linhas do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar com eficiência os itens de dados excluídos. Atualmente, é a única diretiva com suporte a `Soft Delete` política (exclusão é marcada com um sinalizador de algum tipo), que é especificada, da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Você precisa incluir a propriedade softDeleteColumnName na cláusula SELECT, se você estiver usando uma projeção personalizada.

###<a id="CreateDataSourceExample"></a>Exemplo de corpo da solicitação

O exemplo a seguir cria uma fonte de dados com um dicas de consulta e política personalizada:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Resposta

Se a fonte de dados foi criada com êxito, você receberá uma resposta HTTP 201 criado.

##<a id="CreateIndex"></a>Etapa 2: Criar um índice

Crie um índice de pesquisa do Azure de destino se você não tenha um. Você pode fazer isso da [Interface de usuário do Azure Portal](../search/search-create-index-portal.md) , ou usando a [API do índice de criar](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Certifique-se de que o esquema de seu índice de destino é compatível com o esquema dos documentos JSON de origem ou a saída de projeção sua consulta personalizada.

>[AZURE.NOTE] Para coleções particionadas, a chave de documento padrão é de DocumentDB `_rid` propriedade, que obtém renomeada para `rid` na pesquisa do Azure. Do Além disso, DocumentDB `_rid` valores contêm caracteres inválidos nas chaves do Azure pesquisa; Portanto, o `_rid` valores são codificação Base64.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura r mapeamento entre os tipos de dados JSON e tipos de dados de pesquisa Azure

| TIPO DE DADOS JSON|   TIPOS DE CAMPO DE ÍNDICE DE DESTINO COMPATÍVEL|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Números que se parecem com números inteiros|Edm.Int32, Edm.Int64, Edm.String|
|Números que se parecem com pontos flutuantes|Edm.Double, Edm.String|
|Cadeia de caracteres|Edm.String|
|Por exemplo, "a", "b", "c" tipos de matrizes de primitivo |Collection(EDM.String)|
|Cadeias de caracteres que parecem datas| Edm.DateTimeOffset, Edm.String|
|GeoJSON objetos por exemplo, {"tipo": "Ponto", "coordenadas": [longo, lat]} | Edm.GeographyPoint |
|Outros objetos JSON|N/D|

###<a id="CreateIndexExample"></a>Exemplo de corpo da solicitação

O exemplo a seguir cria um índice com um campo de id e a descrição:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Resposta

Se o índice foi criado com êxito, você receberá uma resposta HTTP 201 criado.

##<a id="CreateIndexer"></a>Etapa 3: Criar um indexador

Você pode criar um novo indexador dentro de um serviço de pesquisa do Azure usando uma solicitação HTTP POST com os seguintes cabeçalhos.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

O corpo da solicitação contém a definição de indexador, que deve incluir os seguintes campos:

- **nome**: obrigatório. O nome do indexador.

- **dataSourceName**: obrigatório. O nome de uma fonte de dados existente.

- **targetIndexName**: obrigatório. O nome de um índice existente.

- **cronograma**: opcional. Veja abaixo a [agenda de indexação](#IndexingSchedule) .

###<a id="IndexingSchedule"></a>Executando indexadores em um agendamento

Um indexador, opcionalmente, pode especificar um cronograma. Se houver um cronograma, indexador serão executadas periodicamente conforme a programação. Cronograma tem os seguintes atributos:

- **intervalo**: obrigatório. Um valor de duração que especifica um intervalo ou período para indexador é executado. O menor intervalo permitido é 5 minutos; maior é um dia. Ele deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um valor de [duração de ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

- **hora de início**: obrigatório. Um datetime UTC que especifica quando o indexador deve começar em execução.

###<a id="CreateIndexerExample"></a>Exemplo de corpo da solicitação

O exemplo a seguir cria um indexador que copia dados da coleção referenciada pelo `myDocDbDataSource` de fonte de dados para o `mySearchIndex` índice em um cronograma que começa em 1º de janeiro de 2015 UTC e executa por hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Resposta

Se o indexador foi criado com êxito, você receberá uma resposta HTTP 201 criado.

##<a id="RunIndexer"></a>Etapa 4: Executar um indexador

Além de executando periodicamente em um cronograma, um indexador também pode ser chamado sob demanda por meio da seguinte solicitação de HTTP POST:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Resposta

Se o indexador foi chamado com sucesso, você receberá uma resposta HTTP 202 aceitas.

##<a name="GetIndexerStatus"></a>Etapa 5: Obter o status do indexador

Você pode emitir uma solicitação GET HTTP para recuperar o histórico de status e execução atual de um indexador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Resposta

Você verá uma resposta HTTP 200 Okey juntamente com um corpo de resposta que contém informações sobre status de integridade de indexador geral, a última invocação de indexador, bem como o histórico de chamadas de indexador recente (se houver).

A resposta deve ser semelhante ao seguinte:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Histórico de execução contém até 50 mais recentes concluídas execuções, que são classificadas em ordem cronológica inversa (para que a execução mais recente vier primeira na resposta).

##<a name="NextSteps"></a>Próximas etapas

Parabéns! Você aprendeu apenas a integrar o Azure DocumentDB com a pesquisa do Azure usando o indexador para DocumentDB.

 - Para aprender como mais DocumentDB do Azure, consulte a [página de serviço de DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Para saber como mais sobre pesquisa do Azure, consulte a [página de serviço de pesquisa](https://azure.microsoft.com/services/search/).
