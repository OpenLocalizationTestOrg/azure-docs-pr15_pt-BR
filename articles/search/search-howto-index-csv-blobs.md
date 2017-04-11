<properties
pageTitle="Indexação blobs CSV com indexador de pesquisa do Azure do blob | Microsoft Azure"
description="Saiba como indexar blobs CSV com a pesquisa do Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexação blobs CSV com indexador de pesquisa do Azure do blob 

Por padrão, o [indexador de pesquisa do Azure do blob](search-howto-indexing-azure-blob-storage.md) analisa delimitados blobs de texto como um único bloco de texto. No entanto, com blobs contendo dados CSV, geralmente você deseja tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Talvez você queira analisá-lo em 2 documentos, cada um contendo "id", "data de publicação" e "marcas" campos.

Neste artigo, você aprenderá como analisar blobs CSV com um indexador de blob de pesquisa do Azure. 

> [AZURE.IMPORTANT] Esta funcionalidade está no modo de visualização. Ele está disponível somente na API REST usando a versão **2015-02-28-visualização**. Verifique se lembrar, visualizar APIs destinam-se para testar e avaliação e não deve ser usados em ambientes de produção. 

## <a name="setting-up-csv-indexing"></a>Configuração de indexação de CSV

Para indexar CSV blobs, criar ou atualizar uma definição de indexador com a `delimitedText` análise modo:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para obter mais detalhes sobre a API de indexador criar, confira [Criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`indica que a primeira linha (não está em branco) de cada blob contém cabeçalhos.
Se blobs não contém uma linha de cabeçalho inicial, os cabeçalhos deverão ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Atualmente, apenas a codificação de UTF-8 é suporte. Além disso, somente a vírgula `','` caractere é suportado como o delimitador. Se precisar de suporte para outras codificações ou delimitadores, informe-em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Quando você usa o modo de análise de texto delimitado, pesquisa Azure pressupõe que todos os blobs na sua fonte de dados será CSV. Se você precisar oferecer suporte a uma mistura de blobs CSV e não-CSV na mesma fonte de dados, informe-em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Exemplos de solicitação

Colocar isso tudo juntas, aqui estão os exemplos de carga completa. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar a pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para melhorias, por favor, comunique-se em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).