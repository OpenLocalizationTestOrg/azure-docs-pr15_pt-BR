<properties
pageTitle="Armazenamento de tabela do Microsoft Azure indexação com pesquisa Azure"
description="Saiba como indexar dados armazenados nas tabelas do Azure com a pesquisa do Azure"
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
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>Armazenamento de tabela do Microsoft Azure indexação com pesquisa Azure

Este artigo mostra como usar a pesquisa do Azure para dados de índice armazenados em armazenamento de tabela do Azure. O novo indexador de tabela de pesquisa do Azure torna esse processo rápido e uniforme. 

> [AZURE.IMPORTANT] Atualmente, essa funcionalidade está no modo de visualização. Ele está disponível somente na API REST usando a versão **2015-02-28-visualização** e na versão 2.0-visualização do SDK do .NET. Verifique se lembrar, visualizar APIs destinam-se para testar e avaliação e não deve ser usados em ambientes de produção.

## <a name="setting-up-azure-table-indexing"></a>Configuração de indexação de tabela do Microsoft Azure

Para instalar e configurar um indexador de tabela do Microsoft Azure, você pode usar a API de REST de pesquisa do Azure para criar e gerenciar **fontes de dados** e **indexadores** conforme descrito em [operações de indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). Você também pode usar a [versão 2.0-visualização](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) do SDK do .NET. No futuro, o suporte para a indexação de tabela será adicionada ao Portal do Azure.

Uma fonte de dados especifica os dados que serão indexar, credenciais necessárias para acessar os dados e as políticas que habilitar a pesquisa do Azure com eficiência identificar alterações nos dados (novas, modificadas ou excluídas linhas).

Um indexador lê dados de uma fonte de dados e carrega-lo em um índice de pesquisa de destino.

Para configurar a indexação de tabela:

1. Criar uma fonte de dados
    - Definir o `type` parâmetro para`azuretable`
    - Passe a cadeia de caracteres de conexão de conta de armazenamento como o `credentials.connectionString` parâmetro
    - Especifique o nome de tabela usando o `container.name` parâmetro
    - Opcionalmente, especifique uma consulta usando a `container.query` parâmetro. Sempre que possível, use um filtro em PartitionKey para obter melhor desempenho; qualquer outra consulta resultará em uma verificação de tabela inteira, que pode resultar em baixo desempenho para tabelas grandes.
2. Crie um índice de pesquisa com o esquema que corresponde às colunas da tabela que você deseja indexar. 
3. Crie o indexador conectando sua fonte de dados ao índice de pesquisa.

### <a name="create-data-source"></a>Criar fonte de dados

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre a API de Datasource criar, consulte [Criar fonte de dados](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Criar índice 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Para obter mais informações sobre a API de índice criar, consulte [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Criar indexador 

Finalmente, crie o indexador que faz referência a fonte de dados e o índice de destino. Por exemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para obter mais detalhes sobre a API de indexador criar, confira [Criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

Isso é tudo há para-indexação feliz!

## <a name="dealing-with-different-field-names"></a>Lidando com nomes de campo diferente

Muitas vezes, os nomes de campo no seu índice existente serão diferentes dos nomes de propriedade da tabela. Você pode usar **os mapeamentos de campo** para mapear os nomes de propriedade da tabela para os nomes de campo no índice de pesquisa. Para saber mais sobre os mapeamentos de campo, consulte [mapeamentos de campo do indexador de pesquisa do Azure ponte entre as diferenças entre fontes de dados e índices de pesquisa](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Teclas de documentos de manuseio

Em pesquisa do Azure, a chave de documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento que está sendo adicionado ao índice (na verdade, é o único campo obrigatório).

Como linhas da tabela tem uma chave composta, Azure pesquisa gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave chave e linha partição. Por exemplo, se uma linha do PartitionKey for `PK1` e RowKey é `RK1`, em seguida, `Key` valor do campo será `PK1RK1`. 

> [AZURE.NOTE] O `Key` valor pode conter caracteres inválidos em chaves de documento, como traços. Você pode lidar com caracteres inválidos, usando o `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se você fizer isso, lembre-se também usar codificação de Base64 de URL confiável quando passando chaves de documento API chama como pesquisa.

## <a name="incremental-indexing-and-deletion-detection"></a>Detecção incremental de indexação e exclusão
 
Quando você configura um indexador de tabela para executar em um cronograma, ele reindexes linhas somente novas ou atualizadas, conforme determinado por uma linha `Timestamp` valor. Você não precisa especificar uma política de detecção de alteração – indexação incremental é ativado automaticamente para você. 

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de excluir suave – em vez de excluir uma linha, adicione uma propriedade para indicar que ele é excluído e definir uma política de detecção de exclusão suaves na fonte de dados. Por exemplo, a política mostrada abaixo considerará que uma linha é excluída se ele tiver uma propriedade `IsDeleted` com o valor `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar a pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para melhorias, por favor, comunique-se em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).