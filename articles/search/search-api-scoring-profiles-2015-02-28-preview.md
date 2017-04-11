<properties
    pageTitle="Pontuação perfis (Azure pesquisa restante versão da API de 2015-02-28-Preview) | Microsoft Azure | Visualização de pesquisa Azure API"
    description="Pesquisa Azure é um serviço de pesquisa de nuvem hospedado que suporta o ajuste dos resultados classificados com base em perfis de pontuação definidos pelo usuário."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Perfis de pontuação (pesquisa Azure restante versão da API de 2015-02-28-Preview)

> [AZURE.NOTE] Este artigo descreve os perfis pontuação no [2015-02-28-visualização](search-api-2015-02-28-preview.md). Atualmente não há nenhuma diferença entre o `2015-02-28` versão documentada no [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) e o `2015-02-28-Preview` versão descrito aqui, mas oferecemos este documento mesmo assim para fornecer cobertura de documento na API inteira.

## <a name="overview"></a>Visão geral

Pontuação refere-se a computação de um resultado de pesquisa para cada item retornado nos resultados da pesquisa. O resultado é um indicador de relevância de um item no contexto da operação de pesquisa atual. Maior pontuação, mais relevante no item. Nos resultados da pesquisa, os itens são ordenada de cima para baixo, com base na pontuação de pesquisa calculada para cada item de classificação.

Pesquisa Azure usa padrão de pontuação para calcular uma pontuação inicial, mas você pode personalizar o cálculo através de um perfil de pontuação. Perfis de pontuação oferecem um controle maior sobre a classificação de itens nos resultados da pesquisa. Por exemplo, você talvez queira impulsionar itens com base em seu potencial de receita, promover itens mais recentes ou talvez impulsionar itens que foram no estoque muito longa.

Um perfil de pontuação é parte da definição de índice, composta de campos, funções e parâmetros.

Para dar uma ideia de aparência de um perfil de pontuação, o exemplo a seguir mostra um perfil simples chamado 'geográfica'. Este aumenta itens que têm o termo de pesquisa na `hotelName` campo. Ele também usa o `distance` função para preferir itens que estão dentro de dez quilômetros do local atual. Se alguém procura o termo 'scritpt' e 'scritpt' acontece seja parte do nome do hotel, documentos que incluem hotéis com 'scritpt' aparecerá mais altos nos resultados da pesquisa.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Para usar este perfil pontuação, sua consulta é formulada para especificar o perfil na sequência de consulta. Na consulta abaixo, observe o parâmetro de consulta, `scoringProfile=geo` na solicitação.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Essa consulta pesquisa sobre o termo 'scritpt' e passa no local atual. Observe que essa consulta inclui outros parâmetros, tais como `scoringParameter`. Parâmetros de consulta são descritos nos [Documentos de pesquisa (API de pesquisa do Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Clique em [exemplo](#example) para revisar um exemplo mais detalhado de um perfil de pontuação.

## <a name="what-is-default-scoring"></a>O que é a pontuação de padrão?

Pontuação calcula uma pontuação de pesquisa para cada item em um conjunto de resultados ordenado ordem. Cada item em um conjunto de resultados de pesquisa é atribuído a uma pontuação de pesquisa, então classificados como maior para menor. Itens com as maiores pontuações são retornados para o aplicativo. Por padrão, o 50 superior são retornados, mas você pode usar o `$top` parâmetro para retornar um número maior ou menor de itens (até 1000 em uma única resposta).

Por padrão, uma pontuação de pesquisa é calculada com base em Propriedades estatísticas dos dados e a consulta. Pesquisa Azure localiza documentos que incluem os termos de pesquisa na sequência de consulta (algumas ou todas as, dependendo da maneira `searchMode`), favorecem documentos que contêm muitas instâncias do termo de pesquisa. A pontuação de pesquisa sobe ainda mais alta se o termo é raro entre o corpo de dados, mas comuns dentro do documento. Base para essa abordagem à relevância computação é conhecido como TF-IDF ou (frequência de frequência inverso documento termos).

Presumindo que não há nenhuma classificação personalizada, os resultados são, em seguida, classificados por pontuação de pesquisa antes de serem devolvidos para o aplicativo de chamada. Se `$top` não for especificada, 50 itens com a pesquisa mais alta pontuação é retornadas.

Os valores de pontuação de pesquisa podem ser repetidos em todo um conjunto de resultados. Por exemplo, você pode ter 10 itens com uma pontuação de 1.2, 20 itens com uma pontuação de 1.0 e 20 itens com uma pontuação de 0,5. Quando vários ataques têm a mesma pontuação de pesquisa, a ordenação dos mesmos itens marcados não está definida e não é estável. Executar a consulta novamente e você poderá ver itens shift posição. Considerando dois itens com uma pontuação idêntica, há nenhuma garantia que aparece em primeiro lugar.

## <a name="when-to-use-custom-scoring"></a>Quando usar pontuação personalizado

Você deve criar um ou mais perfis pontuação quando o padrão de comportamento de classificação não for suficiente em seus objetivos comerciais de reunião. Por exemplo, você pode decidir que relevância de pesquisa deve preferir itens recém-adicionado. Da mesma forma, você pode ter um campo que contém a margem de lucro, ou algum outro campo indicando o potencial de receita. Aumentar ocorrências trazem benefícios para sua empresa pode ser um fator importante decidir usar perfis de pontuação.

Baseada em relevância ordenação também é implementado por meio de perfis de pontuação. Considere a possibilidade de páginas de resultados de pesquisa que você usou no passado que permitem que você classificar por relevância, data, classificação ou preço. Em pesquisa do Azure, pontuação perfis unidade a opção 'relevância'. A definição de relevância é controlada por você, vinculada à objetivos comerciais e o tipo de experiência de pesquisa que você deseja entregar.

<a name="example"></a>
## <a name="example"></a>Exemplo

Como observado, pontuação personalizado implementado por meio de pontuação perfis definidos em um esquema de índice.

Este exemplo mostra o esquema de um índice com dois perfis de pontuação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta contra deste índice que inclua o perfil como um parâmetro de consulta utilizará o perfil para o conjunto de resultados de pontos.

[Experimente este exemplo](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Fluxo de trabalho

Para implementar o comportamento de pontuação personalizado, adicione um perfil de pontuação para o esquema que define o índice. Você pode ter vários perfis pontuação dentro de um índice, mas você só pode especificar um perfil ao tempo em qualquer consulta determinado.

Comece com o [modelo](#bkmk_template) fornecido neste tópico.

Forneça um nome. Perfis de pontuação são opcionais, mas se você adicionar um, o nome é necessário. Siga as convenções de nomenclatura para campos (começa com uma letra, evita caracteres especiais e palavras reservadas). Consulte [Regras de nomenclatura](http://msdn.microsoft.com/library/azure/dn857353.aspx) para obter mais informações.

O corpo do perfil pontuação é construído com campos ponderados e funções.

### <a name="weights"></a>Espessuras ###

O `weights` propriedade de um perfil de pontuação especifica pares de valor de nome que atribuir um peso relativo a um campo. No [exemplo](#example), os campos albumTitle, gênero e artistName são aumentadas 1,5, 5 e 2, respectivamente. Por que é gênero aumentado muito maior do que os outros? Se a pesquisa é conduzida sobre os dados que é um pouco homogêneos (como é o caso com 'gênero' no `musicstoreindex`), talvez seja necessário uma variação maior nos pesos relativos. Por exemplo, na `musicstoreindex`, 'Rocha' aparece como uma forma selecionada e em descrições de gênero idêntico escrito. Se você quiser gênero para superam descrição do gênero, o campo gênero será necessário muito peso relativo mais alto.

### <a name="functions"></a>Funções ###

Funções são usadas quando cálculos adicionais são necessários para contextos específicos. Tipos de válido de função são `freshness`, `magnitude`, `distance` e `tag`. Cada função tem parâmetros que são exclusivos para ele.

  - `freshness`deve ser usado quando você quiser impulsionar por como novo ou o antigo um item é. Essa função só pode ser usada com campos de datetime (`Edm.DataTimeOffset`). Observação a `boostingDuration` atributo é usado somente com a função de atualização.
  - `magnitude`deve ser usado quando você deseja impulsionar com base em como alta ou baixa um valor numérico. Cenários que ligar para essa função incluem aumentando a margem de lucro, preço mais alto, menor preço ou uma contagem de downloads. Você pode reverter o intervalo, de cima para baixo, se quiser que o inverso padrão (por exemplo, para itens de aumento de sensibilidade menor preço mais itens maior preço). Um determinado intervalo de preços de r $100 a r $1, você poderia definir `boostingRangeStart` em 100 e `boostingRangeEnd` em 1 para aumentar os itens menor preço. Essa função só pode ser usada com campos duplo e inteiro.
  - `distance`deve ser usado quando você quiser impulsionar por proximidade ou localização geográfica. Essa função só pode ser usada com `Edm.GeographyPoint` campos.
  - `tag`deve ser usado quando você quiser impulsionar por marcas em comum entre documentos e consultas de pesquisa. Essa função só pode ser usada com `Edm.String` e `Collection(Edm.String)` campos.
  
#### <a name="rules-for-using-functions"></a>Regras para usar funções ####

  - Tipo de função (atualização do, magnitude, distância, marca) deve ser letras minúsculas.
  - Funções não podem incluir valores nulos ou vazios. Especificamente, se você incluir fieldname, você precisa defini-lo para algo.
  - Funções só podem ser aplicadas aos campos filtráveis. Consulte [Create Index](search-api-2015-02-28-preview.md#CreateIndex) para obter mais informações sobre campos filtráveis.
  - Funções só podem ser aplicadas aos campos que são definidos no conjunto de campos de um índice.

Depois que o índice está definido, construa o índice carregando o esquema de índice, seguido de documentos. Consulte [Create Index](search-api-2015-02-28-preview.md#CreateIndex) e [Adicionar ou atualizar documentos](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) para obter instruções sobre essas operações. Depois que o índice é criado, você deve ter um perfil de pontuação funcional que funciona com seus dados de pesquisa.

<a name="bkmk_template"></a>
## <a name="template"></a>Modelo
Esta seção mostra a sintaxe e o modelo para pontuação perfis. Consulte a [referência de atributo de índice](#bkmk_indexref) na próxima seção para obter descrições dos atributos.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Referência de propriedades de perfil de pontuação

**Observação** Uma função pontuação só pode ser aplicada aos campos que estão filtráveis.

| Propriedade | Descrição |
|----------|-------------|
| `name`   | Necessário. Este é o nome do perfil de pontuação. Ela segue as mesmas convenções de nomenclatura de um campo. Ele deve começar com uma letra, não podem conter pontos, dois-pontos ou @ símbolos e não pode começar com a frase "azureSearch" (diferencia maiusculas de minúsculas). |
| `text` | Contém a propriedade de pesos. |
| `weights` | Opcional. Um par de nome-valor que especifica um nome de campo e a espessura relativa. Peso relativo deve ser um inteiro positivo ou o número de ponto flutuante. Você pode especificar o nome do campo sem um peso correspondente. Pesos são usados para indicar a importância de um campo em relação a outra. |
| `functions` | Opcional. Observe que uma função pontuação só pode ser aplicada aos campos que estão filtráveis. |
| `type` | Necessário para funções de pontuação. Indica o tipo da função usar. Os valores válidos incluem `magnitude`, `freshness`, `distance` e `tag`. Você pode incluir mais de uma função em cada perfil pontuação. O nome da função deve ser letras minúsculas. |
| `boost` | Necessário para funções de pontuação. Um número positivo usado como multiplicador para pontuação bruta. Ele não pode ser igual a 1. |
| `fieldName` | Necessário para funções de pontuação. Uma função pontuação só pode ser aplicada aos campos que fazem parte do conjunto de campo do índice, e que são filtráveis. Além disso, cada tipo de função apresenta restrições adicionais (atualização do é usada com campos de datetime, magnitude com campos inteiro ou duplo, distância com campos de localização e marca com a cadeia de caracteres ou campos do conjunto de cadeia de caracteres). Você só pode especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir dois magnitude definições, uma para cada campo. |
| `interpolation` | Necessário para funções de pontuação. Define a inclinação para o qual a pontuação aumentando aumenta desde o início do intervalo para o final do intervalo. Os valores válidos incluem `linear` (padrão), `constant`, `quadratic`, e `logarithmic`. Para obter detalhes, consulte [Definir interpolações](#bkmk_interpolation) . |
| `magnitude` | A magnitude pontuação função é usada para alterar a classificação com base no intervalo de valores de um campo numérico. Estes são alguns dos exemplos de uso mais comuns, isso:<ul><li>Classificações por estrelas: alterar a pontuação baseado no valor no campo "Classificação de estrela". Quando dois itens são relevantes, o item com a classificação maior será exibido primeiro.</li><li>Margem: Quando dois documentos relevantes, um revendedor poderá impulsionar documentos que tenham margens superiores primeiro.</li><li>Clique em contagens: para aplicativos que acompanham o clique por meio de ações para produtos ou páginas, você poderia usar magnitude aos itens de aumento de sensibilidade que tendem a obter o máximo de tráfego.</li><li>Baixar contagens: para aplicativos que downloads de controle, o permite de função magnitude você impulsionar itens que têm mais downloads.</li></ul> |
| `magnitude:boostingRangeStart` | Define o valor de início do intervalo que magnitude é marcou. O valor deve ser um número inteiro ou ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 1. Para mais de 50% de margens, isso seria 50. |
| `magnitude:boostingRangeEnd` | Define o valor do final do intervalo que magnitude é marcou. O valor deve ser um número inteiro ou ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 4. |
| `magnitude:constantBoostBeyondRange` | Valores válidos são true ou false (padrão). Quando definido como true, o aumento de sensibilidade completo continuará aplicar aos documentos que têm um valor para o campo de destino que é maior do que a extremidade superior do intervalo. Se for falso, o aumento de sensibilidade dessa função não será aplicado aos documentos tendo um valor para o campo de destino que esteja fora do intervalo. |
| `freshness` | A atualização do pontuação função é usada para alterar a pontuação de classificação de itens com base em valores nos campos de DateTimeOffset. Por exemplo, um item com uma data mais recente pode ser classificado superiores a itens mais antigos. (Observe que também é possível classificadas itens como eventos de calendário com datas futuras, de forma que os itens mais próximo ao presente podem ser classificados como superiores itens ainda mais no futuro.) Na versão atual do serviço, uma extremidade do intervalo será corrigida para a hora atual. A outra extremidade é um tempo no passado com base na `boostingDuration`. Para aumentar um intervalo de horários no futuro use um negativo `boostingDuration`. A taxa na qual o aumentando muda de máximo e mínimo da faixa é determinado pela interpolação aplicada ao perfil pontuação (consulte a figura a seguir). Para inverter o fator de aumento aplicado, escolha um fator de aumento de menor que 1. |
| `freshness:boostingDuration` | Define um período de expiração após quais aumentando interromperá para um documento específico. Consulte [conjunto boostingDuration] [#bkmk_boostdur] na seção a seguir para sintaxe e exemplos. |
| `distance` | A distância função pontuação é usada para afetam a pontuação de documentos com base em como fecha ou muito estão em relação uma localização geográfica de referência. O local de referência é fornecido como parte da consulta em um parâmetro (usando o `scoringParameter` parâmetro de consulta) como um lon, lat argumento. |
| `distance:referencePointParameter` | Um parâmetro a serem transmitidos em consultas para usar como local de referência. scoringParameter é um parâmetro de consulta. Consulte [Procurar documentos](search-api-2015-02-28-preview.md#SearchDocs) para descrições dos parâmetros de consulta. |
| `distance:boostingDistance` | Um número que indica a distância em quilômetros do local referência onde o intervalo de aumento termina. |
| `tag` | A função de pontuação de marca é usada para afetam a pontuação de documentos com base em marcas em documentos e consultas de pesquisa. Documentos com marcas em comum com a consulta de pesquisa serão ser aumentou. As marcas para a consulta de pesquisa é fornecido como um parâmetro pontuação em cada solicitação de pesquisa (usando o `scoringParameter` parâmetro de consulta). |
| `tag:tagsParameter` | Um parâmetro a serem transmitidos em consultas para especificar marcas para uma determinada solicitação. `scoringParameter`é um parâmetro de consulta. Consulte [Procurar documentos](search-api-2015-02-28-preview.md#SearchDocs) para descrições dos parâmetros de consulta. |
| `functionAggregation` | Opcional. Se aplica somente quando funções são especificadas. Os valores válidos incluem: `sum` (padrão), `average`, `minimum`, `maximum`, e `firstMatching`. Uma pontuação de pesquisa é um único valor que é calculado de diversas variáveis, incluindo várias funções. Este atributos indica como a aumenta de todas as funções é combinadas em um aumento de agregação único que é aplicado à pontuação documento base. A pontuação básica baseia-se no valor tf-idf calculado a partir do documento e a consulta de pesquisa. |
| `defaultScoringProfile` | Ao executar uma solicitação de pesquisa, se nenhum perfil pontuação for especificada, então a pontuação de padrão é usado (tf-idf somente). Um padrão de nome do perfil de pontuação pode ser definido aqui, causando a pesquisa do Azure para usar esse perfil quando nenhum perfil específico for fornecido na solicitação de pesquisa. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Definir interpolações

Interpolações permitem que você defina a inclinação para o qual a pontuação aumentando aumenta desde o início do intervalo para o final do intervalo. As seguintes interpolações podem ser usadas:

  - `Linear`: Para itens que estão dentro do intervalo máximo e mínimo, o aumento de sensibilidade aplicado ao item será feito em uma quantidade constantemente decrescente. Linear é a interpolação padrão para um perfil de pontuação.
  - `Constant`: Para itens que estão dentro do início e final intervalo, um aumento constante será aplicado aos resultados da classificação.
  - `Quadratic`: Em comparação a uma interpolação Linear que tenha um aumento constantemente decrescente, quadrática inicialmente diminuirá em ritmo menor e, em seguida, como ele aproxima o intervalo de término, diminui em um intervalo muito maior. Essa opção de interpolação não é permitida na marca pontuação funções.
  - `Logarithmic`: Em comparação a uma interpolação Linear que tenha um aumento constantemente decrescente, logarítmica inicialmente diminuirá em ritmo superior e, em seguida, como ele aproxima o intervalo de término, diminui em um intervalo muito menor. Essa opção de interpolação não é permitida na marca pontuação funções.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Definir boostingDuration

`boostingDuration`é um atributo da função atualização. Você pode usá-lo para definir uma expiração ponto depois que aumentando interromperá para um documento específico. Por exemplo, para aumentar a uma linha de produto ou marca por um período promocional 10 dias, você poderia especificar o período de 10 dias como "P10D" para esses documentos. Ou para impulsionar especificam eventos futuros na próxima semana "-P7D".

`boostingDuration`deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um valor de duração de ISO 8601). O padrão para isso é: `[-]P[nD][T[nH][nM][nS]]`.

A tabela a seguir fornece vários exemplos.

| Duração | boostingDuration |
|----------|------------------|
| 1 dia | "P1D" |
| 2 dias e 12 horas | "P2DT12H" |
| 15 minutos | "PT15M" |
| 30 dias, 5 horas, 10 minutos e 6.334 segundos | "P30DT5H10M6.334S" |

Para obter mais exemplos, consulte [esquema XML: tipos de dados (w3 no site)](http://www.w3.org/TR/xmlschema11-2/).

**Consulte também**
[API REST do serviço de pesquisa Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN <br/>
[Criar índice (API de pesquisa Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) no MSDN<br/>
[Adicionar um perfil de pontuação para um índice de pesquisa](http://msdn.microsoft.com/library/azure/dn798928.aspx) no MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
