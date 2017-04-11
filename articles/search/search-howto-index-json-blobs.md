<properties
pageTitle="Indexação blobs JSON com indexador de pesquisa do Azure do blob"
description="Indexação blobs JSON com indexador de pesquisa do Azure do blob"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexação blobs JSON com indexador de pesquisa do Azure do blob 

Este artigo mostra como configurar indexador de blob de pesquisa do Azure para extrair o conteúdo estruturado de bolhas que contêm JSON.

## <a name="scenarios"></a>Cenários

Por padrão, o [indexador de pesquisa do Azure do blob](search-howto-indexing-azure-blob-storage.md) analisa blobs JSON como um único bloco de texto. Muitas vezes, você quer preservar a estrutura dos seus documentos JSON. Por exemplo, considerando o documento JSON 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Talvez você queira analisá-lo em um documento de pesquisa do Azure com "texto", "data de publicação" e "marcas" campos.

Como alternativa, quando seu blobs contêm uma **matriz de objetos JSON**, convém cada elemento da matriz se tornar um documento de pesquisa do Azure separado. Por exemplo, dado um blob com este JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Você pode preencher o índice de pesquisa do Azure com 3 documentos separados, cada uma com campos "id" e "texto". 

> [AZURE.IMPORTANT] Esta funcionalidade está no modo de visualização. Ele está disponível somente na API REST usando a versão **2015-02-28-visualização**. Verifique se lembrar, visualizar APIs destinam-se para testar e avaliação e não deve ser usados em ambientes de produção. 

## <a name="setting-up-json-indexing"></a>Configuração de indexação JSON

Para indexar JSON blobs, defina o `parsingMode` parâmetro de configuração para `json` (para cada blob como um único documento de índice) ou `jsonArray` (se sua blobs contêm uma matriz JSON): 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Se necessário, use **os mapeamentos de campo** para selecionar as propriedades do documento JSON de origem usada para popular o índice de pesquisa de destino.  Isso é descrito em detalhes abaixo. 

> [AZURE.IMPORTANT] Quando você usa `json` ou `jsonArray` modo de análise, o Azure pesquisa pressupõe que todos os blobs na sua fonte de dados será JSON. Se você precisar oferecer suporte a uma mistura de blobs JSON e não-JSON na mesma fonte de dados, informe-em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Usando mapeamentos de campo para criar documentos de pesquisa 

Atualmente, Azure Search não pode indexar aleatório documentos JSON diretamente, porque ele dá suporte a tipos de dados apenas primitivos, matrizes de cadeia de caracteres e GeoJSON pontos. No entanto, você pode usar **os mapeamentos de campo** para selecionar partes do seu documento JSON e "tire-los" em campos de nível superior do documento pesquisa. Para saber mais sobre noções básicas de mapeamentos de campo, consulte [mapeamentos de campo do indexador de pesquisa do Azure ponte entre as diferenças entre fontes de dados e índices de pesquisa](search-indexer-field-mappings.md).

Volte ao nosso documento JSON de exemplo: 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo Edm.String, `date` do tipo Edm.DateTimeOffset, e `tags` do tipo Collection(Edm.String). Para mapear sua JSON dentro da forma desejada, use os seguintes mapeamentos de campo: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

Os nomes de campos de origem na mapeamentos são especificados usando a notação [JSON ponteiro](http://tools.ietf.org/html/rfc6901) . Você começar com uma barra para se referir a raiz do seu documento JSON e analise a propriedade desejada (em aleatório nível de aninhamento) usando encaminhar caminho barra separados. 

Você também pode consultar a elementos individuais da matriz usando um índice baseado em zero. Por exemplo, para escolher o primeiro elemento da matriz "marcas" do exemplo acima, use um mapeamento de campo assim:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Se o nome de um campo de origem em um caminho de mapeamento de campo se refere a uma propriedade que não existe no JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos suportá documentos com um esquema diferente (que é um caso de uso comum). Porque não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação de mapeamento do campo. 

Se seus documentos JSON contenham apenas propriedades de nível superior simples, talvez não seja necessário mapeamentos de campo em todos os. Por exemplo, se seu JSON tem esta aparência, as propriedades de nível superior "texto", "data de publicação" e "marcas" serão mapear diretamente para os campos correspondentes no índice de pesquisa: 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>Indexação matrizes JSON aninhadas

O que acontece se você deseja indexar uma matriz de objetos JSON, mas essa matriz é aninhado em algum lugar dentro do documento? Você pode escolher qual propriedade contém a matriz usando o `documentRoot` propriedade de configuração. Por exemplo, se seu blobs ter esta aparência: 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Use esta configuração para indexar a matriz contida na propriedade "level2": 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Exemplos de solicitação

Colocar isso tudo juntas, aqui estão os exemplos de cargas concluída. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar a pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para melhorias, por favor, comunique-se em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).