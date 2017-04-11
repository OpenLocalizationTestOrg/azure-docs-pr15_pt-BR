<properties 
pageTitle="Operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-Preview) | Visualização de pesquisa Azure API" 
description="Operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-Preview)#

> [AZURE.NOTE] Este artigo descreve indexadores na [API REST de 2015-02-28-visualização](search-api-2015-02-28-preview.md). Esta versão da API adiciona versões de visualização do indexador de armazenamento de Blob do Azure com a extração do documento e indexador de armazenamento de tabela do Azure, além de outras melhorias. A API também suporta disponibilizados indexadores (GA), incluindo indexadores para banco de dados do SQL Azure, SQL Server em VMs do Azure e DocumentDB do Azure.

## <a name="overview"></a>Visão geral ##

Pesquisa Azure pode integrar diretamente com algumas fontes de dados comuns, removendo a necessidade de escrever código para seus dados de índice. Para configurar esse problema, você pode chamar a API de pesquisa do Azure para criar e gerenciar **fontes de dados**e **indexadores** . 

Um **indexador** é um recurso que se conecta a fontes de dados com os índices de pesquisa de destino. Um indexador é usado das seguintes maneiras: 

- Execute uma única cópia dos dados para preencher um índice.
- Sincronize um índice com alterações na fonte de dados em uma agenda. O agendamento é parte da definição do indexador.
- Chame sob demanda para atualizar um índice, conforme necessário. 

Um **indexador** é útil quando você quiser atualizações regulares um índice. Você pode configurar um agendamento embutido como parte de uma definição de indexador, ou executá-lo sob demanda usando [Indexador executar](#RunIndexer). 

Uma **fonte de dados** Especifica que dados precisam ser indexadas, credenciais para acessar os dados e políticas para habilitar a pesquisa do Azure com eficiência identificar alterações nos dados (tais como modificadas ou excluídas linhas em uma tabela de banco de dados). Ele é definido como um recurso independente para que ele pode ser usado por vários indexadores.

As seguintes fontes de dados são suportadas:

- **Banco de dados do SQL Azure** e **SQL Server em VMs Azure**. Para um orientação sobre como alvo, consulte o [artigo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **DocumentDB azure**. Para um orientação sobre como alvo, consulte o [artigo](../documentdb/documentdb-search-indexer.md). 
- **Armazenamento de Blob do azure**, incluindo os seguintes formatos de documento: PDF, Microsoft Office (DOCX/DOC, XSLX/XLS, PPTX/PPT, MSG), HTML, XML, ZIP e texto sem formatação arquivos (incluindo JSON). Para um orientação sobre como alvo, consulte o [artigo](search-howto-indexing-azure-blob-storage.md).
- **Armazenamento de tabela do Microsoft azure**. Para um orientação sobre como alvo, consulte o [artigo](search-howto-indexing-azure-tables.md).
     
Vamos considerar adicionando suporte para fontes de dados adicionais no futuro. Para ajudar a priorizar essas decisões, forneça seus comentários sobre o [Fórum de comentários de pesquisa do Azure](http://feedback.azure.com/forums/263029-azure-search).

Consulte [Limites de serviço](search-limits-quotas-capacity.md) para limites máximos relacionados aos recursos de fonte de dados e indexador.

## <a name="typical-usage-flow"></a>Fluxo de uso típico ##

Você pode criar e gerenciar indexadores e fontes de dados por meio de solicitações HTTP simples (POST, GET, colocar, excluir) em relação a um determinado `data source` ou `indexer` recurso.

Configuração de indexação automática normalmente é um processo de quatro etapas:

1. Identifique a fonte de dados que contém os dados que precisa ser indexadas. Tenha em mente que Azure pesquisa pode não oferece suporte para todos os tipos de dados presentes na sua fonte de dados. Consulte [tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para a lista.

2. Crie um índice de pesquisa do Azure cujo esquema é compatível com sua fonte de dados.
  
3. Crie uma fonte de dados de pesquisa do Azure conforme descrito em [Criar fonte de dados](#CreateDataSource).
  
4. Crie um indexador de pesquisa do Azure como descrito [Indexador criar](#CreateIndexer).

Você deve planejar a criação de um indexador para cada combinação de origem de índice e dados de destino. Você pode ter vários indexadores escrevendo para o mesmo índice, e você pode reutilizar a mesma fonte de dados para vários indexadores. Entretanto, um indexador só pode consumir uma fonte de dados por vez e pode gravar apenas em um único índice. 

Depois de criar um indexador, você pode recuperar seu status de execução usando a operação [Get indexador Status](#GetIndexerStatus) . Você também pode executar um indexador a qualquer momento (em vez de ou além executando periodicamente em um cronograma) usando a operação de [Indexador executar](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Criar fonte de dados ##

Em pesquisa do Azure, uma fonte de dados é usada com indexadores, fornecendo as informações de conexão para atualização de dados ad-hoc ou agendado de um índice de destino. Você pode criar uma nova fonte de dados dentro de um serviço de pesquisa do Azure usando uma solicitação HTTP POST.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar a COLOCAÇÃO e especifique o nome da fonte de dados no URI. Se a fonte de dados não existir, ele será criado.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Observação**: O número máximo de fontes de dados permitido varia por nível de preços. O serviço gratuito permite até 3 fontes de dados. Serviço padrão permite 50 fontes de dados. Consulte [Limites de serviço](search-limits-quotas-capacity.md) para obter detalhes.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação de **Criar fonte de dados** pode ser construída usando o método de uma POSTAGEM ou a COLOCAÇÃO. Ao usar POSTAGEM, você deve fornecer um nome de fonte de dados no corpo da solicitação junto com a definição de fonte de dados. Com colocar, o nome é parte da URL. Se a fonte de dados não existir, ele será criado. Se ele já existir, ele será atualizado para a nova definição. 

O nome da fonte de dados deve estar em letras minúsculas, comece com uma letra ou número, têm sem barras ou pontos e ter menos de 128 caracteres. Após iniciar o nome da fonte de dados com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não são consecutivos. Consulte [regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obter detalhes.

O `api-version` é necessária. A versão atual é `2015-02-28`.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais. 

- `Content-Type`: Necessários. Defina como`application/json`
- `api-key`: Necessários. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Criar fonte de dados** deve incluir uma `api-key` cabeçalho definido como sua chave de administrador (em vez de uma chave de consulta). 
 
Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no [Portal do Azure](https://portal.azure.com/). Consulte [criar um serviço de pesquisa no portal](search-create-service-portal.md) para a navegação de página Ajuda.

<a name="CreateDataSourceRequestSyntax"></a>
**Sintaxe de solicitação de corpo**

O corpo da solicitação contém uma definição de fonte de dados, que inclui o tipo de fonte de dados, credenciais ler os dados, bem como um opcional de dados detecção de alterações e políticas de detecção de exclusão de dados que são usadas para identificar com eficiência alterados ou excluídos dados na fonte de dados quando usada com um indexador periodicamente agendado. 

A sintaxe para estruturar a carga de solicitação é da seguinte maneira. Uma solicitação de exemplo é fornecida adiante neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Solicitação contém as seguintes propriedades: 

- `name`: Necessários. O nome da fonte de dados. Um nome de fonte de dados deve apenas contêm letras minúsculas, dígitos ou traços, não é possível iniciar ou terminar com traços e está limitado a 128 caracteres.
- `description`: Descrição opcional. 
- `type`: Necessários. Deve ser um dos tipos de fonte de dados com suporte:
    - `azuresql`-Banco de dados do SQL azure ou SQL Server em VMs Azure
    - `documentdb`-DocumentDB azure
    - `azureblob`-Armazenamento de Blob azure
    - `azuretable`-Armazenamento de tabela azure
- `credentials`:
    - Necessário `connectionString` propriedade especifica a cadeia de conexão da fonte de dados. O formato da cadeia de caracteres de conexão depende do tipo de fonte de dados: 
        - Para SQL Azure, essa é a cadeia de conexão do SQL Server normal. Se você estiver usando o portal do Azure para recuperar a cadeia de conexão, use o `ADO.NET connection string` opção.
        - Para DocumentDB, a cadeia de conexão deve estar no seguinte formato: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Todos os valores são necessários. Você pode encontrá-las no [portal do Azure](https://portal.azure.com/).  
        - Para Azure Blob e o armazenamento de tabela, essa é a cadeia de conexão de conta de armazenamento. O formato é descrito [aqui](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). Protocolo de ponto de extremidade HTTPS é necessário.  
- `container`, necessários: especifica os dados de índice usando o `name` e `query` propriedades: 
    - `name`Necessário:
        - SQL Azure: Especifica a tabela ou modo de exibição. Você pode usar nomes qualificados esquema, tais como `[dbo].[mytable]`.
        - DocumentDB: Especifica a coleção. 
        - Armazenamento de Blob do Azure: Especifica o contêiner de armazenamento.
        - Armazenamento de tabela do Azure: Especifica o nome da tabela. 
    - `query`, opcionais:
        - DocumentDB: permite que você especifique uma consulta que nivela um layout de documento JSON aleatório em um esquema simples que Azure Search pode indexar.  
        - Armazenamento de Blob do Azure: permite que você especifique uma pasta virtual dentro do contêiner de blob. Por exemplo, para o caminho de blob `mycontainer/documents/blob.pdf`, `documents` pode ser usado como a pasta virtual.
        - Armazenamento de tabela do Azure: permite que você especifique uma consulta que filtra o conjunto de linhas a serem importados.
        - SQL Azure: consulta não é suportada. Se você precisar dessa funcionalidade, votar para [essa sugestão](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- Opcional `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` propriedades são descritas abaixo.

<a name="DataChangeDetectionPolicies"></a>
**Políticas de detecção de alteração de dados**

A finalidade de uma política de detecção de alteração de dados é identificar com eficiência os itens de dados alterados. Políticas de suportados variam com base no tipo de fonte de dados. Seções a seguir descrevem cada política. 

***Diretiva de detecção de alteração de marca d'água*** 

Use esta política quando sua fonte de dados contém uma coluna ou propriedade que atenda aos seguintes critérios:
 
- Todas as inserções especificar um valor para a coluna. 
- Todas as atualizações para um item também alteram o valor da coluna. 
- O valor desta coluna aumenta com cada alteração.
- Consultas que usam uma cláusula de filtro semelhante à seguinte `WHERE [High Water Mark Column] > [Current High Water Mark Value]` podem ser executadas com eficiência.

Por exemplo, ao usar fontes de dados SQL Azure, um indexado `rowversion` coluna é o candidato ideal para uso com a política de marca d'água alta. 

Essa política pode ser especificada da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Ao usar fontes de dados de DocumentDB, você deve usar o `_ts` propriedade fornecida pelo DocumentDB. 

> [AZURE.NOTE] Ao usar fontes de dados do Azure Blob, pesquisa Azure automaticamente usa uma marca d'água alta alterar diretiva de detecção com base em última modificação carimbo de hora de um blob; Você não precisa especificar tal diretiva por conta própria.   

***Diretiva de detecção de alteração de integrado do SQL***

Se seu banco de dados SQL tem suporte para [o controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos o uso de SQL integrado alterar controle de política. Essa política permite que o controle de alterações mais eficiente e permite a pesquisa do Azure para identificar linhas excluídas sem precisar tem uma coluna de explícita "excluir suave" no seu esquema.

Controle de alterações integrada é suportada começando com as seguintes versões de banco de dados do SQL Server: 
- SQL Server 2008 R2, se você estiver usando o SQL Server em VMs do Azure.
- Azure SQL banco de dados V12, se você estiver usando o Azure SQL Database.  

Quando usar política de controle de alterações integrado do SQL, não especificar uma política de detecção de exclusão de dados separado - esta política possui suporte interno para identificar linhas excluídas. 

Essa política só pode ser usada com tabelas; ele não pode ser usado com modos de exibição. Você precisa habilitar o controle de alterações para a tabela que você está usando antes de poder usar essa política. Para obter instruções, consulte [Habilitar e desabilitar o controle de alterações](https://msdn.microsoft.com/library/bb964713.aspx) .    
 
Quando estruturar a solicitação de **Criar fonte de dados** , diretiva de controle de alteração de SQL integrado pode ser especificado da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Políticas de detecção de exclusão de dados**

A finalidade de uma política de detecção de exclusão de dados é identificar com eficiência os itens de dados excluídos. Atualmente, é a única diretiva com suporte a `Soft Delete` diretiva, que permite a identificação de itens excluídos com base no valor de uma `soft delete` coluna ou propriedade na fonte de dados. Essa política pode ser especificada da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**Observação:** Somente colunas com a cadeia de caracteres, inteiro ou valores booleanos são suportadas. O valor usado como `softDeleteMarkerValue` deve ser uma cadeia de caracteres, mesmo que a coluna correspondente detém inteiros ou boolianos. Por exemplo, se o valor que aparece em sua fonte de dados for 1, use `"1"` como o `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Exemplos de corpo da solicitação**

Se você pretende usar a fonte de dados com um indexador que é executado em um cronograma, este exemplo mostra como especificar políticas de detecção de alteração e exclusão: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Se você pretende usar a fonte de dados única cópia dos dados, as políticas podem ser omitidas:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Resposta**

Para uma solicitação bem-sucedida: 201 criado. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Atualizar fonte de dados ##

Você pode atualizar uma fonte de dados existente usando uma solicitação de HTTP colocar. Especifique o nome da fonte de dados para atualizar a solicitação URI:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

O `api-version` é necessária. A versão atual é `2015-02-28`. [Controle de versão de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e mais informações sobre versões alternativas.

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Solicitação**

A sintaxe de corpo de solicitação é o mesmo usado para [solicitações de criar fonte de dados](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Algumas propriedades não podem ser atualizadas em uma fonte de dados existente. Por exemplo, você não pode alterar o tipo de uma fonte de dados existente.  

> [AZURE.NOTE] Se você não quiser alterar a cadeia de conexão para uma fonte de dados existente, você pode especificar o literal `<unchanged>` para a cadeia de conexão. Isso é útil em situações nas quais você precisa atualizar um dados de origem mas não tem acesso conveniente para a cadeia de conexão pois é dados confidenciais.

**Resposta**

Para uma solicitação bem-sucedida: 201 criado se uma nova fonte de dados foi criado e 204 sem conteúdo se uma fonte de dados existente foi atualizada.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Fontes de dados de lista ##

A operação de **Fontes de dados de lista** retorna uma lista de fontes de dados no seu serviço de pesquisa do Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessária. A versão atual é `2015-02-28`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Para uma solicitação bem-sucedida: 200 Okey.

Aqui está um corpo de resposta de exemplo:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Observe que você pode filtrar a resposta para baixo até apenas as propriedades que você está interessado. Por exemplo, se desejar somente uma lista de nomes de fonte de dados, usar o OData `$select` opção de consulta:

    GET /datasources?api-version=205-02-28&$select=name

Nesse caso, a resposta do exemplo acima terá a seguinte aparência: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Essa é uma técnica útil para salvar a largura de banda se você tiver muitas fontes de dados no seu serviço de pesquisa.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Obter a fonte de dados ##

A operação de **Fonte de dados obter** obtém a definição de fonte de dados da pesquisa do Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessária. A versão atual é `2015-02-28`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

A resposta é semelhante aos exemplos nas [solicitações de exemplo de criar fonte de dados](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Não defina o `Accept` cabeçalho de solicitação para `application/json;odata.metadata=none` quando chamar essa API como isso fará com que `@odata.type` atributo a serem omitidos da resposta e você não conseguirá diferenciar alteração de dados e políticas de detecção de exclusão de dados de diferentes tipos. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Excluir fonte de dados ##

A operação de **Fonte de dados excluir** remove uma fonte de dados do seu serviço de pesquisa do Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Se qualquer indexadores referência a fonte de dados que você está excluindo, ainda continuará a operação de exclusão. No entanto, esses indexadores farão a transição para o estado de erro durante sua próxima execução.  

O `api-version` é necessária. A versão atual é `2015-02-28`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 sem conteúdo é retornado por uma resposta bem-sucedido.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Criar indexador ##

Você pode criar um novo indexador dentro de um serviço de pesquisa do Azure usando uma solicitação HTTP POST.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar a COLOCAÇÃO e especifique o nome da fonte de dados no URI. Se a fonte de dados não existir, ele será criado.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] O número máximo de indexadores permitida varia por nível de preços. O serviço gratuito permite até 3 indexadores. Serviço padrão permite 50 indexadores. Consulte [Limites de serviço](search-limits-quotas-capacity.md) para obter detalhes.

O `api-version` é necessária. A versão atual é `2015-02-28`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.


<a name="CreateIndexerRequestSyntax"></a>
**Sintaxe de solicitação de corpo**

O corpo da solicitação contém uma definição de indexador, que especifica a fonte de dados e o índice de destino de indexação, bem como a agenda de indexação opcional e parâmetros. 


A sintaxe para estruturar a carga de solicitação é da seguinte maneira. Uma solicitação de exemplo é fornecida adiante neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Cronograma de indexador**

Um indexador, opcionalmente, pode especificar um cronograma. Se houver um cronograma, indexador serão executadas periodicamente conforme a programação. Cronograma tem os seguintes atributos:

- `interval`: Necessários. Um valor de duração que especifica um intervalo ou período para indexador é executado. O menor intervalo permitido é 5 minutos; maior é um dia. Ele deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um valor de [duração de ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `"P[nD][T[nH][nM]]"`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas. 

- `startTime`: Necessários. Um datetime UTC quando o indexador deve iniciar a execução. 

**Parâmetros de indexador**

Um indexador opcionalmente pode especificar vários parâmetros que afetam seu comportamento. Todos os parâmetros são opcionais.  

- `maxFailedItems`: O número de itens que pode falhar ao ser indexadas antes de executar um indexador é considerado uma falha. Padrão é 0. Informações sobre os itens com falha são retornadas pela operação [Get indexador Status](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: O número de itens que pode falhar ao ser indexadas em cada lote antes de executar um indexador é considerado uma falha. Padrão é 0.

- `base64EncodeKeys`: Especifica estando ou não chaves de documento serão codificada base 64. Pesquisa Azure impõe restrições nos caracteres que podem estar presentes em uma chave do documento. No entanto, os valores nos dados de origem podem conter caracteres inválidos. Se for necessário indexar tais valores como chaves de documento, este sinalizador pode ser definido como true. O padrão é `false`.

- `batchSize`: Especifica o número de itens que são lidos da fonte de dados e indexados como um único lote para melhorar o desempenho. O padrão depende do tipo de fonte de dados: é 1000 para SQL Azure e DocumentDB e 10 para armazenamento de Blob do Azure.

**Mapeamentos de campo**

Você pode usar os mapeamentos de campo para mapear um nome de campo na fonte de dados para outro nome de campo no índice de destino. Por exemplo, considere uma tabela de origem com um campo `_id`. Pesquisa Azure não permite que um nome de campo que começam com um sublinhado, para que o campo deve ser renomeado. Isso pode ser feito usando o `fieldMappings` propriedade do indexador da seguinte forma: 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Você pode especificar vários mapeamentos de campo: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Nomes de campos de origem e destino diferenciam maiusculas de minúsculas.

<a name="FieldMappingFunctions"></a>
***Funções de mapeamento de campo***

Mapeamentos de campos também podem ser usados para transformar valores de campos de origem usando *funções de mapeamento*.

Apenas uma função é suportada atualmente: `jsonArrayToStringCollection`. Ele analisa um campo que contém uma cadeia de caracteres formatada como uma matriz JSON em um campo Collection(Edm.String) no índice de destino. Ele é destinado com indexador do SQL Azure em particular, como SQL não tem um tipo de dados nativo conjunto. Ela pode ser usada da seguinte maneira: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por exemplo, se o campo de origem contiver a cadeia de caracteres `["red", "white", "blue"]`, em seguida, o campo de destino do tipo `Collection(Edm.String)` será preenchida com os três valores `"red"`, `"white"` e `"blue"`.

Observe que o `targetFieldName` propriedade é opcional; Se a esquerda, o `sourceFieldName` valor é usado. 

<a name="CreateIndexerRequestExamples"></a>
**Exemplos de corpo da solicitação**

O exemplo a seguir cria um indexador que copia dados da tabela referenciada pelo `ordersds` de fonte de dados para o `orders` índice em um cronograma que começa em 1º de janeiro de 2015 UTC e executa por hora. Cada invocação de indexador será bem-sucedida se não mais do que 5 itens falharem ao ser indexadas em cada lote, e não mais do que 10 itens falharem ao ser indexadas no total. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Resposta**

201 criado para uma solicitação de bem-sucedido.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Indexador de atualização ##

Você pode atualizar um indexador existente usando uma solicitação de HTTP colocar. Especifique o nome do indexador atualizar na solicitação URI:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

O `api-version` é necessária. A versão atual é `2015-02-28`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Solicitação**

A sintaxe de corpo de solicitação é a mesma [indexador criar solicitações](#CreateIndexerRequestSyntax).

**Resposta**

Para uma solicitação bem-sucedida: 201 criado se um novo indexador foi criada e 204 sem conteúdo se um indexador existente foi atualizado.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Indexadores de lista ##

A operação de **Lista indexadores** retorna a lista de indexadores no seu serviço de pesquisa do Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. [Controle de versão de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e mais informações sobre versões alternativas.

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Para uma solicitação bem-sucedida: 200 Okey.

Aqui está um corpo de resposta de exemplo:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Observe que você pode filtrar a resposta para baixo até apenas as propriedades que você está interessado. Por exemplo, se desejar somente uma lista de nomes de indexador, use o OData `$select` opção de consulta:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Nesse caso, a resposta do exemplo acima terá a seguinte aparência: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Essa é uma técnica útil para salvar a largura de banda se você tiver muitas indexadores no seu serviço de pesquisa.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Obter indexador ##

A operação de **Obter indexador** obtém a definição de indexador de pesquisa do Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

A resposta é semelhante aos exemplos em [criar indexador solicitações de exemplo](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Excluir indexador ##

A operação de **Indexador excluir** remove um indexador do seu serviço de pesquisa do Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando um indexador é excluído, as execuções indexador em andamento no momento serão executadas até a conclusão, mas nenhuma execuções adicionais serão agendadas. Tenta usar um indexador inexistente resultará em código de status HTTP 404 não encontrado. 
 
O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 sem conteúdo é retornado por uma resposta bem-sucedido.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Executar indexador ##

Além de executando periodicamente em um cronograma, um indexador também pode ser chamado sob demanda por meio da operação do **Indexador executar** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 202 aceitos é retornado por uma resposta bem-sucedido.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Obter o Status do indexador ##

A operação **Get Status indexador** recupera o histórico de status e execução atual de um indexador: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 Okey por uma resposta bem-sucedido.

Corpo da resposta contém informações sobre status de integridade de indexador geral, a última invocação de indexador, bem como o histórico de chamadas de indexador recente (se houver). 

Um corpo de resposta de amostra tem esta aparência: 

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

**Status do indexador**

Status do indexador pode ser um dos seguintes valores:

- `running`indica que o indexador está sendo executado normalmente. Observe que algumas das execuções indexador ainda podem estar falhando, portanto, é uma boa ideia verificar o `lastResult` propriedade também. 

- `error`indica que o indexador encontrou um erro que não pode ser corrigido sem a intervenção. Por exemplo, as credenciais de fonte de dados podem ter expirado ou o esquema de fonte de dados ou o índice de destino foi alterado em uma quebra de maneira. 

**Resultado da execução de indexador**

Um resultado de execução indexador contém informações sobre a execução de uma única indexador. O resultado mais recente é reproduzido como o `lastResult` propriedade do status indexador. Outros recentes, se estiverem presentes, são retornados resultados como o `executionHistory` propriedade do status indexador. 

Resultado da execução de indexador contém as seguintes propriedades: 

- `status`: o status de uma execução. Ver o [Status de execução de indexador](#IndexerExecutionStatus) abaixo para obter detalhes. 

- `errorMessage`: mensagem de erro para uma falha na execução. 

- `startTime`: a hora no UTC quando essa execução iniciado.

- `endTime`: a hora no UTC quando essa execução encerrada. Este valor não está definido se a execução ainda está em andamento.

- `errors`: uma matriz de erros de nível de item, se houver. Cada entrada contém uma chave de documento (`key` propriedade) e uma mensagem de erro (`errorMessage` propriedade). 

- `itemsProcessed`: o número de dados fonte itens (por exemplo, linhas de tabela) que o indexador tentou indexar durante essa execução. 

- `itemsFailed`: o número de itens que falhou durante a essa execução.  
 
- `initialTrackingState`: sempre `null` para a primeira execução do indexador, ou se os dados alterados política de rastreamento não está habilitado na fonte de dados usada. Se essa diretiva estiver habilitada, em execuções subsequentes esse valor indica a primeira valor processada por essa execução de controle de alterações (menor). 

- `finalTrackingState`: sempre `null` se os dados política de controle de alterações não está habilitado na fonte de dados usada. Caso contrário, indica a valor processado com êxito por essa execução de controle de alterações (mais alta) mais recente. 

<a name="IndexerExecutionStatus"></a>
**Status de execução de indexador**

Status de execução de indexador captura o status de uma execução de indexador único. Ele pode ter os seguintes valores:

- `success`indica que a execução de indexador foi concluída com êxito.

- `inProgress`indica que a execução de indexador está em andamento. 

- `transientFailure`indica que um indexador Falha na execução. Consulte `errorMessage` propriedade para obter detalhes. A falha pode ou não pode exigir a intervenção corrigir - por exemplo, corrigir uma incompatibilidade de esquema entre a fonte de dados e o índice de destino requer ação do usuário, enquanto um tempo de inatividade de fonte de dados temporários não. Chamadas de indexador continuará por programação, se houver. 

- `persistentFailure`indica que o indexador falhou em uma forma que requer a intervenção. Parar de execuções indexador agendada. Depois de resolver o problema, use redefinir indexador API para reiniciar as execuções agendadas. 

- `reset`indica que o indexador foi redefinido por uma chamada para redefinir a API do indexador (veja abaixo). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Redefinir indexador ##

A operação de **Redefinir indexador** redefine estado associado indexador de controle de alterações. Isso permite disparar a reindexação de zero (por exemplo, se seu esquema de fonte de dados tiver sido alterado), ou para alterar a política de detecção de alteração de dados de uma fonte de dados associada com o indexador.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessária. A versão de visualização é `2015-02-28-Preview`. 

O `api-key` deve ser uma chave de administrador (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 sem conteúdo para uma resposta bem-sucedida.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados de pesquisa Azure e tipos de dados SQL ##

<table style="font-size:12">
<tr>
<td>Tipo de dados SQL</td>  
<td>Os tipos de campo permitidos índice de destino</td>
<td>Anotações</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>flutuação real,</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, dinheiro<br>decimal<br>numérico
</td>
<td>Edm.String</td>
<td>Pesquisa Azure não oferece suporte para converter tipos de casas decimais em Edm.Double porque isso faria perder precisão
</td>
</tr>
<tr>
<td>CARACT, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(EDM.String)</td>
<td>Consulte [Funções de mapeamento de campo](#FieldMappingFunctions) neste documento para obter detalhes sobre como transformar uma coluna de cadeia de caracteres em um Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, data, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Geografia</td>
<td>Edm.GeographyPoint</td>
<td>Há suporte para apenas geografia instâncias do tipo POINT com 4326 SRID (que é o padrão)</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas eles podem ser usados para controle de alterações</td>
</tr>
<tr>
<td>tempo, período de tempo<br>binário, varbinary, imagem,<br>XML, geometria, tipos CLR</td>
<td>N/D</td>
<td>Sem suporte</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados JSON e tipos de dados de pesquisa Azure ##

<table style="font-size:12">
<tr>
<td>Tipo de dados JSON</td> 
<td>Os tipos de campo permitidos índice de destino</td>
<td>Anotações</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números integrais</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números de ponto flutuante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>cadeia de caracteres</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>tipos de matrizes de primitivo, por exemplo, ["a", "b", "c"]</td>
<td>Collection(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Cadeias de caracteres que parecem datas</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objetos de ponto de GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Pontos de GeoJSON são objetos JSON no seguinte formato: {"tipo": "Ponto", "coordenadas": [longo, lat]} </td>
</tr>
<tr>
<td>Outros objetos JSON</td>
<td>N/D</td>
<td>Não suportado; Pesquisa Azure atualmente suporta somente tipos primitivos e coleções de cadeia de caracteres</td>
</tr>
</table>