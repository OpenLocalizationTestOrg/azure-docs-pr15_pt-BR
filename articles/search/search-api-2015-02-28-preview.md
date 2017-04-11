<properties
   pageTitle="Azure serviço REST API Versão 2015-02-28-visualização pesquisa | Microsoft Azure | Visualização de pesquisa Azure API"
   description="Azure serviço REST API Versão 2015-02-28-visualização pesquisa inclui recursos experimentais como pesquisas analisadores de linguagem Natural e moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API do resto de serviço de pesquisa Azure: Versão 2015-02-28-visualização

Este artigo é a documentação de referência para `api-version=2015-02-28-Preview`. Essa visualização estende a versão atual estará disponível, [versão da api = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), fornecendo os seguintes recursos experimentais:

- `moreLikeThis`consulta parâmetro nos [Documentos de pesquisa](#SearchDocs) API. Ele encontra outros documentos relevantes para outro documento específico.

Algumas partes adicionais do `2015-02-28-Preview` API REST documentadas separadamente. Eles incluem:

- [Perfis de pontuação](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexadores](search-api-indexers-2015-02-28-preview.md)

Serviço de pesquisa do Azure está disponível em várias versões. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.

## <a name="apis-in-this-document"></a>APIs neste documento

Azure API do serviço de pesquisa oferece suporte a duas sintaxes de URL para operações de API: simples e OData (consulte [suporte para OData (API de pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obter detalhes). A lista a seguir mostra a sintaxe simples.

[Criar índice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Atualizar índice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obter índice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Índices de listagem](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obter estatísticas de índice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Analisador de teste](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Excluir um índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Adicionar, excluir e atualizar dados em um índice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Procurar documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Documento de pesquisa](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contagem de documentos](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Sugestões](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Operações de índice

Você pode criar e gerenciar índices em um serviço de pesquisa do Azure via simples solicitações HTTP (POST, GET, colocar, excluir) em relação a um recurso de índice fornecido. Para criar um índice, primeiro você posta um documento JSON que descreve o esquema de índice. O esquema define os campos de índice, seus tipos de dados e como eles podem ser usados (por exemplo, em pesquisas de texto completo, filtros, classificação ou faceting). Ele também define os perfis de pontuação, suggesters e outros atributos para configurar o comportamento do índice.

O exemplo a seguir fornece uma ilustração de um esquema usada para procurar nas informações de hotel com o campo de descrição definido em dois idiomas. Observe como atributos controlam como o campo é usado. Por exemplo o `hotelId` é usado como a chave de documento (`"key": true`) e é excluído do pesquisas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Depois de criar o índice, você carregará documentos que preencher o índice. Consulte [Adicionar ou atualizar documentos](#AddOrUpdateDocuments) para a próxima etapa.

Para um vídeo de Introdução para indexação na pesquisa do Azure, consulte o [canal 9 nuvem cobrir episódio na pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Criar índice

Um índice é o principal meio organizando e pesquisando documentos na pesquisa do Azure, semelhante a como uma tabela organiza os registros em um banco de dados. Cada índice tem um conjunto de documentos que todos estejam em conformidade com o esquema de índice (nomes de campos, tipos de dados e propriedades), mas os índices também especificar adicionais construções (suggesters, pontuação perfis e opções de CORS) que definem outros comportamentos de pesquisa.

Você pode criar um novo índice dentro de um serviço de pesquisa do Azure usando uma solicitação HTTP POST ou colocar. O corpo da solicitação é um esquema JSON que especifica as informações de índice e configuração.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar a COLOCAÇÃO e especifique o nome de índice no URI. Se o índice não existir, ele será criado.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Criar um índice determina a estrutura dos documentos armazenados e usados em operações de pesquisa. Preencher o índice é uma operação separada. Nesta etapa, você pode usar um [indexador](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponível para fontes de dados com suporte) ou uma operação de [Adicionar, atualizar ou excluir documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) . O índice invertido é gerado quando os documentos são lançados.

**Observação**: O número máximo de índices permitida varia por nível de preços. O serviço gratuito permite até 3 índices. Serviço padrão permite 50 índices por serviço de pesquisa. Para obter detalhes, consulte [limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) .

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação de **Create Index** pode ser construída usando o método de uma POSTAGEM ou a COLOCAÇÃO. Ao usar POSTAGEM, você deve fornecer um nome de índice no corpo da solicitação junto com a definição de esquema de índice. Com colocar, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se ele já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, comece com uma letra ou número, têm sem barras ou pontos e ter menos de 128 caracteres. Após iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não são consecutivos.

O `api-version` é necessária. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter uma lista de versões disponíveis.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: Necessários. Defina como`application/json`
- `api-key`: Necessários. O `api-key` é usado para
- autentica a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Create Index** deve incluir um `api-key` cabeçalho definido como sua chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

<a name="RequestData"></a>
**Sintaxe de solicitação de corpo**

O corpo da solicitação contém uma definição de esquema, que inclui a lista de campos de dados dentro de documentos que serão inseridos deste índice, tipos de dados, atributos, bem como uma lista opcional de pontuação perfis que são usados para pontuação documentos correspondentes ao tempo de consulta.

Observe que, para uma solicitação de POSTAGEM, você deverá especificar o nome do índice no corpo da solicitação.

Pode haver apenas um campo de chave no índice. Ele deve ser um campo de cadeia de caracteres. Este campo representa o identificador exclusivo para cada documento armazenado no índice.

As partes principais de um índice incluem o seguinte:

- `name`
- `fields`que serão inseridas deste índice, incluindo o nome, tipo de dados e propriedades que definem ações permitidas nesse campo.
- `suggesters`usado para as consultas com previsão de escrita ou preenchimento automático.
- `scoringProfiles`usado para pontuação de pesquisa personalizada de classificação. Consulte [Adicionar pontuação perfis](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` usado para definir como seus documentos/consultas são divididas em tokens indexáveis pesquisável. Consulte [análise na pesquisa do Azure](https://aka.ms//azsanalysis) para obter detalhes.
- `defaultScoringProfile`usado para substituir o padrão comportamentos de pontuação.
- `corsOptions`para permitir consultas entre origens seu índice.

A sintaxe para estruturar a carga de solicitação é da seguinte maneira. Uma solicitação de exemplo é fornecida adiante neste tópico.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Atributos de índice**

Os seguintes atributos podem ser definidos ao criar um índice. Para obter detalhes sobre os perfis de pontuação e pontuação, consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Define o nome do campo.

`type`-Define o tipo de dados para o campo. Consulte [Tipos de dados com suporte](#DataTypes) para uma lista de tipos compatíveis.

`searchable`-Marca o campo como texto completo capaz de pesquisa. Isso significa que ele passará análise como a quebra automática de linha durante a indexação. Se você definir um `searchable` campo a um valor, como "chover dia", internamente ele será dividido nos tokens individuais "chover" e "dia". Isso permite pesquisas de texto completo para esses termos. Campos de tipo `Edm.String` ou `Collection(Edm.String)` são `searchable` por padrão. Campos de outros tipos não podem ser `searchable`.

  - **Observação**: `searchable` campos consumam espaço extra no seu índice, desde que a pesquisa do Azure armazenará uma versão editáveis adicional do valor do campo para pesquisas de texto completo. Se você deseja economizar espaço no seu índice e não é necessário um campo a ser incluído nas pesquisas, defina `searchable` para `false`.

`filterable`-Permite o campo a ser referenciada em `$filter` consultas. `filterable`difere `searchable` em como cadeias de caracteres são tratadas. Campos de tipo `Edm.String` ou `Collection(Edm.String)` que são `filterable` não são submetidos a quebra, portanto comparações são para somente correspondências exatas. Por exemplo, se você definir tal campo `f` para "chover dia", `$filter=f eq 'sunny'` não encontrará nenhuma correspondência, mas `$filter=f eq 'sunny day'` será. Todos os campos são `filterable` por padrão.

`sortable`-Por padrão o sistema classifica os resultados por pontuação, mas em experiências muitos usuários desejará classificar por campos nos documentos. Campos de tipo `Collection(Edm.String)` não podem ser `sortable`. Todos os outros campos são `sortable` por padrão.

`facetable`-Normalmente usado em uma apresentação de resultados de pesquisa que inclui a contagem de visitas por categoria (por exemplo, pesquisar câmeras digitais e consulte visitas por marca, pela megapixels, pelo preço, etc.). Esta opção não pode ser usada com campos tipo `Edm.GeographyPoint`. Todos os outros campos são `facetable` por padrão.

  - **Observação**: campos do tipo `Edm.String` que são `filterable`, `sortable`, ou `facetable` pode ter no máximo 32 KB de comprimento. Isso ocorre porque esses campos são tratados como um termo de pesquisa simples, e o comprimento máximo de um termo na pesquisa do Azure é 32KB. Se você precisar armazenar mais texto do que isso em um campo de única cadeia de caracteres, você precisará definir explicitamente `filterable`, `sortable`, e `facetable` para `false` em sua definição de índice.

  - **Observação**: se um campo tem nenhum dos atributos acima definidos como `true` (`searchable`, `filterable`, `sortable`, ou`facetable`) no campo efetivamente é excluído do índice invertido. Essa opção é útil para campos que não são usados em consultas, mas são necessários nos resultados da pesquisa. Excluir esses campos do índice melhora o desempenho.

`key`-Marca o campo como contendo identificadores exclusivos para documentos no índice. Exatamente um campo deve ser escolhido como o `key` campo e ele devem ser do tipo `Edm.String`. Campos de chave podem ser usados para procurar documentos diretamente por meio da [API de pesquisa](#LookupAPI).

`retrievable`-Define se o campo pode ser retornado em um resultado de pesquisa.  Isso é útil quando você deseja usar um campo (por exemplo, margem) como um filtro, classificação ou mecanismo de pontuação, mas não quero que o campo fique visível para o usuário final. Esse atributo deve ser `true` para `key` campos.

`analyzer`-Define o nome da análise de usar para o campo na hora de pesquisa e indexação. Para o conjunto permitido de valores consulte [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção pode ser usada somente com `searchable` campos e ele não podem ser definidos juntamente com um `searchAnalyzer` ou `indexAnalyzer`.  Assim que o analisador é escolhido, ele não pode ser alterado para o campo.

`searchAnalyzer`-Define o nome da análise de usados em tempo de pesquisa do campo. Para o conjunto permitido de valores consulte [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção pode ser usada somente com `searchable` campos. Ele deve ser definido juntamente com `indexAnalyzer` e não pode ser definido juntamente com a `analyzer` opção. Esse analisador pode ser atualizado em um campo existente.

`indexAnalyzer`-Define o nome da análise de usados em tempo de indexação para o campo. Para o conjunto permitido de valores consulte [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção pode ser usada somente com `searchable` campos. Ele deve ser definido juntamente com `searchAnalyzer` e não pode ser definido juntamente com a `analyzer` opção. Assim que o analisador é escolhido, ele não pode ser alterado para o campo.

`suggesters`-Define o modo de pesquisa e os campos que são a origem do conteúdo para obter sugestões. Consulte [Suggesters](#Suggesters) para obter detalhes.

`scoringProfiles`-Define a pontuação comportamentos personalizados que permitem que você influenciar os itens que aparecerão maiores nos resultados da pesquisa. Perfis de pontuação são compostos de funções e espessuras de campo. Consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter mais informações sobre os atributos usados em um perfil de pontuação.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Suporte a idiomas**

Campos de pesquisa são submetidos análise mais frequentemente envolve a quebra, normalização de texto e a filtragem de termos. Por padrão, os campos de pesquisa na pesquisa do Azure são analisados com o [Analisador de Apache Lucene padrão](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) que divide o texto em elementos de seguir as regras de["segmentação de texto Unicode"](http://unicode.org/reports/tr29/) . Além disso, o analisador de padrão converte todos os caracteres em seus formulários de letras minúsculas. Termos de pesquisa e de documentos indexados percorrer a análise durante a indexação e processamento de consulta.

Pesquisa Azure oferece suporte a uma variedade de idiomas. Cada idioma requer um analisador de texto não padrão que contas para características de um determinado idioma. Pesquisa Azure oferece dois tipos de analisadores:

- 35 analyzers feitos por Lucene.
- 50 analyzers feitos por patenteados linguagem natural de Microsoft processamento tecnologia usada no Office e o Bing.

Alguns desenvolvedores pode ser preferível a solução mais familiar, simple de código-fonte aberto Lucene. Lucene analyzers são mais rapidamente, mas os analyzers Microsoft têm recursos avançados, como lexematização, o word decompounding (em idiomas como alemão, dinamarquês, holandês, sueco, norueguês, estoniano, término, húngaro, Eslovaco) e o reconhecimento de entidade (URLs, emails, datas, números). Se possível, você deve executar as comparações entre a Microsoft e o Lucene analyzers decidir qual é a melhor opção.

***Como ele se compara***

O analisador de Lucene para inglês estende o analisador de padrão. Ele remove possessivos (do à direita) de palavras, aplica truncamento de acordo com as [algoritmo Porter truncamento](http://tartarus.org/~martin/PorterStemmer/)e remove inglês [palavras de parada](http://en.wikipedia.org/wiki/Stop_words).

Em comparação, o analisador da Microsoft executa tematização em vez de truncamento. Isso significa que ele pode lidar com formas flexionadas e irregulares muito melhor quais resultados nos resultados de pesquisa mais relevantes (inspeção módulo 7 da [apresentação MVA de pesquisa do Azure](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) para obter mais detalhes).

A indexação com analyzers Microsoft é em média duas ou três vezes mais lenta que seus equivalentes de Lucene, dependendo do idioma. Desempenho da pesquisa não deve ser afetado significativamente para consultas de tamanho médio.

***Configuração***

Para cada campo na definição de índice, você pode definir o `analyzer` propriedade para um nome de analisador que especifica quais idiomas e fornecedor. O analisador de mesmo será aplicado quando a indexação e pesquisa para esse campo.
Por exemplo, você pode ter campos separados para inglês, francês e espanhol descrições de hotel que existem lado a lado no mesmo índice. Use o [parâmetro de consulta de 'searchFields'](#SearchQueryParameters) para especificar qual campo específicas de idioma para pesquisar contra em suas consultas. Você pode examinar os exemplos de consulta que incluam o `analyzer` propriedade nos [Documentos de pesquisa](#SearchDocs). 

***Lista de análise***

Abaixo está a lista de idiomas aceitos junto com nomes de analisador Lucene e Microsoft.

<table style="font-size:12">
    <tr>
        <th>Idioma</th>
        <th>Nome de analisador da Microsoft</th>
        <th>Nome de analisador de Lucene</th>
    </tr>
    <tr>
        <td>Árabe</td>
        <td>ar.Microsoft</td>
        <td>ar.Lucene</td>      
    </tr>
    <tr>
        <td>Armênio</td>
        <td></td>
        <td>hy.Lucene</td>
    </tr>
    <tr>
        <td>Bengali</td>
        <td>bn.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Basco</td>
        <td></td>
        <td>Eu.Lucene</td>
    </tr>
    <tr>
        <td>Búlgaro</td>
        <td>BG.Microsoft</td>
        <td>BG.Lucene</td>
    </tr>
    <tr>
        <td>Catalão</td>
        <td>CA.Microsoft</td>
        <td>CA.Lucene</td>          
    </tr>
    <tr>
        <td>Chinês simplificado</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>     
    </tr>
    <tr>
        <td>Chinês tradicional</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>     
    <tr>
    <tr>
        <td>Croata</td>
        <td>hr.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Tcheco</td>
        <td>CS.Microsoft</td>
        <td>CS.Lucene</td>      
    </tr>    
    <tr>
        <td>Dinamarquês</td>
        <td>da.Microsoft</td>
        <td>da.Lucene</td>      
    </tr>    
    <tr>
        <td>Holandês</td>
        <td>NL.Microsoft</td>
        <td>NL.Lucene</td>  
    </tr>    
    <tr>
        <td>Inglês</td>        
        <td>en.Microsoft</td>
        <td>en.Lucene</td>      
    </tr>
    <tr>
        <td>Estoniano</td>
        <td>ET.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finlandês</td>
        <td>Fi.Microsoft</td>
        <td>Fi.Lucene</td>      
    </tr>    
    <tr>
        <td>Francês</td>
        <td>FR.Microsoft</td>
        <td>FR.Lucene</td>      
    </tr>
    <tr>
        <td>Galego</td>
        <td></td>
        <td>GL.Lucene</td>      
    </tr>
    <tr>
        <td>Alemão</td>
        <td>de.Microsoft</td>
        <td>de.Lucene</td>      
    </tr>
    <tr>
        <td>Grego</td>
        <td>el.Microsoft</td>
        <td>el.Lucene</td>      
    </tr>
    <tr>
        <td>Guzerate</td>
        <td>Gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hebraico</td>
        <td>he.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.Lucene</td>      
    </tr>
    <tr>
        <td>Húngaro</td>      
        <td>hu.Microsoft</td>
        <td>hu.Lucene</td>
    </tr>
    <tr>
        <td>Islandês</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonésio (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.Lucene</td>      
    </tr>
    <tr>
        <td>Irlandês</td>
        <td></td>
        <td>GA.Lucene</td>
    </tr>
    <tr>
        <td>Italiano</td>
        <td>IT.Microsoft</td>
        <td>IT.Lucene</td>      
    </tr>
    <tr>
        <td>Japonês</td>
        <td>ja.Microsoft</td>
        <td>ja.Lucene</td>
        
    </tr>
    <tr>
        <td>Kannada</td>
        <td>Ka.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coreano</td>
        <td>ko.Microsoft</td>
        <td>ko.Lucene</td>
    </tr>
    <tr>
        <td>Letão</td>        
        <td>LV.Microsoft</td>
        <td>LV.Lucene</td>  
    </tr>
    <tr>
        <td>Lituano</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaiala</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaio (latino)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marata</td>
        <td>Mr.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norueguês</td>
        <td>NB.Microsoft</td>
        <td>no.Lucene</td>      
    </tr>
    <tr>
        <td>Persa</td>
        <td></td>
        <td>FA.Lucene</td>      
    </tr>
    <tr>
        <td>Polonês</td>
        <td>pl.Microsoft</td>
        <td>pl.Lucene</td>      
    </tr>
    <tr>
        <td>Português (Brasil)</td>
        <td>pt-Br.microsoft</td>
        <td>pt-Br.lucene</td>       
    </tr>
    <tr>
        <td>Português (Portugal)</td>
        <td>pt-Pt.microsoft</td>        
        <td>pt-Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>PA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Romeno</td>
        <td>ro.Microsoft</td>
        <td>ro.Lucene</td>
    </tr>
    <tr>
        <td>Russo</td>
        <td>RU.Microsoft</td>
        <td>RU.Lucene</td>  
    </tr>
    <tr>
        <td>Sérvio (Cirílico)</td>
        <td>SR-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Sérvio (latino)</td>
        <td>SR-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Eslovaco</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Esloveno</td>
        <td>SL.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Espanhol</td>
        <td>es.Microsoft</td>
        <td>es.Lucene</td>
    </tr>
    <tr>
        <td>Sueco</td>
        <td>SV.Microsoft</td>
        <td>SV.Lucene</td>
    </tr>

    <tr>
        <td>Tâmil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Tailandês</td>
        <td>th.Microsoft</td>
        <td>th.Lucene</td>
    </tr>
    <tr>
        <td>Turco</td>
        <td>TR.Microsoft</td>
        <td>TR.Lucene</td>      
    </tr>
    <tr>
        <td>Ucraniano</td>
        <td>uk.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamita</td>
        <td>vi.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Além disso, pesquisa Azure fornece configurações de idioma independente analisador</td>
    <tr>
        <td>ASCII padrão de dobra</td>
        <td>standardasciifolding.Lucene</td>
        <td>
        <ul>
            <li>Segmentação de texto Unicode (Tokenizer padrão)</li>
            <li>Filtro de dobra ASCII - converte caracteres Unicode que não pertencem ao conjunto de caracteres ASCII que primeiro 127 em seus equivalentes de ASCII para. Isso é útil para remover diacríticos.</li>
        </ul>
        </td>
    </tr>
</table>

Todos os analisadores com nomes anotados com <i>lucene</i> são ativados pelo [Analisadores de idioma do Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Mais informações sobre o ASCII dobra filtro podem ser encontradas [aqui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` define quais campos em um índice são usados para preenchimento automático em pesquisas de suporte. Normalmente cadeias de caracteres de pesquisa parcial são enviadas à [API de sugestões](#Suggestions) enquanto o usuário está digitando uma consulta de pesquisa, e a API retorna um conjunto de frases sugeridas. Um suggester que você define no índice determina quais campos são usados para criar os termos de pesquisa com previsão de escrita. Consulte [Suggesters](#Suggesters) para obter detalhes de configuração.

**Perfis de pontuação**

A `scoringProfile` define pontuação comportamentos personalizados que permitem que você influenciar os itens que aparecerão maiores nos resultados da pesquisa. Perfis de pontuação são compostos de funções e espessuras de campo. Para usá-los, você pode especificar um perfil por nome na sequência de consulta.

Um padrão de pontuação perfil opera nos bastidores para calcular um resultado de pesquisa para cada item em um conjunto de resultados. Você pode usar o internos, sem nome pontuação perfil. Como alternativa, definir `defaultScoringProfile` usar um perfil personalizado como o padrão, invocado sempre que um perfil personalizado não for especificado na sequência de consulta.

Consulte [Adicionar perfis pontuação para um índice de pesquisa (API de restante do serviço de pesquisa do Azure)](search-api-scoring-profiles-2015-02-28-preview.md) para obter detalhes.

**Opções de CORS**

Javascript do lado do cliente não pode chamar qualquer APIs por padrão, desde que o navegador impedirá que todas as solicitações de entre origens. Ativar CORS (compartilhamento de recursos entre origens) definindo a `corsOptions` atributo para permitir que as consultas entre origens para seu índice. Observação Essa consulta apenas o suporte APIs CORS por razões de segurança. As seguintes opções podem ser definidas para CORS:

- `allowedOrigins`(obrigatório): esta é uma lista de origens que terão acesso ao índice. Isso significa que qualquer código Javascript servido dessas origens poderão ser consultar seu índice (presumindo que ele fornece a chave de API correta). Cada origem normalmente é do formulário `protocol://fully-qualified-domain-name:port` Embora a porta geralmente é omitida. Consulte [Este artigo](http://go.microsoft.com/fwlink/?LinkId=330822) para obter mais detalhes.
 - Se você quiser permitir o acesso a todas as origens, inclua `*` como um único item na `allowedOrigins` matriz. Observe que **isso não é recomendável prática para serviços de pesquisa de produção.** No entanto, pode ser útil para desenvolvimento ou fins de depuração.
- `maxAgeInSeconds`(opcional): navegadores usam esse valor para determinar a duração (em segundos) para respostas comprovação do cache CORS. Isso deve ser um inteiro não negativo. Esse valor é maior, o melhor desempenho será, mas mais tempo levará para CORS política alterações tenham efeito. Se não estiver definida, uma duração padrão de 5 minutos será usada.

<a name="CreateUpdateIndexExample"></a>
**Exemplo de corpo da solicitação**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Resposta**

Para uma solicitação bem-sucedida: "201 criado".

Por padrão, o corpo da resposta conterá o JSON para a definição de índice criado. Se o `Prefer` cabeçalho solicitação está definido como `return=minimal`, o corpo de resposta estará vazio e o código de status de sucesso será "204 sem conteúdo" em vez de "201 criado". Isso é verdadeiro independentemente se colocar ou POSTAGEM foi usada para criar o índice.

**Comentários**

Atualmente, não há suporte limitado para atualizações de esquema de índice. Todas as atualizações de esquema que exijam a reindexação como alterar tipos de campo não são suportadas no momento. Embora campos existentes não podem ser alterados ou excluídos, os novos campos podem ser adicionados a um índice existente a qualquer momento. Quando um novo campo é adicionado, todos os documentos existentes no índice automaticamente terá um valor nulo para esse campo. Não há espaço de armazenamento adicional será consumido até novos documentos são adicionados ao índice.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

O recurso de sugestões na pesquisa do Azure é um recurso de consulta com previsão de escrita ou preenchimento automático, fornecendo uma lista de possíveis termos de pesquisa em resposta às entradas de cadeia de caracteres parcial inseridos em uma caixa de pesquisa. Você já deve ter notado sugestões de consulta ao usar mecanismos de pesquisa da web comercial: digitando ".NET" em Bing produz uma lista de termos para ".NET 4,5", ".NET Framework 3.5", e assim por diante. Ao usar o serviço de pesquisa API REST, implementar sugestões em um aplicativo personalizado do Azure pesquisa requer o seguinte:

- Ativar sugestões adicionando uma construção **suggester** no seu índice, fazendo o nome, o modo de pesquisa e uma lista de campos para a qual digitação antecipada é invocada. Por exemplo, se você especificar "NomeCidade" como um campo de origem, digitando uma cadeia de caracteres de pesquisa parcial de "Mar" resultará em "Seattle", "Litoral" e "Seatac" (todos os três são nomes de cidades real) oferecidos como sugestões de consulta para o usuário.

- Chame sugestões, chamando a [API de sugestões](#Suggestions) em seu código de aplicativo. Normalmente cadeias de caracteres de pesquisa parcial são enviadas para o serviço enquanto o usuário está digitando uma consulta de pesquisa e, em seguida, essa API retorna um conjunto de frases sugeridas.

Este artigo explica como configurar um **suggester**. Você também deve examinar a [API de sugestões](#Suggestions) para obter detalhes sobre como um suggester é usado.

**Uso**

`Suggesters`são criados no índice e trabalho melhor quando usada para sugerir documentos específicos em vez de termos ampliados ou frases. Os campos de candidato melhores são títulos, nomes e outras frases relativamente curtos que podem identificar um item. Menos eficazes são campos repetitivos, como categorias e marcas, ou campos muito longos como campos descrições ou comentários.

Como parte da definição de índice, você pode adicionar um único suggester para o `suggesters` conjunto. Propriedades que definem um suggester incluem o seguinte:

- `name`: O nome da suggester. Você usa o nome do suggester ao chamar o `suggest` API.
- `searchMode`: A estratégia usada para procurar frases de candidatos. O modo somente suportado atualmente é `analyzingInfixMatching`, que realiza correspondente flexíveis frases no início ou no meio de frases.
- `sourceFields`: Uma lista de um ou mais campos que são a origem do conteúdo para obter sugestões. Somente os campos de tipo de `Edm.String` e `Collection(Edm.String)` podem ser fontes para sugestões. Somente os campos que não têm um analisador de idioma personalizada definir podem ser usados.

**Exemplo de suggester**

Um suggester faz parte do índice. Somente uma suggester pode existir na `suggesters` conjunto na versão atual, junto com o conjunto de campos e `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Se você usou a versão de visualização pública do Azure Search, `suggesters` substitui uma propriedade booleana mais antiga (`"suggestions": false`) que suporte apenas o prefixo sugestões para cadeias de caracteres curtas (3-25 caracteres). Sua substituição, `suggesters`, suporta infix correspondência localiza termos correspondentes no início ou no meio do conteúdo do campo, com melhor tolerância de erros em cadeias de caracteres de pesquisa. Começando com a versão disponível, isso é agora a implementação única das sugestões de API. O antigo `suggestions` propriedade que foi introduzida em `api-version=2014-07-31-Preview` continua a trabalhar nessa versão, mas não está funcionando na `2015-02-28` ou versões posteriores de pesquisa do Azure.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Atualizar índice

Você pode atualizar um índice existente em pesquisa do Azure usando uma solicitação de HTTP colocar. Atualizações podem incluir adicionando novos campos ao esquema existente, modificar as opções de CORS e modificar perfis de pontuação. Para obter mais informações, consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Especifique o nome do índice a atualizar no URI da solicitação:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante:** Suporte para atualizações de esquema de índice está limitado aos operações que não requeiram recriar o índice de pesquisa. Todas as atualizações de esquema que exijam a reindexação, como a alteração de tipos de campo, não são suportadas no momento. Novos campos podem ser adicionados a qualquer momento, embora campos existentes não podem ser alterados ou excluídos. O mesmo se aplica à `suggesters`. Novos campos podem ser adicionados a um suggester no momento em que os campos são adicionados, mas campos não podem ser removidos do `suggesters` e campos existentes não podem ser adicionados a `suggesters`.

Ao adicionar um novo campo a um índice, todos os documentos existentes no índice automaticamente terá um valor nulo para esse campo. Não há espaço de armazenamento adicional será consumido até novos documentos são adicionados ao índice.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação de **Atualizar índice** é construída usando HTTP colocar. Com colocar, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se o índice já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, comece com uma letra ou número, têm sem barras ou pontos e ter menos de 128 caracteres. Após iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não são consecutivos.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: Necessários. Defina como`application/json`
- `api-key`: Necessários. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Atualizar índice** deve incluir um `api-key` cabeçalho definido como sua chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Sintaxe de solicitação de corpo**

Ao atualizar um índice existente, o corpo deve incluir a definição de esquema original, além dos novos campos que você está adicionando, bem como os perfis de pontuação modificados, suggesters CORS opções e, se houver. Se você não estiver modificando as opções de CORS e perfis de pontuação, você deve incluir os originais de quando o índice foi criado. Em geral o padrão de melhor usar atualizações é recuperar a definição de índice com um GET, modificá-lo, então atualizá-lo com colocar.

A sintaxe de esquema usada para criar um índice é reproduzida aqui para sua conveniência. Consulte [Create Index](#CreateIndex) para obter mais detalhes.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Resposta**

Para uma solicitação bem-sucedida: "204 sem conteúdo".

Por padrão, o corpo de resposta estará vazio. No entanto, se o `Prefer` cabeçalho solicitação está definido como `return=representation`, corpo da resposta conterá o JSON para a definição de índice que foi atualizado. Nesse caso, o código de status de sucesso será "200 Okey".

**Atualizar a definição de índice com analyzers personalizados**

Assim que um analisador, um tokenizer, um filtro de token ou um filtro de caract é definido, ela não pode ser modificada. Novos podem ser adicionados a um índice existente somente se a `allowIndexDowntime` sinalizador estiver definido como true na solicitação de atualização de índice: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Observe que essa operação colocará seu índice offline para solicitações de consulta falha e pelo menos alguns segundos, causando a indexação. Disponibilidade de desempenho e gravação do índice pode ser por vários minutos após o índice é atualizado de deficiência visual ou mais índices muito grandes.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Índices de lista

A operação de **Índices de lista** retorna uma lista dos índices atualmente no seu serviço de pesquisa do Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação de **Índices de lista** pode ser construída usando o método GET.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: Necessários. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Índices de lista** deve incluir um `api-key` definida como uma chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

Aqui está um corpo de resposta de exemplo:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Observe que você pode filtrar a resposta para baixo até apenas as propriedades que você está interessado. Por exemplo, se desejar somente uma lista de nomes de índice, use o OData `$select` opção de consulta:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Nesse caso, a resposta do exemplo acima terá a seguinte aparência:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Essa é uma técnica útil para salvar a largura de banda se você tiver muitas índices em seu serviço de pesquisa.

<a name="GetIndex"></a>
## <a name="get-index"></a>Obter índice

A operação de **Índice obter** obtém a definição de índice de pesquisa do Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **Obter índice** pode ser construída usando o método GET.

O [nome do índice] na solicitação de URI especifica quais índice para retornar a partir do conjunto de índices.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Obter índice** deve incluir um `api-key` definida como uma chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

Consulte o exemplo JSON em [criar e atualizar um índice](#CreateUpdateIndexExample) para obter um exemplo de carga de resposta.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Excluir índice

A operação de **Índice excluir** remove um índice e documentos associados do seu serviço de pesquisa do Azure. Você pode obter o nome do índice no painel de serviço no portal do Azure ou da API. Consulte [Índices de lista](#ListIndexes) para obter detalhes.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **Excluir índice** pode ser construída usando o método DELETE.

O [nome do índice] na solicitação de URI especifica quais índice para excluir da coleção de índices.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: Necessários. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo para o URL do serviço. A solicitação de **Excluir índice** deve incluir um `api-key` cabeçalho definido como sua chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 204 sem conteúdo é retornado por uma resposta bem-sucedido.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Obter estatísticas de índice

A operação de **Obter estatísticas de índice** retorna da pesquisa do Azure uma contagem de documento para o índice atual, além de uso de armazenamento.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] As estatísticas de tamanho de armazenamento e a contagem de documento são coletadas cada alguns minutos, não em tempo real. Portanto, as estatísticas retornadas por esta API podem não refletir alterações causados por operações de indexação recentes.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviços. A solicitação de **Obter estatísticas de índice** pode ser construída usando o método GET.

O [nome do índice] na solicitação de URI informa o serviço para retornar as estatísticas de índice para o índice especificado.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.


**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Obter estatísticas de índice** deve incluir um `api-key` definida como uma chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

O corpo de resposta é no seguinte formato:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Analisador de teste

**Analisar API** mostra como um analisador quebra o texto em tokens.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviços. A solicitação de **Analisar API** pode ser construída usando o método POST.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.


**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Analisar API** deve incluir um `api-key` definida como uma chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do índice e o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

ou

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

O `analyzer_name`, `tokenizer_name`, `token_filter_name` e `char_filter_name` devem ser nomes válidos de analisadores predefinidos ou personalizados, tokenizers, filtros de token e filtros de CARACT para o índice. Para saber mais sobre o processo de análise lexical consulte [análise na pesquisa do Azure](https://aka.ms/azsanalysis).

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

O corpo de resposta é no seguinte formato:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analisar o exemplo da API**

**Solicitação**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Resposta**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Operações de documento

Em pesquisa do Azure, um índice é armazenado na nuvem e preenchido usando documentos JSON que você carrega no serviço. Todos os documentos que você carregar compõem o corpo dos seus dados de pesquisa. Documentos contêm campos, alguns dos quais estão indexados em termos de pesquisa, como eles são carregados. O `/docs` segmento de URL na API de pesquisa do Azure representa o conjunto de documentos em um índice. Todas as operações executadas no conjunto de como carregar, mesclando, excluir ou consultar documentos leve coloque no contexto de um único índice, então as URLs para essas operações sempre serão iniciado com `/indexes/[index name]/docs` um nome de índice fornecido.

O código do seu aplicativo deve gerar documentos JSON para carregar no Azure pesquisa ou você pode usar um [indexador](https://msdn.microsoft.com/library/dn946891.aspx) para carregar documentos se a fonte de dados for Azure SQL Database ou DocumentDB. Normalmente, os índices são preenchidos a partir de um único conjunto de dados que você fornecer.

Você deve planejar ter um documento para cada item que você deseja pesquisar. Um aplicativo de locação filme pode ter um documento por filme, um aplicativo de vendas pode ter um documento por SKU, um aplicativo de certificação online pode ter um documento por curso, uma empresa de pesquisa pode ter um documento para cada papel acadêmico no seu repositório e assim por diante.

Documentos consistem em um ou mais campos. Campos podem conter valores de texto que é indexado pela pesquisa do Azure em termos de pesquisa, bem como não indexado ou não são de texto que podem ser usados em perfis de pontuação ou filtros. Os nomes, tipos de dados e recursos de pesquisa com suporte para cada campo são determinados pelo esquema de índice. Um dos campos em cada esquema de índice deve ser designado como uma ID, e cada documento deve ter um valor para o campo identificação que identifica exclusivamente o documento no índice. Todos os outros campos de documento são opcionais e padrão serão um valor nulo se não for especificado à esquerda. Observe que os valores nulos não têm espaço no índice de pesquisa.

Antes de você pode carregar documentos, você deve ter já criado o índice no serviço. Consulte [Create Index](#CreateIndex) para obter detalhes sobre esta primeira etapa.

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Adicionar, atualizar ou excluir documentos

Você pode carregar, documentos de mala direta, direta ou carregar ou exclusão de um índice especificado usando HTTP POST. Para grandes números de atualizações, recomenda-se o processamento em lotes de documentos até (1000 documentos por lote) ou cerca de 16 MB por lote.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. Você pode carregar, documentos de mala direta, direta ou carregar ou exclusão de um índice especificado usando HTTP POST.

A solicitação URI inclui [nome do índice], especificando qual índice para postar documentos. Você só pode postar documentos para um índice por vez.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: Necessários. Defina como`application/json`
- `api-key`: Necessários. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo do seu serviço. A solicitação de **Adicionar documentos** deve incluir um `api-key` cabeçalho definido como sua chave de administrador (em vez de uma chave de consulta).

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

O corpo da solicitação contém um ou mais documentos a serem indexados. Documentos são identificados por uma chave exclusiva. Cada documento está associado uma ação: carregamento, mala direta, mergeOrUpload ou excluir. Solicitações de carregamento devem incluir os dados do documento como um conjunto de pares chave/valor.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Teclas de documento podem conter apenas letras, números, traços ("-"), sublinhados ("_") e sinais de igual ("="). Para obter mais detalhes, consulte [regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Ações do documento**

- `upload`: Uma ação de carregamento é semelhante a "upsert" onde o documento será inserido se for novas e atualizadas/substituído se existir. Observe que todos os campos são substituídos no caso de atualização.
- `merge`: Mesclar atualiza um documento existente com os campos especificados. Se o documento não existir, a mesclagem falhará. Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos de tipo `Collection(Edm.String)`. Por exemplo, se o documento contiver um campo "marcas" com valor `["budget"]` e você executar uma mala direta com valor `["economy", "pool"]` para "marcas", o valor final do campo "marcas" será `["economy", "pool"]`. Ele será **não** ser `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comporta como `merge` se já existe um documento com a chave especificada no índice. Se o documento não existi-lo se comporta como `upload` com um novo documento.
- `delete`: Excluir remove o documento especificado do índice. Observe que todos os campos que você especificar em um `delete` operação diferente no campo de chave será ignorada. Se você quiser remover um campo individual de um documento, use `merge` em vez disso e simplesmente configurar o campo explicitamente para `null`.

**Resposta**

Código de status 200 (Okey) é retornado por uma resposta bem-sucedida, indicando que todos os itens indexados com êxito. Isso é indicado pela `status` propriedade sendo definida como true para todos os itens, bem como o `statusCode` propriedade sendo definida 201 (para documentos carregados recentemente) ou 200 (para documentos mesclados ou excluídos):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Código de status 207 (Status múltiplo) é retornado quando pelo menos um item não foi indexado com êxito. Itens que não tiverem sido indexados têm o `status` campo definido como false. O `errorMessage` e `statusCode` propriedades indicará o motivo do erro do indexação:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

A tabela a seguir explica os diversos códigos de status de por documento que podem ser retornados na resposta. Observe que alguns indicam problemas com a solicitação em si, enquanto outras pessoas indicam condições de erro temporário. O último que você deverá repetir após um atraso.

<table style="font-size:12">
    <tr>
        <th>Código de status</th>
        <th>Significado</th>
        <th>Nova tentativa</th>
        <th>Anotações</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Documento com êxito foi modificado ou excluído.</td>
        <td>n/d</td>
        <td>Operações de exclusão são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotente</a>. Isto é, mesmo se não existe uma chave de documento no índice, a tentativa de uma operação de exclusão com essa chave resultará em um código de 200 status.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Documento foi criado com êxito.</td>
        <td>n/d</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Ocorreu um erro no documento que impede que ele está sendo indexado.</td>
        <td>Não</td>
        <td>A mensagem de erro na resposta indicará o que está errado com o documento.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>O documento não puderam ser mesclado porque a chave fornecida não existe no índice.</td>
        <td>Não</td>
        <td>Este erro não ocorre para carregamentos desde que eles criarem novos documentos, e não ocorre para exclusões porque eles são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotente</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Foi detectado um conflito de versão ao tentar indexar um documento.</td>
        <td>Sim</td>
        <td>Isso pode acontecer quando você estiver tentando o índice mais de uma vez simultaneamente no mesmo documento.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>O índice está temporariamente indisponível porque ele foi atualizado com o sinalizador 'allowIndexDowntime' definido como 'true'.</td>
        <td>Sim</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>O serviço de pesquisa está temporariamente indisponível, possivelmente devido a carga pesada.</td>
        <td>Sim</td>
        <td>Seu código deve esperar antes de tentar novamente nesse caso ou risco prolongando a indisponibilidade de serviço.</td>
    </tr>
</table> 

**Observação**: se o código do seu cliente frequentemente encontrar uma resposta 207, um motivo possível é que o sistema está sob carga. Você pode confirmar isso marcando a `statusCode` propriedade para 503. Se esse for o caso, recomendamos ***a otimização de solicitações de indexação***. Caso contrário, se a indexação de tráfego não diminuir, o sistema foi possível iniciar rejeitar todas as solicitações com 503 erros.

Código de status 429 indica que você excedeu sua cota no número de documentos por índice. Você deve criar um novo índice ou atualizar limites de capacidade superiores.

**Exemplo:**

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
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Procurar documentos

Uma operação de **pesquisa** é emitida como uma solicitação GET ou POST e especifica os parâmetros que fornecem os critérios para seleção de documentos correspondentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usar POST em vez de GET**

Quando você usa HTTP GET para chamar a API de **pesquisa** , você precisa estar ciente de que o comprimento da solicitação URL não pode exceder 8 KB. Isso geralmente é suficiente para a maioria dos aplicativos. No entanto, alguns aplicativos produzem consultas muito grandes ou expressões de filtro de OData. Para esses aplicativos, usando HTTP POST é a melhor opção, pois permite maior filtros e consultas que GET. Com POSTAGEM, o número de termos ou cláusulas em uma consulta é o fator de limitação, não o tamanho da consulta bruto desde que o limite de tamanho de solicitação POST é de aproximadamente 16 MB.

> [AZURE.NOTE] Apesar do limite de tamanho de solicitação de POSTAGEM for muito grande, expressões de filtro e consultas de pesquisa não podem ser absolutamente complexas. Consulte [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) e [sintaxe de expressões de OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de consulta e filtro de pesquisa.

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **pesquisa** pode ser construída usando os métodos GET ou POST.

URI da solicitação especifica quais índice a consulta, para todos os documentos que correspondam aos parâmetros. Parâmetros são especificados na sequência de consulta no caso de solicitações GET e na solicitação de solicitações de corpo no caso de POSTAGEM.

Como prática recomendada ao criar solicitações GET, lembre-se de parâmetros de consulta específica de [codificar URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) ao chamar a API REST diretamente. Para operações de **pesquisa** , isso inclui:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

Codificação de URL só é recomendado nos parâmetros de consulta acima. Se você inadvertidamente codificar a URL a cadeia de caracteres de consulta inteira (tudo após o?), solicitações interromperá.

Além disso, a codificação de URL só é necessário ao chamar a API REST diretamente usando GET. Nenhuma codificação de URL é necessário ao chamar **pesquisa** usando POSTAGEM, ou quando usar a [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que controla a codificação de URL para você.

<a name="SearchQueryParameters"></a>
**Parâmetros de consulta**

**Pesquisa** aceita diversos parâmetros que fornecem critérios de consulta e também especificar o comportamento de pesquisa. Você fornecer que esses parâmetros na URL sequência de consulta ao chamar **pesquisa** via GET e como propriedades JSON no corpo da solicitação ao chamar **pesquisa** por meio de POSTAGEM. A sintaxe para alguns parâmetros é um pouco diferente entre GET e POST. Essas diferenças são indicadas conforme aplicável abaixo:

`search=[string]`(opcional) - o texto a ser pesquisado. Todos os `searchable` campos são pesquisados por padrão, a menos que `searchFields` especificado. Ao pesquisar `searchable` campos, o próprio texto de pesquisa é indexado, portanto vários termos podem ser separados por espaço em branco (por exemplo: `search=hello world`). Para combinar qualquer termo, use `*` (Isso pode ser útil para consultas de filtro booliano). Omitir esse parâmetro tem o mesmo efeito da configuração com `*`. Consulte [Sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) para informações específicas sobre a sintaxe da pesquisa.

  - **Observação**: os resultados às vezes podem ser surpresa ao consultar sobre `searchable` campos. O tokenizer inclui lógica para lidar com casos comuns para texto em inglês como apóstrofos, vírgulas de números, etc. Por exemplo, `search=123,456` corresponderão um termo único 123,456 em vez de cada um do termos 123 e 456, como vírgulas são usadas como separadores de milhar para números grandes em inglês. Por esse motivo, recomendamos o uso de espaço em branco em vez de pontuação para separar termos na `search` parâmetro.

`searchMode=any|all`(opcional, padrão é `any`) - se um ou todos os termos de pesquisa devem ser correspondidos para contar o documento como uma correspondência.

`searchFields=[string]`(opcional) - a lista de nomes de campo separados por vírgulas para pesquisar o texto especificado. Campos de destino devem ser marcados como `searchable`.

`queryType=simple|full`(opcional, padrão é `simple`) - quando definida como o texto de pesquisa "simples" é interpretada usando uma linguagem de consulta simples que possibilite símbolos, como +, * e "". Consultas são avaliadas em todos os campos pesquisáveis (ou campos indicados em `searchFields`) em cada documento por padrão. Quando o tipo de consulta é definido como `full` texto de pesquisa é interpretado usando a linguagem de consulta Lucene que permite pesquisas específicas do campo e ponderadas. Consulte [Sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) e [Sintaxe de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) para informações específicas sobre a sintaxe da pesquisa. 
 
> [AZURE.NOTE] Intervalo de pesquisa no Lucene não há suporte para a linguagem de consulta por $filter que oferece funcionalidade similar.

`moreLikeThis=[key]`(opcional) **Importante:** Este recurso só está disponível em `2015-02-28-Preview`. Esta opção não pode ser usada em uma consulta que contém o parâmetro de pesquisa de texto, `search=[string]`. O `moreLikeThis` parâmetro localiza documentos que são semelhantes do documento especificado pela chave do documento. Quando uma solicitação de pesquisa é feita com `moreLikeThis`, uma lista de termos de pesquisa é gerada com base na frequência e raridade de termos no documento de origem. Esses termos são usados em seguida, fazer a solicitação. Por padrão, o conteúdo de todas `searchable` campos são considerados, a menos que `searchFields` é usado para restringir os campos que são pesquisados.  

`$skip=#`(opcional) - o número de resultados de pesquisa para ignorar; Não pode ser maior que 100,000. Se você precisa digitalizar documentos em sequência, mas não é possível usar `$skip` devido a essa limitação, considere o uso de `$orderby` em uma chave totalmente ordenadas e `$filter` com um intervalo de consulta, em vez disso.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `skip` em vez de `$skip`.

`$top=#`(opcional) - o número de resultados de pesquisa para recuperar. Isso pode ser usado em conjunto com `$skip` implementar paginação de cliente dos resultados da pesquisa.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `top` em vez de `$top`.

`$count=true|false`(opcional, padrão é `false`)-Especifica se a contagem total de resultados de busca. É a contagem de todos os documentos que correspondem a `search` e `$filter` parâmetros, ignorando `$top` e `$skip`. Definir esse valor como `true` pode ter um impacto de desempenho. Observe que a contagem retornada é uma aproximação.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `count` em vez de `$count`.

`$orderby=[string]`(opcional) - uma lista de expressões separados por vírgulas para classificar os resultados por. Cada expressão pode ser um nome de campo ou uma chamada para a `geo.distance()` função. Cada expressão pode ser seguido por `asc` para indicado em ordem crescente, e `desc` para indicar decrescente. O padrão é ordem crescente. TIES vai ser desfeitos pelas pontuações de correspondência de documentos. Se nenhuma `$orderby` for especificada, a ordem de classificação padrão é decrescente por pontuação de correspondência do documento. Há um limite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `orderby` em vez de `$orderby`.

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para recuperar. Se não for especificado, todos os campos marcados como recuperáveis no esquema serão incluídos. Você pode solicitar também explicitamente todos os campos definindo esse parâmetro para `*`.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `select` em vez de `$select`.

`facet=[string]`(zero ou mais) - um campo para faceta por. Opcionalmente, a cadeia de caracteres pode conter parâmetros para personalizar o faceting expressa separados por vírgulas `name:value` pares. Parâmetros válidos são:

- `count`(máx número de termos de faceta; padrão é 10). Não há nenhum máximo, mas valores mais altos provocam uma perda de desempenho correspondente, especialmente se o campo Facetado contém um grande número de termos exclusivos.
  - Por exemplo: `facet=category,count:5` obtém as categorias de cinco principais nos resultados de faceta.  
  - **Observação**: se o `count` parâmetro é menor que o número de termos exclusivos, os resultados podem não ser precisos. Isso é devido a maneira como as consultas de faceting são distribuídas por fragmentos. Aumentar `count` geralmente aumenta a precisão de contagens de termos, mas a um custo de desempenho.
- `sort`(uma das `count` classificar *em ordem decrescente* por contagem, `-count` classificar *em ordem crescente* por contagem, `value` classificar *em ordem crescente* por valor, ou `-value` classificar *em ordem decrescente* por valor)
  - Por exemplo: `facet=category,count:3,sort:count` obtém as categorias de três principais nos resultados de faceta em ordem decrescente por número de documentos com o nome de cada cidade. Por exemplo, se as três categorias principais são orçamento, bichos e luxo e orçamento tem 5 visitas, bichos tem 6 e luxo tem 4, então as classificações será na ordem bichos, orçamento, luxo.
  - Por exemplo: `facet=rating,sort:-value` produz classificações para todas as classificações de possíveis, em ordem decrescente por valor. Por exemplo, se as classificações são de 1 a 5, as classificações serão ordenadas 5, 4, 3, 2, 1, independentemente de quantos documentos correspondem cada classificação.
- `values`(delimitada numérico ou `Edm.DateTimeOffset` valores especificando um conjunto dinâmico de valores de entrada de faceta)
  - Por exemplo: `facet=baseRate,values:10|20` produz três classificações: uma para taxa de base 0 até, mas sem incluir até 10, um para 10, mas não incluindo 20 e outra para 20 ou superior.
  - Por exemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produz duas classificações: uma para hotéis renovated antes de fevereiro de 2010 e para hotéis renovated fevereiro 1º, 2010 ou posterior.
- `interval`(intervalo inteiro maior do que 0 para números, ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para valores de tempo de data)
  - Por exemplo: `facet=baseRate,interval:100` produz baseadas em intervalos de taxa base de tamanho 100. Por exemplo, se forem taxas base todas entre $60 e US $600, haverá classificações para 0-100, 100-200, 200-300, 300-400, 400-500 e 500-600.
  - Por exemplo: `facet=lastRenovationDate,interval:year` produz um Balde de cada ano quando hotéis foram renovated.
- `timeoffset`([+-] hh: mm, [+-] hh: mm, ou [+-] hh) `timeoffset` é opcional. Só pode ser combinado com o `interval` opção e somente quando aplicado a um campo do tipo `Edm.DateTimeOffset`. O valor Especifica o deslocamento de horário UTC conta para definir limites de tempo.
  - Por exemplo: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` usa o limite de dia que inicia no UTC 01:00:00 (meia-noite no fuso horário de destino)
- **Observação**: `count` e `sort` podem ser combinadas na mesma especificação faceta, mas eles não podem ser combinados com `interval` ou `values`, e `interval` e `values` não pode ser combinado juntos.
- **Observação**: aspectos de intervalo em vez de data são calculados com base no horário UTC se `timeoffset` não for especificado. Por exemplo: para `facet=lastRenovationDate,interval:day`, o dia limite começa em 00:00:00 UTC. 

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `facets` em vez de `facet`. Além disso, você especificá-lo como uma matriz JSON de cadeias de caracteres onde cada cadeia de caracteres é uma expressão de faceta separada.

`$filter=[string]`(opcional) - uma expressão de pesquisa estruturadas em sintaxe de OData padrão. Consulte [Sintaxe de expressões de OData](#ODataExpressionSyntax) para obter detalhes sobre o subconjunto da gramática de expressão de OData que dá suporte a pesquisa do Azure.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `filter` em vez de `$filter`.

`highlight=[string]`(opcional) - um conjunto de nomes de campo separados por vírgulas usados para sucessos realça. Somente `searchable` campos podem ser usados para realce de sucessos.

`highlightPreTag=[string]`(opcional, padrão é `<em>`) - uma marca que precede para acertar destaques de cadeia de caracteres. Deve ser definido com `highlightPostTag`.

> [AZURE.NOTE] Quando chamar **pesquisa** usando GET, caracteres reservados na URL deve ser porcentagem-codificado (por exemplo, % 23 em vez de #).

`highlightPostTag=[string]`(opcional, padrão é `</em>`)-uma marca de cadeia de caracteres que acrescenta para acertar destaques. Deve ser definido com `highlightPreTag`.

> [AZURE.NOTE] Quando chamar **pesquisa** usando GET, caracteres reservados na URL deve ser porcentagem-codificado (por exemplo, % 23 em vez de #).

`scoringProfile=[string]`(opcional) - o nome de um perfil de pontuação para avaliar corresponder a pontuação para obter a correspondência de documentos para classificar os resultados.

`scoringParameter=[string]`(zero ou mais) - indica os valores para cada parâmetro definido em uma função de pontuação (por exemplo, `referencePointParameter`) usando o formato `name-value1,value2,...`.

- Por exemplo, se o perfil pontuação define uma função com um parâmetro chamado "mylocation" a opção de cadeia de caracteres de consulta seria `&scoringParameter=mylocation--122.2,44.8`. O primeiro traço separa o nome da lista de valores, enquanto o segundo hífen faz parte do primeiro valor (longitude neste exemplo).
- Parâmetros de pontuação, como marca aumentar que pode conter vírgulas, você pode escape qualquer desses valores na lista usando aspas simples. Se os próprios valores contiverem aspas simples, você pode escape-las por dobra.
  - Por exemplo, se você tiver uma marca aumentando o parâmetro chamado "mytag" e você quiser impulsionar na marca valores "Olá, o ' Brien" e "Smith", a consulta opção de cadeia de caracteres seria `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Observe que as cotações são somente necessários para valores que contenham vírgulas.

> [AZURE.NOTE] Ao chamar **pesquisa** usando POSTAGEM, este parâmetro é chamado `scoringParameters` em vez de `scoringParameter`. Além disso, especifique como uma matriz JSON de cadeias de caracteres onde cada cadeia de caracteres é uma separado `name-values` par.

`minimumCoverage`(opcional, o padrão é 100)-um número entre 0 e 100 indicando a porcentagem de índice que deve ser coberto por uma consulta de pesquisa em ordem para a consulta para ser relatada como um sucesso. Por padrão, o índice inteiro deve estar disponível ou `Search` retornará o código de status HTTP 503. Se você definir `minimumCoverage` e `Search` é bem sucedida, ele irá retornar HTTP 200 e incluir uma `@search.coverage` valor na resposta indicando a porcentagem do índice que foi incluída na consulta.

> [AZURE.NOTE] Definir esse parâmetro para um valor menor que 100 pode ser útil para garantir a disponibilidade de pesquisa mesmo para os serviços com apenas uma réplica. No entanto, nem todos os documentos correspondentes são garantia presentes nos resultados da pesquisa. Se o cancelamento de pesquisa é mais importante para o seu aplicativo disponibilidade, é melhor deixar `minimumCoverage` com seu valor padrão de 100.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, o `api-version` especificado como um parâmetro de consulta na URL, independentemente se você chamar **pesquisa** com GET ou POST.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo para o URL do serviço. A solicitação de **pesquisa** pode especificar uma chave admin ou tecla de consulta para `api-key`.

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Para obter: nenhum.

Para POSTAGEM:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuação de respostas de pesquisa parcial**

Às vezes Azure pesquisa não pode retornar todos os resultados solicitados em uma única resposta de pesquisa. Isso pode acontecer por diferentes motivos, como quando a consulta solicita muitos documentos, não especificando `$top` ou especificar um valor para `$top` que é muito grande. Nesses casos, o Azure pesquisa incluirá o `@odata.nextLink` anotação no corpo da resposta e também `@search.nextPageParameters` se tiver sido uma solicitação POST. Você pode usar os valores dessas anotações para Formula outra solicitação de pesquisa para obter a próxima parte da resposta de pesquisa. Isso se chama uma ***continuação*** da solicitação de pesquisa original e as anotações são geralmente chamadas ***tokens de continuação***. Consulte [o exemplo a seguir](#SearchResponse) para obter detalhes sobre a sintaxe dessas anotações e onde elas aparecem no corpo da resposta. 

Os motivos por que a pesquisa de Azure poderá retornar tokens de continuação são específico de implementação e sujeitos a mudança. Clientes robustos sempre devem estar prontos para lidar com casos em que menos documentos que o esperado são retornados e um token de continuação é incluído para continuar a recuperação de documentos. Observe também que você deve usar o mesmo método HTTP como a solicitação original para continuar. Por exemplo, se você enviou uma solicitação GET, quaisquer solicitações de continuação enviar devem também usar GET (e da mesma forma para POSTAGEM).

<a name="SearchResponse"></a>
**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemplos:**

Você pode encontrar exemplos adicionais na página da [Sintaxe de expressões de OData para pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)  O índice de pesquisa classificados em ordem decrescente por data.


    OBTER /indexes/hotels/docs? pesquisa = * & $orderby = lastRenovationDate desc & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "*", "orderby": "lastRenovationDate desc"}

2)  Em uma pesquisa faceta, pesquise no índice e recuperar aspectos de categorias, classificação, marcas, bem como itens com baseRate em intervalos específicos:


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = categoria & faceta = classificação & faceta = marcas & faceta = baseRate, valores: 80 | 150 | 220 & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "testar", "aspectos": ["categoria", "classificação", "marcas", "baseRate, valores: 80 | 150 | 220"]}

3)  Usando um filtro, limitar os resultados de consulta Facetado anterior após o usuário clicar na categoria "Bichos" e 3 de classificação:


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = marcas & faceta = baseRate, valores: 80 | 150 | 220 & $filter = & versão da api de classificação eq 3 e categoria eq 'bichos' = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "testar", "aspectos": ["marcas", "baseRate, valores: 80 | 150 | 220"], "filtro": "eq de classificação 3 e categoria eq 'Bichos'"}

4) Em uma pesquisa faceta, defina um limite superior em termos exclusivos retornados em uma consulta. O padrão é 10, mas você pode aumentar ou diminuir esse valor usando o `count` parâmetro na `facet` atributo:


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = cidade, contagem: 5 & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "testar", "aspectos": ["cidade, contagem: 5"]}

5)  Pesquisar o índice de campos específicos; Por exemplo, um campo de idiomas específicos:


    OBTER /indexes/hotels/docs? pesquisa = hôtel & searchFields = description_fr & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "hôtel", "searchFields": "description_fr"}

6) Pesquise o índice em vários campos. Por exemplo, você pode armazenar e campos de pesquisa em vários idiomas, tudo isso dentro o mesmo índice de consulta.  Se as descrições de inglês e francês conjunta existirem no mesmo documento, você pode retornar um ou todos nos resultados da consulta:


    OBTER /indexes/hotels/docs? pesquisa = hotel & searchFields = descrição, description_fr & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "hotel", "searchFields": "Descrição, description_fr"}

Observe que você só pode consultar um índice de cada vez. Não crie vários índices para cada idioma, a menos que você planeja consultar uma de cada vez.

7)  Paginação - obter a 1ª página de itens (o tamanho da página é 10):


    OBTER /indexes/hotels/docs? pesquisa = * & $skip = 0 & $top = 10 & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "*", "Ignorar": 0, "superior": 10}

8)  Paginação - obter a página 2a de itens (o tamanho da página é 10):


    OBTER /indexes/hotels/docs? pesquisa = * & $skip = 10 & $top = 10 & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "*", "Ignorar": 10, "superior": 10}

9)  Recupere um conjunto específico de campos:


    OBTER /indexes/hotels/docs? pesquisa = * & $select = hotelName, a descrição e a versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "*", "Selecione": "hotelName, descrição"}

10)  Recupere documentos que correspondam a uma expressão de filtro específicos:


    OBTER /indexes/hotels/docs? $filter = (baseRate ge 60 e baseRate lt 300) ou hotelName eq 'Fique decorativa' & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"filtro": "(baseRate ge 60 e baseRate lt 300) ou hotelName eq 'Permanecer decorativa'"}

11) Pesquisar os fragmentos de índice e retornar com realces de ocorrências


    OBTER /indexes/hotels/docs? pesquisa = algo & Realçar = descrição & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "algo", "realçar": "Descrição"}

12) Pesquisar o índice e retornar documentos classificados de local mais próximo de futuramente longe de uma referência


    OBTER /indexes/hotels/docs? pesquisa = algo & $orderby=geo.distance (local, geography'POINT(-122.12315 47.88121)') & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "algo", "orderby": "geo.distance (local, geography'POINT(-122.12315 47.88121)')"}

13) Pesquisar o índice presumindo que existe um perfil de pontuação chamado "geográfica" com duas funções de pontuação de distância, uma definição de um parâmetro chamado "currentLocation" e uma definição de um parâmetro chamado "lastLocation"


    OBTER /indexes/hotels/docs? pesquisa = algo & scoringProfile = geográfica & scoringParameter = currentLocation - 122.123,44.77233 & scoringParameter = lastLocation - 121.499,44.2113 & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "algo", "scoringProfile": "geográfica", "scoringParameters": ["currentLocation - 122.123,44.77233", "lastLocation - 121.499,44.2113"]}

14) Localize documentos no índice usando a [sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx). Esta consulta retorna hotéis onde campos pesquisáveis contenham os termos "conforto" e "local", mas não "bichos":


    OBTER /indexes/hotels/docs? pesquisa = conforto + local-bichos & searchMode = all & versão da api = 2015-02-28-Preview

    POSTAGEM /indexes/hotels/docs/search? versão da api = 2015-02-28-Preview {"search": "conforto + local-bichos", "searchMode": "todos"}

Observe o uso da `searchMode=all` acima. Incluindo esse parâmetro substitui o padrão de `searchMode=any`, garantindo que `-motel` significa "E não" em vez de "Ou não". Sem `searchMode=all`, você obtém "Ou não" que se expande ao invés de restringe os resultados da pesquisa e isso pode ser intuitivo ao contador alguns usuários.

15) Localize documentos no índice usando [lucene sintaxe de consulta](https://msdn.microsoft.com/library/mt589323.aspx). Essa consulta retornará hotéis onde o campo categoria contém o termo "orçamento" e todos os campos de pesquisa que contenham a frase "recentemente renovated". Documentos que contenham a frase "recentemente renovated" são classificados superior como resultado do valor de aumento de sensibilidade do termo (3)

    OBTER /indexes/hotels/docs?search =: orçamento de categoria e \"recentemente renovated\"^ 3 & searchMode = all & versão da api = 2015-02-28-Preview & querytype = completo

    POSTAGEM /indexes/hotels/docs/search?api-version = 2015-02-28-Preview {"search": ": orçamento de categoria e \"recentemente renovated\"^ 3", "queryType": "completa", "searchMode": "todos"}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Documento de pesquisa

A operação de **Pesquisa documento** recupera um documento de pesquisa do Azure. Isso é útil quando um usuário clica em um resultado de pesquisa específicos, e você deseja procurar detalhes específicos sobre esse documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **Pesquisa de documento** pode ser construída da seguinte maneira.

    GET /indexes/[index name]/docs/key?[query parameters]

Como alternativa, você pode usar a sintaxe de OData tradicional para pesquisa de chave:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

URI da solicitação inclui um [nome de índice] e [chave], especificando qual documento para recuperar de índice que. Você só pode obter um documento por vez. Use a **pesquisa** para obter vários documentos em uma única solicitação.

**Parâmetros de consulta**

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para recuperar. Se não for especificado ou definido como `*`, todos os campos marcados como recuperáveis no esquema são incluídos na projeção.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, o `api-version` especificado como um parâmetro de consulta.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo para o URL do serviço. A solicitação de **Pesquisa de documento** pode especificar uma chave admin ou tecla de consulta para `api-key`.

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemplo**

Pesquisa o documento que possui a chave '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Pesquisa o documento que possui a chave '3' usando a sintaxe de OData:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Contagem de documentos

A operação de **Documentos de contagem** recupera uma contagem do número de documentos em um índice de pesquisa. O `$count` sintaxe é parte do protocolo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **Contagem de documentos** pode ser construída usando o método GET.

O [nome do índice] na solicitação de URI informa o serviço para retornar uma contagem de todos os itens no conjunto de documentos do índice especificado.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Accept`: Este valor deve ser definido como `text/plain`.
- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo para o URL do serviço. A solicitação de **Contagem de documentos** pode especificar uma chave admin ou tecla de consulta para `api-key`.

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

Corpo da resposta contém o valor de contagem como um inteiro em texto sem formatação.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Sugestões

A operação de **sugestões** recupera sugestões com base na entrada de pesquisa parcial. Ele normalmente é usado nas caixas de pesquisa para fornecer sugestões com digitação antecipada como os usuários são inserir termos de pesquisa.

Solicitações de sugestão objetivo em sugerindo documentos de destino, para que o texto sugerido pode ser repetido se vários documentos de candidato correspondem a mesma pesquisa de entrada. Você pode usar `$select` para recuperar outros campos de documento (incluindo a chave de documento) para que você possa dizer qual documento é a fonte para cada sugestão.

Uma operação de **sugestões** é emitida como uma solicitação GET ou POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usar POST em vez de GET**

Quando você usa HTTP GET para ligar as **sugestões** API, você precisa estar ciente de que o comprimento da solicitação URL não pode exceder 8 KB. Isso geralmente é suficiente para a maioria dos aplicativos. Entretanto, alguns aplicativos produzem consultas muito grandes, especificamente expressões de filtro de OData. Para esses aplicativos, usando HTTP POST é a melhor opção, pois permite filtros maiores que GET. Postagem, o número de cláusulas em um filtro é o fator de limitação, não o tamanho da cadeia de caracteres filtro bruto desde que o limite de tamanho de solicitação POST é de aproximadamente 16 MB.

> [AZURE.NOTE] Apesar do limite de tamanho de solicitação de POSTAGEM for muito grande, expressões de filtro não podem ser absolutamente complexas. Consulte [sintaxe de expressões de OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de filtro.

**Solicitação**

HTTPS é necessário para solicitações de serviço. A solicitação de **sugestões** pode ser construída usando os métodos GET ou POST.

URI da solicitação especifica o nome do índice a consulta. Parâmetros, como o termo de pesquisa parcialmente entrada, são especificados na sequência de consulta no caso de solicitações GET, e na solicitação de solicitações de corpo no caso de POSTAGEM.

Como prática recomendada ao criar solicitações GET, lembre-se de parâmetros de consulta específica de [codificar URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) ao chamar a API REST diretamente. Para operações de **sugestões** , isso inclui:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

Codificação de URL só é recomendado nos parâmetros de consulta acima. Se você inadvertidamente codificar a URL a cadeia de caracteres de consulta inteira (tudo após o?), solicitações interromperá.

Além disso, a codificação de URL só é necessário ao chamar a API REST diretamente usando GET. Nenhuma codificação de URL é necessário ao chamar **sugestões** usando POST, ou quando usar a [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que controla a codificação de URL para você.

**Parâmetros de consulta**

**Sugestões** aceita diversos parâmetros que fornecem critérios de consulta e também especificar o comportamento de pesquisa. Você fornecer que esses parâmetros na URL sequência de consulta ao chamar **sugestões** via GET e como propriedades JSON no corpo da solicitação ao chamar **sugestões** via POSTAGEM. A sintaxe para alguns parâmetros é um pouco diferente entre GET e POST. Essas diferenças são indicadas conforme aplicável abaixo:

`search=[string]`-o texto de pesquisa para usar para sugerir consultas. Deve ser pelo menos 1 caractere e não mais do que 100 caracteres.

`highlightPreTag=[string]`(opcional) - uma cadeia de marca que precede para pesquisar ocorrências. Deve ser definido com `highlightPostTag`.

> [AZURE.NOTE] Quando chamar **sugestões** usando GET, caracteres reservados na URL deve ser porcentagem-codificado (por exemplo, % 23 em vez de #).

`highlightPostTag=[string]`(opcional) - uma cadeia de marca que acrescenta para pesquisar ocorrências. Deve ser definido com `highlightPreTag`.

> [AZURE.NOTE] Quando chamar **sugestões** usando GET, caracteres reservados na URL deve ser porcentagem-codificado (por exemplo, % 23 em vez de #).

`suggesterName=[string]`-o nome do suggester conforme especificado no `suggesters` conjunto que faz parte da definição de índice. A `suggester` determina quais campos são verificados para termos de consulta sugerido. Consulte [Suggesters](#Suggesters) para obter detalhes.

`fuzzy=[boolean]`(opcional, padrão = false)-quando definida como true, essa API encontrará sugestões mesmo se houver um caractere substituído ou ausente no texto de pesquisa. Enquanto isso proporciona uma experiência melhor em alguns cenários-tem um custo sugestão confusa pesquisas são mais lentas e consumam mais recursos de desempenho.

`searchFields=[string]`(opcional) - a lista de nomes de campo separados por vírgulas para pesquisar o texto de pesquisa especificada. Campos de destino devem estar habilitados para sugestões.

`$top=#`(opcional, padrão = 5)-o número de sugestões para recuperar. Deve ser um número entre 1 e 100.

> [AZURE.NOTE] Ao chamar **sugestões** usando POST, este parâmetro é chamado `top` em vez de `$top`.

`$filter=[string]`(opcional) - uma expressão que filtra os documentos considerados para sugestões.

> [AZURE.NOTE] Ao chamar **sugestões** usando POST, este parâmetro é chamado `filter` em vez de `$filter`.

`$orderby=[string]`(opcional) - uma lista de expressões separados por vírgulas para classificar os resultados por. Cada expressão pode ser um nome de campo ou uma chamada para a `geo.distance()` função. Cada expressão pode ser seguido por `asc` para indicado em ordem crescente, e `desc` para indicar decrescente. O padrão é ordem crescente. Há um limite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Ao chamar **sugestões** usando POST, este parâmetro é chamado `orderby` em vez de `$orderby`.

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para recuperar. Se não for especificado, somente o documento chave e sugestão texto será retornado. Você pode solicitar explicitamente todos os campos definindo esse parâmetro para `*`.

> [AZURE.NOTE] Ao chamar **sugestões** usando POST, este parâmetro é chamado `select` em vez de `$select`.

`minimumCoverage`(opcional, padrões para 80)-um número entre 0 e 100 indicando a porcentagem de índice que deve ser coberto por uma consulta de sugestões em ordem para a consulta para ser relatada como um sucesso. Por padrão, pelo menos 80% do índice deve estar disponível ou `Suggest` retornará o código de status HTTP 503. Se você definir `minimumCoverage` e `Suggest` é bem sucedida, ele irá retornar HTTP 200 e incluir uma `@search.coverage` valor na resposta indicando a porcentagem do índice que foi incluída na consulta.

> [AZURE.NOTE] Definir esse parâmetro para um valor menor que 100 pode ser útil para garantir a disponibilidade de pesquisa mesmo para os serviços com apenas uma réplica. No entanto, nem todas as sugestões de correspondência são garantia presentes nos resultados. Se o cancelamento é mais importante para o seu aplicativo disponibilidade, então é melhor não diminuir `minimumCoverage` abaixo de seu valor padrão de 80.

`api-version=[string]`(obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte o [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, o `api-version` especificado como um parâmetro de consulta na URL, independentemente se você chamar **sugestões** com GET ou POST.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais

- `api-key`: O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres, exclusivo para o URL do serviço. A solicitação de **sugestões** pode especificar uma chave admin ou chave de consulta como o `api-key`.

Você precisará também o nome do serviço para construir a URL da solicitação. Você pode obter o nome do serviço e `api-key` de seu painel de serviço no Portal do Azure. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para a navegação de página Ajuda.

**Corpo da solicitação**

Para obter: nenhum.

Para POSTAGEM:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Resposta**

Código de status: 200 Okey é retornado por uma resposta bem-sucedido.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se a opção de projeção é usada para recuperar campos eles serão incluídos em cada elemento da matriz:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemplo**

Recuperar 5 sugestões onde a entrada de pesquisa parcial é 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
