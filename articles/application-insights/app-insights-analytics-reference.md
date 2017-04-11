<properties 
    pageTitle="Referência na análise no aplicativo ideias | Microsoft Azure" 
    description="Referência de instruções em análise, a ferramenta de pesquisa avançada de obtenção de informações do aplicativo. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Referência do Analytics

[Análise](app-insights-analytics.md) é o recurso de pesquisa avançada de [Obtenção de informações do aplicativo](app-insights-overview.md). Estas páginas descrevem a linguagem de consulta de análise.

> [AZURE.NOTE] [Unidade de teste Analytics em nossos dados simulados](https://analytics.applicationinsights.io/demo) se seu aplicativo não estiver enviando dados de obtenção de informações do aplicativo ainda.

## <a name="index"></a>Índice


**Permitir que** [Permitir que](#let-clause)


**Operadores e consultas** [contagem](#count-operator)  |  [avaliar](#evaluate-operator) | [estender](#extend-operator) | [junção](#join-operator) | [limite](#limit-operator) | [mvexpand](#mvexpand-operator) | [Analisar](#parse-operator) | [projeto](#project-operator) | [projeto ausente](#project-away-operator) | [intervalo](#range-operator) | [reduzir](#reduce-operator) | [renderizar diretiva](#render-directive) | [restringir cláusula](#restrict-clause) | [Classificar](#sort-operator) | [Resumir](#summarize-operator) | [levar](#take-operator) | [superior](#top-operator) | [aninhadas início](#top-nested-operator) | [união](#union-operator) | [onde](#where-operator) | [onde-in](#where-in-operator)

**Agregações** [qualquer](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [contagem](#count) | [CONT.se](#countif) | [BDCONTAR](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [Máx](#max) | [min](#min) | [percentil](#percentile) | [percentuais](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [DESVPAD](#stdev) | [soma](#sum) | [variação](#variance)

**Escalares** [Booleanos literais](#boolean-literals)  |  [Operadores booleanos](#boolean-operators) | [projeções](#casts) | [comparações escalares](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Números** [Operadores aritméticos](#arithmetic-operators)  |  [Literais numéricos](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [plantas](#floor) | [gama](#gamma) | [log](#log) | [rand](#rand) | [raiz](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Data e hora** [Data e hora expressões](#date-and-time-expressions)  |  [Data e hora literais](#date-and-time-literals) | [atrás](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [agora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Cadeia de caracteres** [GUIDs](#guids)  |  [Ofuscadas literais de cadeia de caracteres](#obfuscated-string-literals) | [Cadeia de caracteres literais](#string-literals) | [comparações](#string-comparisons) | [countof](#countof) | [extrair](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [Substituir](#replace) | [Dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadeia](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrizes, objetos e dinâmico** [Literais Array e object](#array-and-object-literals)  |  [Funções de objeto dinâmico](#dynamic-object-functions) | [objetos dinâmicos em cláusulas permitem que](#dynamic-objects-in-let-clauses) | [caminho de JSON expressões](#json-path-expressions) | [nomes](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervalo](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Permitir que

### <a name="let-clause"></a>permitir que a cláusula

**Tabular permitem - nomear uma tabela**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Escalar permitem - um valor de nomenclatura**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda permitem - uma função de nomenclatura**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Uma cláusula permitem que vincula um [nome](#names) a um resultado tabular, um valor escalar ou uma função. A cláusula é um prefixo a uma consulta e o escopo da vinculação é essa consulta. (Deixe não oferece uma maneira itens de nome que você usa mais tarde na sessão.)

**Sintaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *tipo:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:* Uma consulta não prefixada por uma cláusula permitem.

**Exemplos**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Associação a mesmo:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Operadores e consultas

Uma consulta sobre sua telemetria consiste em uma referência a um fluxo de origem, seguido por um pipeline de filtros. Por exemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro precedido pelo caractere de barra vertical `|` é uma instância de um *operador*, com alguns parâmetros. A entrada para o operador é a tabela que é o resultado do pipeline anterior. Na maioria dos casos, todos os parâmetros são [expressões escalares](#scalars) sobre as colunas da entrada. Em alguns casos, os parâmetros são os nomes das colunas de entrada e, em alguns casos, o parâmetro é uma segunda tabela. O resultado de uma consulta é sempre uma tabela, mesmo se tiver apenas uma coluna e uma linha.

Consultas podem conter quebras de linha única, mas são encerradas por uma linha em branco. Eles podem conter comentários entre `//` e no final da linha.

Uma consulta pode ser prefixada por um ou mais [permitem cláusulas](#let-clause), que definem escalares, tabelas ou funções que podem ser usadas dentro da consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`é usado em exemplos de consulta abaixo para indicar a tabela de pipeline ou fonte anterior.
> 

### <a name="count-operator"></a>operador Count

O `count` operador retorna o número de registros (linhas) no conjunto de registros de entrada.

**Sintaxe**

    T | count

**Argumentos**

* *T*: os dados tabulares cujos registros serão contados.

**Retorna**

Essa função retorna uma tabela com um único registro e coluna do tipo `long`. O valor da célula única é o número de registros no *T*. 

**Exemplo**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>avaliar o operador

`evaluate`é um mecanismo de extensão que permite algoritmos especializados a ser anexado às consultas.

`evaluate`deve ser o último operador no pipeline de consulta (exceto para um possível `render`). Ele não deve aparecer no corpo de uma função.

[Avaliar autocluster](#evaluate-autocluster) | [avaliar cesta](#evaluate-basket) | [avaliar diffpatterns](#evaluate-diffpatterns) | [avaliar extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>Avaliar autocluster

     T | evaluate autocluster()

AutoCluster localiza padrões comuns de atributos discretos (dimensões) dos dados e irá reduzir os resultados da consulta original (seja 100 ou 100 linhas de k) para um pequeno número de padrões. AutoCluster foi desenvolvido para ajudar a analisar falhas (por exemplo, exceções, travamentos), mas potencialmente pode trabalhar em qualquer conjunto de dados filtrados. 

**Sintaxe**

    T | evaluate autocluster( arguments )

**Retorna**

AutoCluster retorna um conjunto de padrões que capturar partes dos dados com valores comuns compartilhados entre vários atributos discretos (geralmente pequeno). Cada padrão é representado por uma linha nos resultados. 

As duas primeiras colunas são a contagem e a porcentagem de linhas fora a consulta original que são capturados pelo padrão. As colunas restantes são da consulta original e seu valor é um valor específico da coluna ou ' *' indicando valores variáveis. 

Observe que os padrões não são separados: elas pode ser sobrepostos e geralmente não abrange a todas as linhas originais. Algumas linhas não podem vir em qualquer padrão.

**Dicas**

* Use `where` e `project` na conexão entrada para reduzir os dados apenas o que você está interessado.
* Quando você encontrar uma linha interessante, talvez você queira aprofundá-lo ainda mais adicionando seus valores específicos para seus `where` filtro.

**Argumentos (tudo opcional)**

* `output=all | values | minimal` 

    O formato dos resultados. As colunas de contagem e porcentagem sempre aparecem nos resultados. 

 * `all`-todas as colunas de entrada são saída
 * `values`-filtra colunas com apenas ' *' nos resultados
 * `minimal`-também filtra colunas que são idênticas para todas as linhas na consulta original. 


* `min_percent=`*duplo* (padrão: 1)

    A cobertura de porcentagem mínima das linhas geradas.

    Exemplo:`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int* (padrão: 25) 

    O número de sementes determina o número de pontos de pesquisa de local inicial do algoritmo. Em alguns casos, dependendo da estrutura dos dados, aumentando o número de sementes aumenta o número (ou qualidade) dos resultados por meio de espaço de pesquisa de aumento na relação de consulta mais lento. O argumento num_seeds tem diminuindo resultados em ambas as direções para diminuir abaixo de 5 irá obter melhorias de desempenho significativo e crescentes acima de 50 raramente gerar padrões adicionais.

    Exemplo:`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < duplas < 1*+ (padrão: 0,5)

    Fornece algum controle sobre o equilíbrio entre genérico (cobertura alta) e informativos (valores compartilhados muitos). Size_weight crescente geralmente reduz o número de padrões e cada padrão tende a cobrir uma porcentagem maior. Diminuir size_weight geralmente produz padrões mais específicas com cobertura de porcentagem menor e mais valores compartilhados. Os bastidores fórmula é uma média geométrica ponderada entre a pontuação de genérica normalizada e pontuação informativas com size_weight e size_weight 1 como os pesos. 

    Exemplo:`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*nome da coluna*

    Considera cada linha na entrada acordo com o peso especificado (por padrão a cada linha tem uma espessura de '1'), uso comum de uma coluna de peso é levar em amostragem de conta ou bucketing/agregação dos dados que já estão incorporados em cada linha.

    Exemplo:`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>Avaliar cesta

     T | evaluate basket()

Cesta localiza todos os padrões de frequentes de atributos discretos (dimensões) dos dados e retornará todos os padrões de frequentes que passado o limite de frequência na consulta original. Cesta certamente encontrará todos os padrões de frequentes nos dados, mas não garantimos que têm polinomial de tempo de execução. O tempo de execução da consulta é linear no número de linhas, mas em alguns casos pode ser exponencial do número de colunas (dimensões). Cesta baseia-se no algoritmo Apriori originalmente desenvolvido para mineração de dados de análise do carrinho. 

**Retorna**

Todos os padrões que aparecem em mais do que uma fração especificada (padrão 0,05) dos eventos.

**Argumentos (tudo opcional)**


* `threshold=`*0.015 < duplas < 1* (padrão: 0,05) 

    Define a proporção mínima das linhas a serem considerados frequente (padrões com proporção menor não serão retornados).

    Exemplo:`T | evaluate basket("threshold=0.02")`


* `weight_column=`*nome da coluna*

    Considera cada linha na entrada acordo com o peso especificado (por padrão a cada linha tem uma espessura de '1'), uso comum de uma coluna de peso é levar em amostragem de conta ou bucketing/agregação dos dados que já estão incorporados em cada linha.

    Exemplo: T | Avaliar basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (padrão: 5)

    Define o número máximo de dimensões uncorrelated por carrinho, limitado por padrão para diminuir o tempo de execução de consulta.


* `output=minimize` | `all` 

    O formato dos resultados. As colunas de contagem e porcentagem sempre aparecem nos resultados.

 * `minimize`-filtra colunas com apenas ' *' nos resultados.
 * `all`-todas as colunas de entrada são saída.




#### <a name="evaluate-diffpatterns"></a>Avaliar diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compara dois conjuntos de dados da mesma estrutura e localiza padrões de atributos discretos (dimensões) que caracterizam diferenças entre os dois conjuntos de dados. Diffpatterns foi desenvolvido para ajudar a analisar falhas (por exemplo por falhas comparando a falhas em um determinado período de tempo), mas potencialmente poderá encontrar as diferenças entre os dois conjuntos de dados da mesma estrutura. 

**Sintaxe**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Retorna**

Diffpatterns retorna um conjunto de padrões que capturar diferentes partes de dados em dois conjuntos (isto é um padrão de capturar uma grande porcentagem das linhas do primeiro conjunto de dados e baixa porcentagem das linhas no segundo conjunto) (geralmente pequeno). Cada padrão é representado por uma linha nos resultados.

As quatro primeiras colunas são a contagem e a porcentagem de linhas fora a consulta original que são capturados pelo padrão em cada conjunto, quinta coluna é a diferença (em pontos de porcentagem absoluta) entre os dois conjuntos. As colunas restantes são da consulta original e seu valor é um valor específico da coluna ou * significando valores variáveis. 

Observe que os padrões não são distintos: elas pode ser sobrepostos e geralmente não abrange a todas as linhas originais. Algumas linhas não podem vir em qualquer padrão.

**Dicas**

* Use onde e do project na conexão entrada para reduzir os dados apenas o que você está interessado.

* Quando você encontrar uma linha interessante, talvez você queira aprofundá-lo ainda mais adicionando seus valores específicos para filtrar where.

**Argumentos**

* `split=`*nome da coluna* (obrigatório)

    A coluna deve ter exatamente dois valores. Se necessário, crie uma coluna:

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*cadeia de caracteres*

    Informa o algoritmo apenas procure padrões que têm porcentagem superior no conjunto de dados de destino, o destino deve ser um dos dois valores da coluna de divisão.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < duplas < 1* (padrão: 0,05) 

    Define a diferença mínima padrão (proporção) entre os dois conjuntos.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    O formato dos resultados. As colunas de contagem e porcentagem sempre aparecem nos resultados. 

 * `minimize`-filtra colunas com apenas ' *' nos resultados
 * `all`-todas as colunas de entrada são saída

* `weight_column=`*nome da coluna*

    Considera cada linha na entrada acordo com o peso especificado (por padrão cada linha tem uma espessura de '1'). Um uso comum de uma coluna de peso é levar em amostragem de conta ou bucketing/agregação dos dados que já estão incorporados em cada linha.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>Avaliar extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns é usado para enriquecer uma tabela com várias colunas simples que são dinamicamente extraídas fora (semi) colunas estruturadas com base em seu tipo. Atualmente oferece suporte apenas, colunas json ambas as dinâmico e serialização de jsons de cadeia de caracteres.


* `max_columns=`*int* (padrão: 10) 

    O número de novas colunas adicionados é dinâmico e pode ser muito grande (na verdade, é o número de chaves distintas em todos os registros de json) para que estamos deve limitá-lo. As novas colunas são classificadas em ordem decrescente com base na sua frequência e até max_columns são adicionadas à tabela.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*duplo* (padrão: 10.0) 

    Outra maneira de limitar novas colunas ignorando colunas cuja frequência é menor que min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool* (padrão: verdadeiro) 

    Se verdadeiro o nome da coluna complexa será adicionado como um prefixo para os nomes de colunas extraído.


* `prefix_delimiter=`*cadeia de caracteres* (padrão: "_") 

    Se add_prefix = true esse parâmetro define o delimitador que será usado para concatenar os nomes das colunas de novo.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool* (padrão: falso) 

    Se verdadeiro as colunas (json) originais serão mantidas na tabela de saída.


* `output=query | table` 

    O formato dos resultados. 

 * `table`-A saída é a mesma tabela como recebido menos as colunas de entrada especificadas mais novas colunas que foram extraídas das colunas de entrada.
 * `query`-A saída é uma cadeia de caracteres que representa a consulta que você faria para obter o resultado como tabela. 




### <a name="extend-operator"></a>estender o operador

     T | extend duration = stopTime - startTime

Acrescente uma ou mais colunas calculadas a uma tabela. 


**Sintaxe**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* A tabela de entrada.
* *ColumnName:* O nome de colunas para adicionar. [Nomes](#names) diferenciam maiusculas de minúsculas e podem conter ordem alfabética, numérica ou '_' caracteres. Use `['...']` ou `["..."]` citar palavras-chave ou nomes com outros caracteres.
* *Expressão:* Um cálculo sobre as colunas existentes.

**Retorna**

Uma cópia da tabela de entrada, com as colunas adicionais especificadas.

**Dicas**

* Use [`project`](#project-operator) em vez disso, se você também quiser soltar ou renomear algumas colunas.
* Não use `extend` simplesmente para obter um nome mais curto para usar em uma expressão longa. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    As colunas nativas da tabela foram indexadas; o novo nome define uma coluna adicional que não está indexada, portanto, a consulta é provavelmente executado mais lentamente.

**Exemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>operador de junção

    Table1 | join (Table2) on CommonColumn

Mescla as linhas das duas tabelas por valores correspondentes da coluna especificada.


**Sintaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Tabela1* - a 'lado esquerdo' da junção.
* *Tabela2* - 'lado direito' da junção. Pode ser uma expressão de consulta aninhada que gera uma tabela.
* *CommonColumn* - uma coluna que tem o mesmo nome nas duas tabelas.
* *Tipo* - Especifica como linhas das duas tabelas devem ser correspondido.

**Retorna**

Uma tabela com:

* Uma coluna para cada coluna em cada uma das duas tabelas, incluindo as chaves correspondentes. As colunas do lado direito serão automaticamente renomeadas se houver conflitos de nome.
* Uma linha para cada correspondência entre as tabelas de entrada. Uma correspondência é uma linha selecionada de uma tabela que tem o mesmo valor para todas as `on` campos como uma linha em outra tabela. 

* `Kind`não especificado

    Somente uma linha do lado esquerdo sejam correspondentes para cada valor da `on` chave. A saída contém uma linha para cada correspondência desta linha com linhas da direita.

* `Kind=inner`
 
     Há uma linha na saída para todas as combinações de linhas correspondentes da esquerda e direita.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Além das correspondências internas, há uma linha para cada linha na esquerda (e/ou direita), mesmo não se tiver nenhuma correspondência. Nesse caso, as células de saída não coincidentes contêm valores nulos.

* `kind=leftanti`

     Retorna todos os registros do lado esquerdo que não têm uma correspondência da direita. A tabela de resultados tem apenas as colunas do lado esquerdo. 
 
Se houver várias linhas com os mesmos valores para esses campos, você receberá linhas para todas as combinações.

**Dicas**

Para obter melhor desempenho:

* Use `where` e `project` para reduzir o número de linhas e colunas nas tabelas de entrada, antes do `join`. 
* Se uma tabela é sempre menor do que a outra, use-o como lado esquerdo (obsoleta) da junção.
* As colunas para a correspondência de junção devem ter o mesmo nome. Use o operador de projeto se necessário renomear uma coluna em uma das tabelas.

**Exemplo**

Obter estendido atividades de um log no qual algumas entradas marca o início e fim de uma atividade. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>operador de limite

     T | limit 5

Retorna até o número especificado de linhas da tabela de entrada. Não há nenhuma garantia de quais registros são retornados. (Para retornar registros específicos, use [`top`](#top-operator).)

**Alias**`take`

**Sintaxe**

    T | limit NumberOfRows


**Dicas**

`Take`é uma maneira simple e eficiente para ver uma amostra de seus resultados quando você estiver trabalhando interativamente. Lembre-se de que ele não garante para produzir quaisquer linhas específicas ou para apresentá-los em uma ordem específica.

Há um limite implícito no número de linhas retornadas ao cliente, mesmo se você não usa `take`. Para tire esse limite, use o `notruncation` opção de solicitação de cliente.



### <a name="mvexpand-operator"></a>operador de mvexpand

    T | mvexpand listColumn 

Expande uma lista de uma célula (JSON) digitado dinâmico para que cada entrada tem uma linha separada. Todas as outras células em uma linha expandida são duplicadas. 

(Consulte também [`summarize makelist`](#summarize-operator) que executa a função oposta.)

**Exemplo**

Considere que a tabela de entrada é:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Olá"|{"chave": "valor"}|
|2|"mundo"|[0,1, "k", "v"]|

    mvexpand D

Resultado é:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Olá"|{"chave": "valor"}|
|2|"mundo"|0|
|2|"mundo"|1|
|2|"mundo"|"k"|
|2|"mundo"|"v"|


**Sintaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumentos**

* *ColumnName:* No resultado, matrizes na coluna nomeada são expandidos para várias linhas. 
* *ArrayExpression:* Uma expressão gerando uma matriz. Se este formulário é usado, uma nova coluna é adicionada e existente é preservada.
* *Nome:* Um nome para a nova coluna.
* *Typename:* Converte a expressão expandida para um tipo específico
* *RowLimit:* O número máximo de linhas gerado a partir de cada linha original. O padrão é 128.

**Retorna**

Várias linhas para cada um dos valores em qualquer matriz na coluna nomeada ou na expressão de matriz.

A coluna expandida sempre tem tipo dinâmico. Usar um cast como `todatetime()` ou `toint()` se quiser calcular ou agregar valores.

Dois modos de expansões de conjunto de propriedades são suportados:

* `bagexpansion=bag`: Os pacotes de propriedade serão expandidos em pacotes de propriedade de entrada única. Esta é a expansão padrão.
* `bagexpansion=array`: Os pacotes de propriedade serão expandidos em dois elementos `[` *chave*`,`*valor* `]` matriz estruturas, permitindo uniforme acesso às chaves e valores (bem como, por exemplo, executando uma agregação de contagem distinta sobre nomes de propriedade). 

**Exemplos**


    exceptions | take 1 
  	| mvexpand details[0]

Divide um registro de exceções em linhas para cada item no campo de detalhes.



### <a name="parse-operator"></a>operador de análise

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrai valores de uma cadeia de caracteres. Pode usar a correspondência de expressões simples ou regular.

**Sintaxe**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumentos**

* `T`: A tabela de entrada.
* `kind`: 
 * `simple`(padrão): a `Match` cadeias de caracteres são cadeias de caracteres sem formatação.
 * `relaxed`: se o texto não analisar como o tipo de uma coluna, a coluna é definida como null e a análise continua 
 * `regex`: o `Match` cadeias de caracteres são expressões regulares.
* `Text`: Uma coluna ou outra expressão que avalia ou pode ser convertido em uma cadeia de caracteres.
* *Correspondência:* Corresponder a próxima parte da cadeia de caracteres e descartá-lo.
* *Coluna:* Atribua a próxima parte da cadeia de caracteres a essa coluna. A coluna é criada se ele não existir.
* *Tipo:* Analisar a próxima parte da cadeia de caracteres como o tipo especificado, como int, data, duplo. 


**Retorna**

A tabela de entrada, estendida de acordo com a lista de colunas.

Os elementos na `with` cláusula são comparados com o texto de origem alternadamente. Cada elemento chews desativar um bloco de texto de origem: 

* Uma cadeia de caracteres literal ou expressão regular move o cursor correspondente pelo comprimento da correspondência.
* Em uma análise de regex, uma expressão regular pode usar o operador de minimização '?' assim que possível mover para a seguinte correspondência.
* Um nome de coluna com um tipo analisa o texto como o tipo especificado. A menos que tipo = reduzida, uma análise malsucedido invalida correspondentes ao padrão inteiro.
* Um nome de coluna sem um tipo ou com o tipo 'string', copia o número mínimo de caracteres para acessar a correspondência a seguir.
* ' *' Ignora o número mínimo de caracteres para acessar a correspondência a seguir. Você pode usar '*' no início e no final do padrão, ou depois de um tipo diferente de cadeia de caracteres, ou entre correspondências de cadeia de caracteres.

Todos os elementos em um padrão de análise devem corresponder corretamente; Caso contrário, nenhum resultado será produzido. A exceção a essa regra é que, ao tipo = reduzida, se uma variável de tipo de análise falhar, o restante da análise continuará.

**Exemplos**

*Simples:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | contador | apresentar | Ano
---|---|---|---
1 | 2 | SOCKS | 63

*Relaxada:*

Quando a entrada contém uma correspondência correta para cada coluna digitada, uma análise reduzida produz os mesmos resultados que uma análise simple. Mas se uma das colunas digitadas não analisar corretamente, uma análise reduzida continua a processar o restante do padrão, enquanto uma análise simple for interrompido e deixar gerar qualquer resultado.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | apresentar | Ano
---|---|---
1 |  SOCKS | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

recurso | fatia | bloquear | lançamento | anterior
---|---|---|---|---
Agendador | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>operador de projeto

    T | project cost=price*quantity, price

Selecione as colunas para incluir, renomear ou soltar e inserir novas colunas calculadas. A ordem das colunas no resultado é especificada pela ordem os argumentos. Apenas as colunas especificadas nos argumentos são incluídas no resultado: quaisquer outras pessoas na entrada são desconectadas.  (Consulte também `extend`.)


**Sintaxe**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* A tabela de entrada.
* *ColumnName:* O nome de uma coluna apareça na saída. Se não houver nenhuma *expressão*, uma coluna com esse nome deve aparecer na entrada. [Nomes](#names) diferenciam maiusculas de minúsculas e podem conter ordem alfabética, numérica ou '_' caracteres. Use `['...']` ou `["..."]` citar palavras-chave ou nomes com outros caracteres.
* *Expressão:* Expressão escalar opcional referenciando as colunas de entrada. 

    É legal para retornar uma nova coluna calculada com o mesmo nome de uma coluna existente na entrada.

**Retorna**

Uma tabela com coluna nomeada como argumentos e quantas linhas como a tabela de entrada.

**Exemplo**

O exemplo a seguir mostra os vários tipos de manipulação que pode ser executadas usando o `project` operador. A tabela de entrada `T` tem três colunas de tipo `int`: `A`, `B`, e `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>operador de projeto ausente

    T | project-away column1, column2, ...

Exclua colunas especificadas. O resultado contém todas as colunas de entrada, exceto aqueles que você nomear.

### <a name="range-operator"></a>operador de intervalo

    range LastWeek from ago(7d) to now() step 1d

Gera uma tabela única coluna de valores. Observe que ele não tem um pipeline de entrada. 

|LastWeek|
|---|
|09:10:04.627 de 2015-12-05|
|09:10:04.627 de 2015 12-06|
|...|
|09:10:04.627 de 2015-12-12|



**Sintaxe**

    range ColumnName from Start to Stop step Step

**Argumentos**

* *ColumnName:* O nome da coluna única na tabela de saída.
* *Iniciar:* O menor valor na saída.
* *Parar:* O maior valor que está sendo gerado a saída (ou um limite do valor mais alto, se as etapas de *etapa* sobre esse valor).
* *Etapa:* A diferença entre dois valores consecutivas. 

Os argumentos devem ser valores numéricos de data ou período de tempo. Eles não podem referenciar as colunas de qualquer tabela. (Se você deseja calcular o intervalo com base em uma tabela de entrada, use a [ *função*de intervalo](#range), talvez com o [operador de mvexpand](#mvexpand-operator).) 

**Retorna**

Uma tabela com uma única coluna chamada *ColumnName*, cujos valores são *Iniciar*, *Iniciar* + *etapa*, … até e incluindo *Parar*.

**Exemplo**  

```AIQL
range Steps from 1 to 8 step 3
```

Uma tabela com uma única coluna chamado `Steps` cujo tipo é `long` e cujos valores são `1`, `4`, e `7`.

**Exemplo**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Uma tabela de meia-noite no últimos sete dias. A função de compartimento (planta) reduz sempre para o início do dia.

**Exemplo**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Mostra como o `range` operador pode ser usado para criar um pequeno, o ad-hoc, a tabela de dimensão que é usada para apresentar zeros onde os dados de origem não tem valores.

### <a name="reduce-operator"></a>reduzir o operador

    exceptions | reduce by outerMessage

Tenta agrupar registros semelhantes. Para cada grupo, o operador saídas a `Pattern` ele considera melhor descreve esse grupo e o `Count` de registros desse grupo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName:* A coluna para examinar. Ele deve ser do tipo cadeia de caracteres.
* *Limite:* Um valor no intervalo {de 0 a 1}. Padrão é 0.001. Para grandes entradas, limite deve ser pequeno. 

**Retorna**

Duas colunas, `Pattern` e `Count`. Em muitos casos, o padrão será um valor completo da coluna. Em alguns casos, ele pode identificar termos comuns e substituir as partes variáveis com ' *'.

Por exemplo, o resultado de `reduce by city` podem incluir: 

|Padrão | Contagem |
|---|---|
| SAN * | 5182 |
| Santa * | 2846 |
| Moscow | 3726 |
| \*-on-\* | 2730 |
| Paris | 27163 |


### <a name="render-directive"></a>renderizar diretiva

    T | render [ table | timechart  | barchart | piechart ]

Renderização direciona a camada de apresentação como Mostrar tabela. Ele deve ser o último elemento da conexão. É uma alternativa conveniente para usando os controles na exibição, permitindo que você salvar uma consulta com um método de apresentação específica.

### <a name="restrict-clause"></a>restringir a cláusula 

Especifica o conjunto de nomes de tabela disponíveis para operadores que acompanha. Por exemplo:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>operador de classificação 

    T | sort by country asc, price desc

Classifique as linhas da tabela de entrada em ordem por uma ou mais colunas.

**Alias**`order`

**Sintaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* A tabela de entrada para classificar.
* *Coluna:* Coluna de *T* pelo qual classificar. O tipo de valores deve ser numérico, data, hora ou cadeia de caracteres.
* `asc`Classificar por em ordem crescente, baixo como alto. O padrão é `desc`, em ordem decrescente de alto para baixo.

**Exemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas as linhas de rastreamentos de tabela que tem um determinado `ActivityId`, classificados por seu carimbo de hora.

### <a name="summarize-operator"></a>Resumir operador

Gera uma tabela que agrega o conteúdo da tabela de entrada.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Uma tabela que mostra o número, a duração da solicitação médio e o conjunto de cidades em cada país/região. Há uma linha na saída para cada país distintos. As colunas de saída mostram a contagem, a duração média, o cidades e o país/região. Todas as outras colunas de entrada são ignoradas.


    T | summarize count() by price_range=bin(price, 10.0)

Uma tabela que mostra quantos itens tem preços em cada intervalo [0,10.0], [10.0,20.0], e assim por diante. Este exemplo tem uma coluna para a contagem e outra para o intervalo de preços. Todas as outras colunas de entrada são ignoradas.


**Sintaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Coluna:* Nome opcional de uma coluna de resultado. Padrões para um nome derivam de expressão. [Nomes](#names) diferenciam maiusculas de minúsculas e podem conter ordem alfabética, numérica ou '_' caracteres. Use `['...']` ou `["..."]` citar palavras-chave ou nomes com outros caracteres.
* *Agregação:* Uma chamada para uma função de agregação como `count()` ou `avg()`, com nomes de coluna como argumentos. Consulte [agregações](#aggregations).
* *GroupExpression:* Uma expressão sobre as colunas, que fornece um conjunto de valores distintos. Normalmente é qualquer um nome de coluna que já fornece um conjunto restrito de valores, ou `bin()` com uma coluna numérica ou tempo como argumento. 

Se você fornecer uma expressão numérica ou tempo sem usar `bin()`, Analytics aplica automaticamente com um intervalo de `1h` tempos, ou `1.0` para números.

Se você não fornecer um *GroupExpression,* a tabela inteira é resumida em uma linha de saída simples.



**Retorna**

As linhas de entrada são organizadas em grupos com os mesmos valores da `by` expressões. Em seguida, as funções de agregação especificada são calculadas ao longo de cada grupo, produzir uma linha para cada grupo. O resultado contém o `by` colunas e também pelo menos uma coluna para cada computado agregação. (Algumas funções de agregação retornam várias colunas.)

O resultado tem quantas linhas existem diferentes combinações de `by` valores. Se desejar resumir sobre intervalos de valores numéricos, use `bin()` para reduzir os intervalos de valores distintos.

**Observação**

Embora você possa fornecer expressões aleatório para agregação e expressões de agrupamento, é mais eficiente para usar nomes de coluna simples ou aplicar `bin()` para uma coluna numérica.



### <a name="take-operator"></a>levar o operador

Alias do [limite](#limit-operator)


### <a name="top-operator"></a>operador Top

    T | top 5 by Name desc nulls first

Retorna os registros de *N* primeiro classificados por colunas especificadas.


**Sintaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argumentos**

* *NumberOfRows:* O número de linhas de *T* para retornar.
* *Sort_expression:* Uma expressão pela qual classificar as linhas. Normalmente é apenas um nome de coluna. Você pode especificar mais de um sort_expression.
* `asc`ou `desc` (o padrão) pode aparecer para controlar se seleção é realmente do "inferior" ou "início" do intervalo.
* `nulls first`ou `nulls last` controles onde valores nulos são exibidos. `First`é o padrão para `asc`, `last` é o padrão para `desc`.


**Dicas**

`top 5 by name`superficialmente equivale ao `sort by name | take 5`. No entanto, ele é executado com mais rapidez e sempre retorna classificados resultados, enquanto `take` não torna nenhuma garantia de tal.

### <a name="top-nested-operator"></a>operador de início aninhadas

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Produz resultados hierárquicos, onde cada nível é uma busca detalhada do nível anterior. É útil para responder a perguntas que pareça "quais são as solicitações de 5 superiores, para cada um deles, quais são as classificações de alto 3 desempenho e para cada um deles, quais são os países 3 principais as solicitações vêm?"

**Sintaxe**

   T | aninhadas superior da coluna N por agregação [,...]

**Argumentos**

* N:int - número de linhas para retornar ou passar ao próximo nível. Em uma consulta com três níveis onde N é 3, 3 e 5, o número total de linhas será 45.
* COLUNA - uma coluna para agrupar por para agregação. 
* AGREGAÇÃO - uma [função de agregação](#aggregations) para aplicar a cada grupo de linhas. Os resultados destas agregações determinará os grupos superiores a ser exibido.


### <a name="union-operator"></a>operador de união

     Table1 | union Table2, Table3

Usa duas ou mais tabelas e retorna as linhas de todas elas. 

**Sintaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Tabela1*, *Tabela2* ...
 *  O nome de uma tabela, tais como `requests`, ou uma tabela definido em uma [cláusula de deixar](#let-clause); ou
 *  A expressão da consulta como`(requests | where success=="True")`
 *  Um conjunto de tabelas especificadas com um caractere curinga. Por exemplo, `e*` pode formar a união de todas as tabelas definidos no anterior cláusulas permitem que cujo nome começou com 'e', junto com a tabela 'exceções'.
* `kind`: 
 * `inner`-O resultado tem o subconjunto de colunas que são comuns a todas as tabelas de entrada.
 * `outer`-O resultado tem todas as colunas que ocorrem em qualquer uma das entradas. Células que não foram definidas por uma linha de entrada são definidas como `null`.
* `withsource=`*ColumnName:* Se especificado, a saída incluirá uma coluna chamada *ColumnName* cujo valor indica qual tabela de origem contribuiu cada linha.

**Retorna**

Uma tabela com quantas linhas quantos estão em todas as tabelas de entrada e como tantas colunas quantos são nomes de coluna exclusiva nas entradas.

**Exemplo**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
União de todas as tabelas cujos nomes começam "tt".


**Exemplo**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
O número de usuários distintos que têm produzidas seja um `exceptions` evento ou uma `traces` evento sobre o dia anterior. No resultado, na coluna 'SourceTable' indicará "Consulta" ou "Comando".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versão mais eficiente produz os mesmos resultados. Ele filtra cada tabela antes de criar a união.

### <a name="where-operator"></a>onde operador

     requests | where resultCode==200

Filtra uma tabela para o subconjunto de linhas que satisfaçam um predicado.

**Alias**`filter`

**Sintaxe**

    T | where Predicate

**Argumentos**

* *T:* A entrada tabular cujos registros deverão ser filtrados.
* *Predicado:* A `boolean` [expressão](#boolean) sobre as colunas de *T*. Ele é avaliado para cada linha de *T*.

**Retorna**

Linhas em *T* para o qual *predicado* é `true`.

**Dicas**

Para obter o desempenho mais rápido:

* **Comparações simples de uso** entre os nomes de coluna e constantes. ('Constante' significa constante sobre a tabela - portanto `now()` e `ago()` são Okey e então são valores escalares atribuídos usando um [ `let` cláusula](#let-clause).)

    Por exemplo, prefere `where Timestamp >= ago(1d)` para `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest termos primeiro**: se você tiver várias cláusulas conjoined com `and`, colocar primeiro as cláusulas que envolvem apenas uma coluna. Portanto `Timestamp > ago(1d) and OpId == EventId` é melhor do que o contrário.


**Exemplo**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros que não mais antigas do que 1 hora, vierem da origem chamada "Kuskus" e tem duas colunas do mesmo valor. 

Observe que podemos colocar a comparação entre duas colunas por último, pois não é possível utilizar o índice e força uma verificação.


### <a name="where-in-operator"></a>operador de onde

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Sintaxe**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Argumentos**

* `col`: Uma coluna na tabela.
* `expr1`…: Uma lista de expressões escalares.

Use `in` é usada para incluir apenas linhas na qual `col` é igual a uma das expressões `expr1...`.

Use `!in` para incluir apenas as linhas que `col` não é igual a qualquer uma das expressões `expr1...`.  


## <a name="aggregations"></a>Agregações

Agregações são funções usadas para combinar valores em grupos criados na [Resumir operação](#summarize-operator). Por exemplo, nessa consulta, dcount() é uma função de agregação:

    requests | summarize dcount(name) by success

### <a name="any"></a>qualquer 

    any(Expression)

Aleatoriamente seleciona uma linha de grupo e retorna o valor da expressão especificada.

Isso é útil, por exemplo, quando alguns coluna tem um grande número de valores semelhantes (por exemplo, uma coluna de "texto de erro") e você quiser dessa coluna uma vez por um valor exclusivo da chave de grupo composto de exemplo. 

**Exemplo**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Localiza uma linha no grupo minimiza/maximises *ExprToMaximize*e retorna o valor de *ExprToReturn* (ou `*` para retornar a linha inteira).

**Dica**: as colunas passado-through são renomeadas automaticamente. Para verificar se você estiver usando os nomes certos, inspecionar os resultados usando `take 5` antes de você conduza os resultados em outro operador.

**Exemplos**

Para cada nome de solicitação, mostra quando ocorreu a solicitação mais longa:

    requests | summarize argmax(duration, timestamp) by name

Mostra todos os detalhes da solicitação maior, não apenas o carimbo de hora:

    requests | summarize argmax(duration, *) by name


Encontre o menor valor de cada métrica, junto com seu carimbo de hora e outros dados:

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>AVG

    avg(Expression)

Calcula a média de *expressão* entre o grupo.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Retorna o mínimo esquema que admite todos os valores de *DynamicExpression*. 

O tipo de coluna do parâmetro deve ser `dynamic` -um conjunto de matriz ou propriedade. 

**Exemplo**

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` é usado para marcar onde você deve usar um índice numérico. Para este esquema, alguns caminhos válidos poderia ser (presumindo que esses índices de exemplo estão no intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Exemplo**

Vamos supor que a coluna de entrada tem três valores dinâmicos:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


O esquema resultante seria:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

O esquema nos diz que:

* O objeto raiz é um contêiner com quatro propriedades denominada x, y, z e t.
* A propriedade chamada "x", o que poderia ser do tipo "int" ou do tipo "string".
* A propriedade chamada "y" que poderia do tipo "double" ou outro contêiner com uma propriedade chamado "w" do tipo "string".
* O ``indexer`` palavra-chave indica que "z" e "t" são matrizes.
* Cada item na matriz "z" é um inteiro ou uma cadeia de caracteres.
* "t" é uma matriz de cadeias de caracteres.
* Cada propriedade é implicitamente opcional e qualquer array pode estar vazia.

##### <a name="schema-model"></a>Modelo de esquema

A sintaxe do esquema retornado é:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Eles são equivalentes a um subconjunto das anotações tipo TypeScript, codificada como um valor dinâmico. Em Typescript, o esquema de exemplo seria:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>Contagem

    count([ Predicate ])

Retorna uma contagem de linhas para os quais *predicado* for avaliado como `true`. Se nenhum *predicado* for especificada, retorna o número total de registros no grupo. 

**Dica de desempenho**: use `summarize count(filter)` em vez de`where filter | summarize count()`

> [AZURE.NOTE] Evite usar Count () para encontrar o número de solicitações, exceções ou outros eventos que ocorreram. Quando [amostragem](app-insights-sampling.md) estiver em operação, o número de pontos de dados mantidas na obtenção de informações de aplicativo será menor que o número de eventos originais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantidos.

### <a name="countif"></a>Cont.Se

    countif(Predicate)

Retorna uma contagem de linhas para os quais *predicado* for avaliado como `true`.

**Dica de desempenho**: use `summarize countif(filter)` em vez de`where filter | summarize count()`

> [AZURE.NOTE] Evite usar countif() para encontrar o número de solicitações, exceções ou outros eventos que ocorreram. Quando [amostragem](app-insights-sampling.md) estiver em operação, o número de pontos de dados será menor que o número de eventos reais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantidos.

### <a name="dcount"></a>BDCONTAR

    dcount( Expression [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* no grupo. (Para listar os valores distintos, use [`makeset`](#makeset).)

*Precisão*, se especificada, controla o equilíbrio entre a velocidade e a precisão.

 * `0`= o cálculo menos preciso e rápido.
 * `1`o padrão, que equilibra o tempo de cálculo e precisão; Erro de 0,8% sobre.
 * `2`= mais preciso e mais lento o cálculo; Erro de 0,4% sobre.

**Exemplo**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* de linhas no grupo ao qual *predicado* é verdadeira. (Para listar os valores distintos, use [`makeset`](#makeset).)

*Precisão*, se especificada, controla o equilíbrio entre a velocidade e a precisão.

 * `0`= o cálculo menos preciso e rápido.
 * `1`o padrão, que equilibra o tempo de cálculo e precisão; Erro de 0,8% sobre.
 * `2`= mais preciso e mais lento o cálculo; Erro de 0,4% sobre.

**Exemplo**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist

    makelist(Expr [ ,  MaxListSize ] )

Retorna um `dynamic` matriz (JSON) de todos os valores de *Expr* no grupo. 

* *MaxListSize* é um limite de inteiro opcional no número máximo de elementos retornados (o padrão é *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Retorna um `dynamic` matriz (JSON) do conjunto de valores distintos que *Expr* leva no grupo. (Dica: para contar apenas os valores distintos, use [`dcount`](#dcount).)
  
*  *MaxSetSize* é um limite de inteiro opcional no número máximo de elementos retornados (o padrão é *128*).

**Exemplo**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Consulte também o [ `mvexpand` operador](#mvexpand-operator) para a função oposta.


### <a name="max-min"></a>Max, min

    max(Expr)

Calcula o número máximo de *Expr*.
    
    min(Expr)

Calcula o mínimo de *Expr*.

**Dica**: Isso fornecerá o Mín ou Máx seu próprio - por exemplo, a maior ou menor preço. Mas, se quiser que outras colunas na linha - por exemplo, o nome do fornecedor com o menor preço - usam [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentil, percentuais, percentilew, percentilesw

    percentile(Expression, Percentile)

Retorna uma estimativa de *expressão* de percentil especificado no grupo. A precisão depende da densidade da população na região do percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Como `percentile()`, mas calcula um número de valores de percentil (que é mais rápido que calculando cada percentil individualmente).

    percentilew(Expression, WeightExpression, Percentile)

Percentil ponderada. Use este comando para dados previamente agregados.  `WeightExpression`é um inteiro que indica quantas linhas originais são representadas por cada linha agregada.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Como `percentilew()`, mas calcula um número de valores de percentil.

**Exemplos**


O valor de `duration` maior que 95% da amostra definida e menor do que 5% do conjunto de amostra, calculado para cada nome de solicitação:

    request 
  	| summarize percentile(duration, 95)
      by name

Omitir "por …" para calcular para a tabela inteira.

Calcule simultaneamente várias percentuais para nomes de solicitação diferente:

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Os resultados mostram que para o /Events/Index de solicitação, 5% das solicitações são respondidas menor que 2.44s, metade no 3.52s, e 5% são mais lentas que 6.85s.

Calcule estatísticas de vários:

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Percentuais ponderadas

Use as funções percentil ponderada em casos em que os dados foram previamente agregados. 

Por exemplo, digamos que seu aplicativo executa milhares de operações por segundo e você quiser saber sua latência. A solução mais simples seria gerar uma solicitação de obtenção de informações do aplicativo ou o evento personalizado para cada operação. Isso criaria muitas tráfego, embora amostragem adaptativa são efetivadas reduzi-la. Mas você decide implementar uma solução ainda melhor: você irá escrever algum código em seu aplicativo para agregar os dados antes de enviá-lo de obtenção de informações do aplicativo. Resumo de agregados será enviado em intervalos regulares, reduzindo a taxa de dados talvez alguns pontos por minuto.

Seu código leva um fluxo de medidas de latência em milissegundos. Por exemplo:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Ele conta as medidas os compartimentos a seguintes:`{ 10, 20, 30, 40, 50, 100 }`

Periodicamente, faz uma série de chamadas de TrackEvent, um para cada compartimento, com medidas personalizadas em cada chamada: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Em análise, você ver um desses grupos de eventos como esta:

`opCount` | `latency`| significado
---|---|---
8 | 10 | = 8 operações na Lixeira 10 MS
6 | 20 | = 6 operações na Lixeira 20 MS
3 | 30 | = 3 operações na Lixeira 30ms
1 | 40 | = 1 operações na Lixeira 40ms

Para obter uma imagem precisa da distribuição original de latências de evento, usamos `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Os resultados são os mesmos, como se tinha usamos simples `percentiles` no conjunto original de medidas.

> [AZURE.NOTE] Percentuais ponderadas não são aplicáveis aos [dados de amostra](app-insights-sampling.md), onde cada linha de amostra representa uma amostra aleatória de linhas originais, em vez de uma Lixeira. As funções de percentil simples são adequadas para dados de amostrados.

#### <a name="estimation-error-in-percentiles"></a>Erro de previsão em percentuais

A agregação de percentuais fornece um valor aproximado usando [T-resumo](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Alguns pontos importantes: 

* Os limites do erro estimativa variam de acordo com o valor do percentil solicitado. A melhor precisão é nas extremidades da [0..100] escala, percentuais 0 e 100 são a exato valores mínimo e máximo da distribuição. A precisão diminui gradualmente à parte central da escala. É pior na mediana e está limitado a 1%. 
* Limites de erro são observados na classificação, não no valor. Suponha que percentil (X, 50) retornado o valor de Xm. A estimativa garante que pelo menos 49% e no máximo 51% dos valores de X são menores que Xm. Não há nenhum limite teórico sobre a diferença entre Xm e real valor médio de X.

### <a name="stdev"></a>DESVPAD

     stdev(Expr)

Retorna o desvio padrão de *Expr* sobre o grupo.

### <a name="variance"></a>variação

    variance(Expr)

Retorna a variação de *Expr* sobre o grupo.

### <a name="sum"></a>soma

    sum(Expr)

Retorna a soma dos *Expr* sobre o grupo.                      


## <a name="scalars"></a>Escalares

[conversões](#casts) | [comparações](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Os tipos suportados são:

| Tipo      | Nomes adicionais   | Tipo equivalente do .NET |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Projeções

Você pode converter de um tipo para outro. Em geral, se a conversão faz sentido, ele funcionará:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Verificar se uma cadeia de caracteres pode ser convertida em um tipo específico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Comparações escalares

||
---|---
`<` |Menos
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 
`in`| Operando à direita é uma matriz (dinâmica) e operando esquerdo é igual a um de seus elementos.
`!in`| Operando à direita é uma matriz (dinâmica) e operando esquerdo não é igual a qualquer um dos seus elementos.




### <a name="gettype"></a>GetType

**Retorna**

Uma cadeia de caracteres que representa o tipo de armazenamento subjacente do argumento único. Isso é especialmente útil quando você tem valores tipo de `dynamic`: neste caso `gettype()` revela como um valor é codificado.

**Exemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hash

**Sintaxe**

    hash(source [, mod])

**Argumentos**

* *fonte*: A fonte escalar o hash é calculado no.
* *Mod*: O valor a ser aplicado no resultado hash de módulo.

**Retorna**

O valor de xxhash (por extenso) de escalar a determinado, módulo determinado valor mod (se especificado).

**Exemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

O `iff()` função avalia o primeiro argumento (o predicado) e retorna o valor da segunda ou terceira argumentos dependendo se o predicado é `true` ou `false`. O segundo e terceiro argumentos deve ser do mesmo tipo.

**Sintaxe**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicado:* Uma expressão que avalia uma `boolean` valor.
* *ifTrue:* Uma expressão que é avaliada e seu valor retornado da função se *predicado* será avaliado como `true`.
* *ifFalse:* Uma expressão que é avaliada e seu valor retornado da função se *predicado* será avaliado como `false`.

**Retorna**

Esta função retorna o valor de *ifTrue* se *predicado* for avaliado como `true`, ou o valor de *ifFalse* caso contrário.

**Exemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, notnull

    isnull(parsejson("")) == true

Leva um único argumento e informa se ele for nulo.

**Sintaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Retorna**

VERDADEIRO ou falso dependendo se o valor é nulo ou não nulo.


|x|IsNull(x)
|---|---
| "" | FALSO
|"x" | FALSO
|parseJSON("")|verdadeiro
|parseJSON("[]")|FALSO
|parseJSON("{}")|FALSO

**Exemplo**

    T | where isnotnull(PossiblyNull) | count

Observe que há outras maneiras de alcançar esse efeito:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Avalia uma consulta ou uma expressão e retorna o resultado como um único valor. Esta função é útil para cálculos em estágios; Por exemplo, calculando uma contagem total de eventos e, em seguida, usá-lo como uma linha de base.

**Sintaxe**

    toscalar(query)
    toscalar(scalar)

**Retorna**

O argumento avaliado. Se o argumento for uma tabela, retorna a primeira coluna da primeira linha. (Uma prática recomendada é organizar que o argumento tiver apenas uma coluna e linha.)

**Exemplo**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Booleanos literais

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Operadores booleanos

    and 
    or 

    

## <a name="numbers"></a>Números

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Literais numéricos

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Operadores aritméticos

|| |
|---|-------------|
| + | Adicionar         |
| - | Subtrair    |
| * | Multiplicar    |
| / | Dividir      |
| % | Módulo      |
||
|`<` |Menos
|`<=`|Menor ou igual a
|`>` |Maior
|`>=`|Maior ou igual a
|`<>`|Não é igual a
|`!=`|Não é igual a 


### <a name="abs"></a>Abs

**Sintaxe**

    abs(x)

**Argumentos**

* x - um inteiro, real ou período de tempo

**Retorna**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>compartimento, andar

Arredonda valores para baixo até um inteiro múltiplo de um tamanho de compartimento determinado. Muito usado na [`summarize by`](#summarize-operator) consulta. Se você tiver um conjunto disperso de valores, eles serão agrupados em um conjunto menor de valores específicos.

Alias `floor`.

**Sintaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumentos**

* *valor:* Um número, data ou período de tempo. 
* *roundTo:* O "tamanho de compartimento." Um número, data ou período de tempo que divide o *valor*. 

**Retorna**

O múltiplo mais próximo de *roundTo* abaixo de *valor*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemplos**

Expressão | Resultado
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


A expressão a seguir calcula um histograma de durações, com um tamanho de Balde de 1 segundo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>ARREDMULTB

Um alias para [`bin()`](#bin).

### <a name="gamma"></a>gama

A [função gama](https://en.wikipedia.org/wiki/Gamma_function)

**Sintaxe**

    gamma(x)

**Argumentos**

* *x:* Um número real

Para números inteiros positivos, `gamma(x) == (x-1)!` por exemplo, `gamma(5) == 4 * 3 * 2 * 1`.

Consulte também [loggamma](#loggamma).


### <a name="log"></a>log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`deve ser um número real > 0. Caso contrário, nulo será retornado.

### <a name="loggamma"></a>loggamma


O logaritmo natural do valor absoluto da [função gama](#gamma).

**Sintaxe**

    loggamma(x)

**Argumentos**

* *x:* Um número real


### <a name="rand"></a>aleatório

Um gerador de números aleatórios.

* `rand()`-um número real entre 0,0 e 1,0
* `rand(n)`-um número inteiro entre 0 e n-1




### <a name="sqrt"></a>raiz

A função raiz quadrada.  

**Sintaxe**

    sqrt(x)

**Argumentos**

* *x:* Um número real > = 0.

**Retorna**

* Um número positivo, como`sqrt(x) * sqrt(x) == x`
* `null`Se o argumento for negativo ou não pode ser convertido em um `real` valor. 




### <a name="toint"></a>toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Data e hora


[atrás](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [agora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Literais de data e hora

|||
---|---
**DateTime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Horários são sempre no UTC. Omitindo a data fornece uma hora hoje.
`now()`|A hora atual.
`now(`-*período de tempo*`)`|`now()-`*período de tempo*
`ago(`*período de tempo*`)`|`now()-`*período de tempo*
**período de tempo**|
`2d`|2 dias
`1.5h`|1,5 hora 
`30m`|30 minutos
`10s`|10 segundos
`0.1s`|segundo 0,1
`100ms`| 100 milissegundos
`10microsecond`|
`1tick`|100 NS
`time("15 seconds")`|
`time("2")`| 2 dias
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Expressões de data e hora

Expressão |Resultado
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Menos
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 




### <a name="ago"></a>atrás

Subtrai o período de tempo determinado da hora relógio UTC atual. Como `now()`, esta função pode ser usada várias vezes em uma instrução e a hora do relógio UTC está sendo referenciada será o mesmo para todas as instâncias.

**Sintaxe**

    ago(a_timespan)

**Argumentos**

* *a_timespan*: intervalo para subtrair a hora de relógio UTC atual (`now()`).

**Retorna**

    now() - a_timespan

**Exemplo**

Todas as linhas com um carimbo de hora na última hora:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Extrai uma parte especificada de uma data como um número inteiro.

**Sintaxe**

    datepart(part, datetime)

**Argumentos**

* `part:String`-{"Ano", "Mês", "Dia", "Hora", "Minuto", "Segundo", "Milissegundos", "Microssegundos", "Nanossegundos"}
* `datetime`

**Retorna**

Long que representa a parte especificada.


### <a name="dayofmonth"></a>DayOfMonth

    dayofmonth(datetime("2016-05-15")) == 15 

O número ordinal do dia do mês.

**Sintaxe**

    dayofmonth(a_date)

**Argumentos**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>DayOfWeek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

O número inteiro de dias desde anterior domingo, como um `timespan`.

**Sintaxe**

    dayofweek(a_date)

**Argumentos**

* `a_date`: A `datetime`.

**Retorna**

O `timespan` desde meia-noite no início do anterior domingo, arredondado para um número inteiro de dias.

**Exemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

O número ordinal do dia do ano.

**Sintaxe**

    dayofyear(a_date)

**Argumentos**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Obtenha o número do mês (1-12) do datetime.

**Exemplo**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>GetYear

Obter o ano de datetime.

**Exemplo**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>Agora

    now()
    now(-2d)

A hora atual do relógio UTC, opcionalmente deslocamento por um determinado período de tempo. Esta função pode ser usada várias vezes em uma instrução e a hora do relógio está sendo referenciada será o mesmo para todas as instâncias.

**Sintaxe**

    now([offset])

**Argumentos**

* *deslocamento:* A `timespan`, será adicionado para a hora de relógio UTC atual. Padrão: 0.

**Retorna**

A hora de relógio UTC atual como um `datetime`.

    now() + offset

**Exemplo**

Determina o intervalo desde o evento identificado pelo predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Verificar se uma cadeia de caracteres é uma data válida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

O resultado inteiro representa o número de semana pelo ISO 8601 padrão. O primeiro dia da semana é domingo, e a primeira semana do ano é a semana que contém quinta primeira do ano. (Os últimos dias de um ano, portanto, podem conter alguns dos dias da semana 1 do próximo ano ou os primeiros dias podem conter algumas das semana 52 ou 53 do ano anterior).


## <a name="string"></a>Cadeia de caracteres

[countof](#countof) | [extrair](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [Substituir](#replace) | [Dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadeia](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Cadeia de caracteres literais

As regras são iguais aos JavaScript.

Cadeias de caracteres podem ser colocadas em único ou caracteres de aspas duplas. 

Barra invertida (`\`) é usada para caracteres de escape como `\t` (guia), `\n` (nova linha) e instâncias do circunscrito caractere de aspas.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Ofuscadas cadeia de caracteres literais

Literais de cadeia de caracteres ofuscadas são cadeias de caracteres que Analytics escurecimento durante a cadeia de caracteres (por exemplo, quando o rastreamento) de saída. O processo de ofuscação substitui todos ofuscadas caracteres pela inicialização (`*`) caracteres.

Para formar uma cadeia de caracteres ofuscada literal, preceder `h` ou 'H'. Por exemplo:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparações de cadeia de caracteres

Operador|Descrição|Diferencia maiusculas de minúsculas|Exemplo de True
---|---|---|---
`==`|É igual a |Sim| `"aBc" == "aBc"`
`<>` `!=`|Não é igual a|Sim| `"abc" <> "ABC"`
`=~`|É igual a |Não| `"abc" =~ "ABC"`
`!~`|Não é igual a |Não| `"aBc" !~ "xyz"`
`has`|Direita-à lado (RHS) é um termo inteiro em à esquerda-à-LHS (lado)|Não| `"North America" has "america"`
`!has`|RHS não é um termo completo no LHS|Não|`"North America" !has "amer"` 
`hasprefix`|RHS é um prefixo de um termo no LHS|Não|`"North America" hasprefix "ame"`
`!hasprefix`|RHS não é um prefixo de qualquer termo no LHS|Não|`"North America" !hasprefix "mer"`
`hassuffix`|RHS é um sufixo de um termo no LHS|Não|`"North America" hassuffix "rth"`
`!hassuffix`|RHS não é um sufixo de qualquer termo no LHS|Não|`"North America" !hassuffix "mer"`
`contains` | RHS ocorre como uma subcadeia de LHS|Não| `"FabriKam" contains "BRik"`
`!contains`| RHS não ocorrer no LHS|Não| `"Fabrikam" !contains "xyz"`
`containscs` | RHS ocorre como uma subcadeia de LHS|Sim| `"FabriKam" contains "Kam"`
`!containscs`| RHS não ocorrer no LHS|Sim| `"Fabrikam" !contains "Kam"`
`startswith`|RHS é uma subcadeia inicial de LHS.|Não|`"Fabrikam" startswith "fab"`
`!startswith`|RHS não é uma subcadeia inicial de LHS.|Não|`"Fabrikam" !startswith "abr"`
`endswith`|RHS é uma subcadeia terminal de LHS.|Não|`"Fabrikam" endswith "kam"`
`!endswith`|RHS não é uma subcadeia terminal de LHS.|Não|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contém uma correspondência para RHS|Sim| `"Fabrikam" matches regex "b.*k"`
`in`|Igual a qualquer um dos elementos|Sim|`"abc" in ("123", "345", "abc")`
`!in`|Não é igual a qualquer um dos elementos|Sim|`"bc" !in ("123", "345", "abc")`

Use `has` ou `in` se você estiver testando a presença de um termo totalmente lexical - ou seja, um símbolo ou uma palavra alfanumérica delimitada por caracteres não alfanuméricos ou o início ou o fim do campo. `has`Efetua mais rápido que `contains`, `startswith` ou `endswith`. A primeira dessas consultas é executado mais rápido:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta as ocorrências de uma subcadeia de caracteres em uma cadeia. Correspondências de cadeia de caracteres sem formatação podem se sobrepor; correspondências Regex não.

**Sintaxe**

    countof(text, search [, kind])

**Argumentos**

* *texto:* Uma cadeia de caracteres.
* *pesquisa:* A cadeia de caracteres simples ou uma expressão regular para corresponder dentro do *texto*.
* *tipo:* `"normal"|"regex"` Padrão `normal`. 

**Retorna**

O número de vezes que a cadeia de caracteres de pesquisa pode ser correspondida no contêiner. Correspondências de cadeia de caracteres sem formatação podem se sobrepor; correspondências Regex não.

**Exemplos**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (não 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>extrair

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtenha uma correspondência para uma [expressão regular](#regular-expressions) de uma cadeia de texto. Opcionalmente, ele converte extraída subcadeia de caracteres para o tipo indicado.

**Sintaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* Uma [expressão regular](#regular-expressions).
* *captureGroup:* Um positivo `int` constante indicando o grupo de captura para extrair. 0 representa o inteiro correspondente, 1 para o valor correspondido na primeira '(' parêntese')' na expressão regular, 2 ou mais para parênteses subsequentes.
* *texto:* A `string` pesquisar.
* *typeLiteral:* Um literal de tipo opcional (por exemplo, `typeof(long)`). Se fornecido, extraída subcadeia de caracteres é convertida em desse tipo. 

**Retorna**

Se *regex* localiza uma correspondência de *texto*: a subcadeia de caracteres correspondida o grupo de captura indicado *captureGroup*, opcionalmente convertido em *typeLiteral*.

Se houver nenhuma correspondência ou falha de conversão de tipo: `null`. 

**Exemplos**

A cadeia de caracteres de exemplo `Trace` é pesquisado para uma definição para `Duration`. A correspondência é convertida em `real`, e em seguida, multiplicados-la por uma constante de tempo (`1s`) para que `Duration` for do tipo `timespan`. Neste exemplo, é igual a 123.45 segundos:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Este exemplo é equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notempty

    isempty("") == true

True se o argumento for uma cadeia de caracteres vazia ou é nulo.
Consulte também [isnull](#isnull).


**Sintaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Retorna**

Indica se o argumento é uma cadeia de caracteres vazia ou isnull.

|x|IsEmpty(x)
|---|---
| "" | verdadeiro
|"x" | FALSO
|parseJSON("")|verdadeiro
|parseJSON("[]")|FALSO
|parseJSON("{}")|FALSO


**Exemplo**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl

Divida uma URL em suas partes.

**Sintaxe**

    parseurl(urlstring)

**Argumentos**

* *urlstring:* UMA URL.

**Retorna**

Um objeto que contém as partes como cadeias de caracteres.

**Exemplo**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>Substituir

Substitua todas as correspondências regex com outra cadeia de caracteres.

**Sintaxe**

    replace(regex, rewrite, text)

**Argumentos**

* *regex:* A [expressão regular](https://github.com/google/re2/wiki/Syntax) para pesquisar *texto*. Ele pode conter grupos de captura no '('parênteses')'. 
* *regravação:* O regex de substituição para qualquer correspondência feita por *matchingRegex*. Use `\0` para consultar o inteiro correspondente, `\1` para o primeiro grupo de captura, `\2` e assim por diante para os grupos de captura subsequentes.
* *texto:* Uma cadeia de caracteres.

**Retorna**

*texto* após substituir todas as correspondências de *regex* com avaliações de *regravação*. Correspondências não se sobreponham.

**Exemplo**

Esta política:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Tem os seguintes resultados:

| x    | STR | substituído|
|---|---|---|
| 1    | Número é 1.000000  | Número foi: 1.000000|
| 2    | Número é 2.000000  | Número foi: 2.000000|
| 3    | Número é 3.000000  | Número foi: 3.000000|
| 4    | Número é 4.000000  | Número foi: 4.000000|
| 5    | Número é 5.000000  | Número foi: 5.000000|
 



### <a name="split"></a>Dividir

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide uma cadeia determinada de acordo com um determinado delimitador e retorna uma matriz de cadeia de caracteres com as subsequências conatined. Opcionalmente, uma subcadeia específica pode ser retornado se existe.

**Sintaxe**

    split(source, delimiter [, requestedIndex])

**Argumentos**

* *origem*: A cadeia de caracteres de origem que será dividida de acordo com o delimitador determinado.
* *delimitador*: O delimitador que será usado para dividir a cadeia de caracteres de origem.
* *requestedIndex*: um índice baseado em zero opcional `int`. Se fornecido, a matriz de cadeia de caracteres retornada conterá a subcadeia de caracteres solicitada se existe. 

**Retorna**

Uma matriz de cadeia de caracteres que contém as subsequências da cadeia de caracteres determinada fonte que são delimitadas por determinado delimitador.

**Exemplos**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatena entre 1 e 16 argumentos, que devem ser strings.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Comprimento de uma cadeia de caracteres.

### <a name="substring"></a>subcadeia de caracteres

    substring("abcdefg", 1, 2) == "bc"

Extrai uma subcadeia de uma cadeia de determinada fonte a partir de um determinado índice. Opcionalmente, o comprimento da subcadeia de caracteres solicitada pode ser especificado.

**Sintaxe**

    substring(source, startingIndex [, length])

**Argumentos**

* *origem:* A cadeia de caracteres de origem que a subcadeia de caracteres será extraída.
* *startingIndex:* Com base em zero caractere posição inicial da subcadeia de caracteres solicitada.
* *comprimento:* Um parâmetro opcional que pode ser usado para especificar o número de caracteres solicitado em subcadeia de caracteres. 

**Retorna**

Uma subcadeia de caracteres dada. A subcadeia inicia na posição de caractere startingIndex (com base em zero) e continua até o final dos caracteres a cadeia de caracteres ou comprimento se especificado.

**Exemplos**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>ToLower

    tolower("HELLO") == "hello"

Converte uma cadeia de caracteres em minúsculas.

### <a name="toupper"></a>ToUpper

    toupper("hello") == "HELLO"

Converte uma cadeia de caracteres para maiusculas.



### <a name="guids"></a>GUIDs

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Matrizes, objetos e dinâmico

[literais](#dynamic-literals) | [projeção](#casting-dynamic-objects) | [operadores](#operators) | [permitem cláusulas](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervalo](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Aqui é o resultado de uma consulta em uma exceção de obtenção de informações do aplicativo. O valor em `details` é uma matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indexação:** Matrizes de índice e objetos apenas como JavaScript:

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mas usar `arraylength` e outras funções de análise (não ".length"!)

**Projeção** Em alguns casos é necessário converter um elemento que você extrair de um objeto, porque seu tipo poderia variar. Por exemplo, `summarize...to` precisa de um tipo específico:

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literais** Para criar uma matriz explícita ou objeto de conjunto de propriedades, escreva-o como uma cadeia de caracteres JSON e cast:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Para separe as propriedades de um objeto em linhas separadas, use mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** Para encontrar todos os caminhos em um objeto complexo:

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** Para localizar o esquema mínimo que admite todos os valores da expressão na tabela:

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` é usado para marcar onde você deve usar um índice numérico. Para este esquema, alguns caminhos válidos poderia ser (presumindo que esses índices de exemplo estão no intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Literais Array e object

Para criar um literal dinâmico, use `parsejson` (alias `todynamic`) com um JSON string argumento:

* `parsejson('[43, 21, 65]')`-uma matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-um único valor de tipo dinâmico que contém um número
* `parsejson('"21"')`-um único valor de tipo dinâmico contendo uma cadeia de caracteres

Observe que, ao contrário de JavaScript, JSON exige o uso de aspas duplas (`"`) ao redor de cadeias de caracteres. Portanto, é geralmente mais fácil cotação uma cadeia de caracteres codificada como JSON literais usando aspas simples (`'`).

Este exemplo cria um valor dinâmico e usa seus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Funções de objeto dinâmico

|||
|---|---|
| *valor* `in` *matriz*| True se houver um elemento de *matriz* que = = *valor*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valor* `!in` *matriz*| True se há nenhum elemento da *matriz* que = = *valor*
|[`arraylength(`matriz`)`](#arraylength)| Nulo se não for uma matriz
|[`extractjson(`caminho, objeto`)`](#extractjson)|Usa o caminho para navegar em objeto.
|[`parsejson(`fonte`)`](#parsejson)| Uma cadeia de caracteres JSON se transforma em um objeto dinâmico.
|[`range(`etapa`)`](#range)| Uma matriz de valores
|[`mvexpand`listColumn](#mvexpand-operator) | Replica uma linha para cada valor em uma lista em uma célula especificada.
|[`summarize buildschema(`coluna`)`](#buildschema) |Infere o esquema de tipo de conteúdo de coluna
|[`summarize makelist(`coluna`)`](#makelist)| Nivela grupos de linhas e coloca os valores da coluna em uma matriz.
|[`summarize makeset(`coluna`)`](#makeset) | Nivela grupos de linhas e coloca os valores da coluna em uma matriz, sem duplicação.

### <a name="dynamic-objects-in-let-clauses"></a>Objetos dinâmicos em cláusulas permitem que


[Deixar cláusulas](#let-clause) repositório dinâmicos valores como cadeias de caracteres, portanto essas duas cláusulas são equivalentes e precisam de ambas as `parsejson` (ou `todynamic`) antes de ser usada:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>arraylength

O número de elementos em uma matriz dinâmica.

**Sintaxe**

    arraylength(array)

**Argumentos**

* *matriz:* A `dynamic` valor.

**Retorna**

O número de elementos na *matriz*, ou `null` se *a matriz* não é uma matriz.

**Exemplos**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtenha um elemento especificado fora um texto JSON usando uma expressão de caminho. Opcionalmente, converta a cadeia de caracteres extraída para um tipo específico.


**Sintaxe**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Retorna**

Esta função executa uma consulta de JsonPath na fonte de dados que contém uma cadeia de JSON válida, opcionalmente convertendo esse valor em outro tipo dependendo o terceiro argumento.



**Exemplo**

A notação de [colchetes] e a notação de ponto são equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Dicas de desempenho**

* Aplicar cláusulas where antes de usar`extractjson()`
* Considere usar uma correspondência de expressões regulares com [extrair](#extract) . Isso pode ser executado muito mais rápido e é eficaz se o JSON é produzido a partir de um modelo.
* Use `parsejson()` se você precisar extrair mais de um valor do JSON.
* Considere a possibilidade de ter o JSON analisado na inclusão declarando o tipo da coluna para ser dinâmico.

### <a name="json-path-expressions"></a>Expressões de caminho de JSON

|||
|---|---|
|`$`|Objeto raiz|
|`@`|Objeto atual|
|`[0]`|Subscrito da matriz|
|`.`ou`[0]` | Filho|

*(Podemos não atualmente implementar curingas, recursão, união ou fatias.)*




### <a name="parsejson"></a>parseJSON

Interprete um `string` como um [valor JSON](http://json.org/)) e retorna o valor como `dynamic`. É superior a usando `extractjson()` quando você precisar extrair mais de um elemento de um objeto composto JSON.

**Sintaxe**

    parsejson(json)

**Argumentos**

* *json:* Um documento JSON.

**Retorna**

Um objeto do tipo `dynamic` especificado pelo *json*.

**Exemplo**

No exemplo a seguir, quando `context_custom_metrics` é um `string` que tem esta aparência: 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

em seguida, o fragmento a seguir recupera o valor da `duration` slot no objeto e de que ele recupera dois slots, `duration.value` e  `duration.min` (`118.0` e `110.0`, respectivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>intervalo

O `range()` função (não devem ser confundidas com as `range` operador) gera uma matriz dinâmica mantém uma série de valores igualmente espaçados.

**Sintaxe**

    range(start, stop, step)

**Argumentos**

* *Iniciar:* O valor do primeiro elemento na matriz resultante. 
* *Parar:* O valor do último elemento na matriz resultante, ou o valor mínimo maior que o último elemento na matriz resultante e dentro de um número inteiro múltiplo de *etapa* de *Iniciar*.
* *etapa:* A diferença entre dois elementos consecutivas da matriz.

**Exemplos**

O exemplo a seguir retorna `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

O exemplo a seguir retorna uma matriz mantém todos os dias no ano de 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte uma cadeia de caracteres em um valor dinâmico.

### <a name="treepath"></a>TreePath

    treepath(dynamic_object)

Enumera todas as expressões de caminho que identificam folhas em um objeto dinâmico. 

**Retorna**

Uma matriz de expressões de caminho.

**Exemplos**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Observação Essa "[0]" indica a presença de uma matriz, mas não especificar o índice usado por um caminho específico.

### <a name="zip"></a>ZIP

    zip(list1, list2, ...)

Combina um conjunto de listas em uma lista de tuplas.

* `list1...`: Uma lista de valores

**Exemplos**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Nomes

Nomes podem ser até 1.024 caracteres de comprimento. Eles diferenciam maiusculas de minúsculas e pode conter letras, dígitos e sublinhados (`_`). 

Um nome usando da cotação [' … '] or [" ... "] para incluir outros caracteres ou usar uma palavra-chave como um nome. Por exemplo:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' caminho\\arquivo\'x\''] | Use \ para escape caracteres|
|["d-e.=/f#\n"] | |
|[@'path\file'] | Nenhum sai - \ é literal|
|[@"\now& depois\"] | |
|[onde] | Usando uma palavra-chave de idioma como um nome|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


