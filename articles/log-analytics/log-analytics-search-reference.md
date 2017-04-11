<properties
    pageTitle="Análise de log pesquisa referência | Microsoft Azure"
    description="A análise de Log de referência de pesquisa descreve o idioma de pesquisa e fornece a sintaxe de consulta geral opções que você pode usar quando pesquisando dados e filtragem de expressões para ajudar a restringir sua pesquisa."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="log-analytics-search-reference"></a>Referência de pesquisa de análise de log

A seguinte seção de referência sobre linguagem de pesquisa descreve as opções de sintaxe de consulta geral que você pode usar quando pesquisando dados e filtragem de expressões para ajudar a restringir sua pesquisa. Ele também descreve comandos que você pode usar para agir nos dados recuperados.

Você pode ler sobre os campos retornados em pesquisas e os aspectos que ajudam você a dill-em categorias de dados no [campo de pesquisa e referência a seção de faceta](#search-field-and-facet-reference)semelhantes.

## <a name="general-query-syntax"></a>Sintaxe de consulta geral

Sintaxe:

```
filterExpression | command1 | command2 …
```

A expressão de filtro (`filterExpression`) define a condição "where" para a consulta. Os comandos se aplicam aos resultados retornados pela consulta. Vários comandos devem ser separados pelo caractere de barra vertical (|).

### <a name="general-syntax-examples"></a>Exemplos de sintaxe geral

Exemplos:

```
system
```

Essa consulta retorna resultados que contêm a palavra "sistema" em qualquer campo que tiver sido indexado por texto completo ou termos de pesquisa.

>[AZURE.NOTE] Nem todos os campos sejam indexados dessa maneira, mas os campos textuais mais comuns (como nomes e descrições) normalmente seria.

```
system error
```

Essa consulta retorna resultados que contêm as palavras *sistema* e o *erro*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Essa consulta retorna resultados que contêm as palavras *sistema* e o *erro*. Ele classifica os resultados pelo campo *ManagementGroupName* (em ordem crescente) e, em seguida, *TimeGenerated* (em ordem decrescente). Leva apenas os 10 primeiros resultados.

>[AZURE.IMPORTANT] Todos os nomes de campo e os valores para os campos de cadeia de caracteres e texto diferenciam maiusculas de minúsculas.

## <a name="filter-expression"></a>Expressão de filtro

As subseções a seguir explicam as expressões de filtro.

### <a name="string-literals"></a>Cadeia de caracteres literais

Uma cadeia de caracteres literal é qualquer cadeia de caracteres que não é reconhecida pelo analisador como uma palavra-chave ou um tipo de dados predefinido (por exemplo, um número ou data).

Exemplos:

```
These all are string literals
```

Essa consulta procura resultados que contenham ocorrências de todas as cinco palavras. Para realizar uma pesquisa de cadeia de caracteres complexas, coloque a cadeia de caracteres literal entre aspas, por exemplo:

```
" Windows Server"
```

Isso só retorna resultados com correspondências exatas para "Windows Server".

### <a name="numbers"></a>Números

O analisador suporta a inteiro decimal e sintaxe de número de ponto flutuante para campos numéricos.

Exemplos:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="datetime"></a>Data/hora

Cada parte dos dados no sistema tem uma propriedade *TimeGenerated* , que representa a data e hora do registro original. Alguns tipos de dados além disso podem ter mais campos de data/hora (por exemplo, *LastModified*).

O seletor / tempo de gráfico de linha do tempo no Log Analytics mostra uma distribuição de resultados ao longo do tempo (de acordo com a consulta atual está sendo executado), com base no campo *TimeGenerated* . Data/hora campos têm um formato de cadeia de caracteres específicos que pode ser usado em consultas para restringir a consulta para um determinado período. Você também pode usar sintaxe para se referir a intervalos de tempo relativo (por exemplo, "entre 3 dias atrás e 2 horas atrás").

Sintaxe:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Exemplo:

```
TimeGenerated:2013-10-01T12:20
```

O comando anterior retorna somente os registros com um valor de *TimeGenerated* de exatamente 12:20 em 1º de outubro de 2013. É provável que ele fornecerá qualquer resultado, mas você entende a ideia.

O analisador também suporta o valor de data/hora mnemônico, agora.


Novamente, é provável que isso produzirá qualquer resultado porque dados não torná-la por meio do sistema ou rápido.

Esses exemplos são blocos de construção a ser usado para datas relativas e absolutas. Nas próximas três subseções, explicaremos como usá-las em filtros mais avançados com exemplos que usam intervalos de data relativa.

### <a name="datetime-math"></a>Matemática de data/hora

Use os operadores matemáticos de data/hora para deslocamento ou arredondar o valor de data/hora usando cálculos simples de data/hora.

Sintaxe:

```
datetime/unit
```

```
datetime[+|-]count unit
```

|Operador|Descrição|
|---|---|
|/|Arredonda data/hora para a unidade especificada. Exemplo: Agora / dia Arredonda a data/hora atual para a meia-noite do dia atual.|
|+ ou -|Desloca data/hora pelo número especificado de unidades. Exemplos: agora + 1 hora desloca a data/hora atual por uma hora ahead.2013-10-01T12:00-10 dias desloca o valor de data volta por 10 dias.|



Você pode encadear os operadores matemáticos de data/hora, por exemplo:

```
NOW+1HOUR-10MONTHS/MINUTE
```

A tabela a seguir lista as unidades de data/hora com suporte.

Unidade de data/hora|Descrição
---|---
ANO, ANOS|Arredonda para o ano atual ou desloca pelo número especificado de anos.
MÊS, MESES|Arredonda para o mês atual ou desloca pelo número especificado de meses.
DATA DO DIA, DIAS,|Arredonda para o dia do mês atual ou desloca pelo número especificado de dias.
HORA, HORAS|Arredonda a hora atual ou compensações pelo número especificado de horas.
MINUTO, MINUTOS|Arredonda minuto atual ou desloca pelo número especificado de minutos.
SEGUNDO, SEGUNDOS|Arredonda o segundo atual ou desloca pelo número especificado de segundos.
MILISSEGUNDOS, MILISSEGUNDOS, MILLI, MILLIS|Arredonda para milissegundos atual ou desloca pelo número especificado de milissegundos.


### <a name="field-facets"></a>Aspectos de campo

Usando aspectos de campo, você pode especificar o critério de pesquisa para campos específicos e seus valores exatas, em vez de escrever consultas de "texto livre" para vários termos em todo o índice. Nós já usou essa sintaxe em vários exemplos nos parágrafos anteriores. Aqui, fornecemos exemplos mais complexos.

**Sintaxe**

```
field:value
```

```
field=value
```

**Descrição**

Pesquisa o campo para o valor específico. O valor pode ser uma cadeia de caracteres literais, número ou data/hora.

Exemplo:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Sintaxe**

*campo > valor*

*campo < valor*

*campo > = valor*

*campo < = valor*

*campo! = valor*

**Descrição**

Fornece as comparações.

Exemplo:

```
TimeGenerated>NOW+2HOURS
```

**Sintaxe**

```
field:[from..to]
```

**Descrição**

Fornece faceting de intervalo.

Exemplo:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="logical-operators"></a>Operadores lógicos

Os idiomas de consulta oferece suporte os operadores lógicos (*e*, *ou*e *não*) e seus aliases de estilo C (*&&*, *||*e *!*) respectivamente. Você pode usar parênteses para agrupar estes operadores.

Exemplos:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Você pode omitir o operador lógico para os argumentos de filtro de nível superior. Nesse caso, o operador e será considerado.


Expressão de filtro|Equivalente a
---|---
Erro do sistema|sistema e erro
sistema "Windows Server" ou gravidade: 1|sistema e ("Windows Server" ou gravidade: 1)

### <a name="wildcarding"></a>Curinga

A linguagem de consulta oferece suporte usando o (*\*) caracteres para representar um ou mais caracteres para um valor em uma consulta.

Exemplos:

 Encontre todos os computadores com "SQL" nome da como "Redmond-SQL".

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] Caracteres curinga não pode ser usada em cotações hoje. Mensagem =`"*This text*"` consideraremos a (\*) usado como um literal (\*) caracteres.
## <a name="commands"></a>Comandos

Os comandos se aplicam aos resultados retornados pela consulta. Use o caractere de barra vertical (|) para aplicar um comando para os resultados recuperados. Vários comandos devem ser separados pelo caractere de barra vertical.

>[AZURE.NOTE] Nomes de comando podem ser escritos em letras maiusculas ou minúsculas, ao contrário dos nomes de campo e os dados.

### <a name="sort"></a>Classificar

Sintaxe:

    sort field1 asc|desc, field2 asc|desc, …

Classifica os resultados por campos específicos. O prefixo de crescente/decrescente é opcional. Se eles forem omitidos, a ordem de classificação de *asc* será considerada. Se uma consulta não usar o comando *Classificar* explicitamente, desc classificar **TimeGenerated** é o comportamento padrão e sempre retornará os resultados mais recentes primeiro.

### <a name="toplimit"></a>Início/limite

Sintaxe:

    top number


    limit number
Limita a resposta para os resultados de primeiras N.

Exemplo:

    Type:Alert errors detected | top 10

Retorna os 10 principais resultados de correspondência.

### <a name="skip"></a>Ignorar

Sintaxe:

    skip number

Ignora o número de resultados listados.

Exemplo:

    Type:Alert errors detected | top 10 | skip 200

Retorna superiores 10 resultados de correspondência, começando pelo resultado 200.

### <a name="select"></a>Selecione

Sintaxe:

    select field1, field2, ...

Limites de resultados para os campos que você escolher.

Exemplo:

    Type:Alert errors detected | select Name, Severity

Limita os campos de resultados retornados ao *nome* e *gravidade*.

### <a name="measure"></a>Medida

O comando de *medida* é usado para aplicar funções estatísticas aos resultados da pesquisa bruto. Isso é muito útil para obter exibições *Agrupar por* sobre os dados. Quando você usa o comando de *medida* , pesquisa de análise de Log exibe uma tabela com resultados agregados.

Sintaxe:

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Agrega os resultados por *groupField* e calcula os valores de medida agregados usando *aggregatedField*.


|Função estatística de medida|Descrição|
|---|---|
|*aggregateFunction*|O nome da função agregada (não diferencia maiusculas de minúsculas). Funções agregadas a seguir são suportadas: Contagem Máx MIN soma AVG DESVPAD COUNTDISTINCT percentil # # ou PCT # # (# # é qualquer número entre 1 e 99)|
|*aggregatedField*|O campo que está sendo agregado. Este campo é opcional para a função agregada Contar, mas precisa ser um campo numérico existente para SOMAR, MAX, MIN, AVG STDDEV, percentil # # ou PCT # # (# # é qualquer número entre 1 e 99). O aggregatedField também pode ser qualquer uma das funções estender com suporte.|
|*fieldAlias*|O alias (opcional) para o valor agregado calculado. Se não especificado, o nome do campo será AggregatedValue.|
|*groupField*|O nome do campo que o conjunto de resultados é agrupado por.|
|*Intervalo*|O intervalo de tempo no formato:**nnnNAME** onde: nnn é o número inteiro positivo. **É o nome de intervalo.** Os nomes de intervalo com suporte incluem (diferencia maiusculas de minúsculas): MILISSEGUNDOS [S] segundo [S] minuto [S] dia de horas [S] [S] mês [S] ano [S]|


A opção de intervalo só pode ser usada em campos de grupo de data/hora (como *TimeGenerated* e *TimeCreated*). Atualmente, isso não é imposto pelo serviço, mas um campo sem data/hora que é passado para o back-end causará um erro de tempo de execução. Quando a validação de esquema é implementada, a API do serviço rejeita consultas que usam campos sem data/hora para agregação de intervalo. A implementação de *medida* atual é compatível com intervalo de agrupamento para qualquer função de agregação.

Se a cláusula por for omitida, mas um intervalo especificado (como uma segunda sintaxe), o campo *TimeGenerated* será considerado por padrão.

Exemplos:

**Exemplo 1**

    Type:Alert | measure count() as Count by ObjectId

*Explicação*

Os alertas por *ID do objeto* de grupos e calcula o número de alertas para cada grupo. O valor agregado é retornado como o campo de *contagem* (alias).

**Exemplo 2**

    Type:Alert | measure count() interval 1HOUR

*Explicação*

Agrupa os alertas por 1 hora intervalos usando o campo *TimeGenerated* e retorna o número de alertas em cada intervalo.

**Exemplo 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explicação*

Mesmo que o exemplo anterior, mas com um alias de campo agregado (*AlertsPerHour*).

**Exemplo 4**

    * | Count () medida por TimeCreated intervalo 5DAYS

*Explicação*

Agrupa os resultados por 5 dias intervalos usando o campo *TimeCreated* e retorna o número de resultados em cada intervalo.

**Exemplo 5**

    Type:Alert | measure max(Severity) by WorkflowName

*Explicação*

Os alertas de grupos por nome de carga de trabalho e retorna o valor máximo de gravidade de alerta para cada fluxo de trabalho.

**Exemplo 6**

    Type:Alert | measure min(Severity) by WorkflowName

*Explicação*

Mesmo que o exemplo anterior, mas com o *mínimo* agregados função.

**Exemplo 7**

    Type:Perf | measure avg(CounterValue) by Computer

*Explicação*

Agrupa desempenho por computador e calcula a média (média).

**Exemplo 8**

    Type:Perf | measure sum(CounterValue) by Computer

*Explicação*

Mesmo que o exemplo anterior, mas usa *soma*.

**Exemplo 9**

    Type:Perf | measure stddev(CounterValue) by Computer

*Explicação*

Mesmo que o exemplo anterior, mas usa *DESVPAD*.

**Exemplo de 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

*Explicação*

Mesmo que o exemplo anterior, mas usa *PERCENTILE70*.

**Exemplo 11**

    Type:Perf | measure pct70(CounterValue) by Computer

*Explicação*

Mesmo que o exemplo anterior, mas usa *PCT70*. Observe que *PCT # #* é apenas um alias para a função *percentil # #* .

**Exemplo de 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

*Explicação*

Agrupa desempenho primeiro por computador e, em seguida, CounterName e calcula a média (média).

**Exemplo 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explicação*

Obtém os fluxos de trabalho de cinco principais com o número máximo de alertas.

**Exemplo de 14**

    * | Meça countdistinct(Computer) por tipo

*Explicação*

Conta o número de computadores exclusivos para cada tipo de relatório.

**Exemplo 15**

    * | Meça countdistinct(Computer) intervalo 1 hora

*Explicação*

Conta o número de computadores exclusivos de relatório para cada hora.

**Exemplo de 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*Explicação*

% Tempo de processador por computador de grupos e retorna a média de cada 1 hora.

**Exemplo 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*Explicação*

Grupos W3CIISLog pelo método e retorna o valor máximo para cada 5 minutos.

**Exemplo de 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*Explicação*

% Tempo de processador por computador de grupos e retorna o mínimo, média, percentil 75 e máximo para cada 1 hora.

**Exemplo 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

*Explicação*

% Tempo de processador primeiro por computador e depois por nome da instância de grupos e retorna o mínimo, média, percentil 75 e máximo para cada 1 hora

**Exemplo de 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

*Explicação*

Calcula que o número máximo de disco gravações por minuto para cada disco no seu computador

### <a name="where"></a>Onde

Sintaxe:

```
**where** AggregatedValue>20
```

Só pode ser usado depois de um comando de *medida* para filtrar ainda mais os resultados agregados que produziu a função de agregação de *medida* .

Exemplos:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### <a name="in"></a>EM

Sintaxe:

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Descrição: Esta sintaxe permite que você crie uma agregação e feed a lista de valores a partir desse agregação em outra pesquisa (primária) externa que irá procurar eventos com esses valor. Você pode fazer isso por delimitador a pesquisa interna entre chaves e alimentação de seus resultados como valores possíveis para um campo na pesquisa externa usando o operador IN.

Exemplo de consulta interna: *computadores atualmente atualizações de segurança* com a seguinte consulta de agregação:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

A consulta final localiza *todos os eventos do Windows para computadores atualmente atualizações de segurança* é semelhante:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="dedup"></a>Dedup

**Sintaxe**

    Dedup FieldName

**Descrição** Retorna o primeiro documento encontrado para cada valor exclusivo do campo determinado.

**Exemplo**

    Type=Event | sort TimeGenerated DESC | Dedup EventID

O exemplo acima retorna um evento (as últimas desde que usamos DESC no TimeGenerated) por Iddoevento


### <a name="extend"></a>Estender

**Descrição** Permite que você criar campos de tempo de execução em consultas. Você também pode usar o comando de medida após estender se desejar executar agregação.

**Exemplo 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Mostrar pontuação de recomendação ponderada para recomendações de avaliação de SQL

**Exemplo 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Mostrar o valor do contador em KB/s em vez de Bytes

**Exemplo 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Dimensione o valor de WireData TotalBytes, de forma que todos os resultados estão entre 0 e 100.

**Exemplo 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Marcar valores de contador de desempenho menor do que 50% las baixa e outras pessoas como alto

**Exemplo 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Calcula que o número máximo de disco gravações por minuto para cada disco no seu computador

**Funções suportadas**


| Função | Descrição | Exemplos de sintaxe |
|---------|---------|---------|
| Abs | Retorna o valor absoluto do valor especificado ou função. | `abs(x)` <br> `abs(-5)` |
| ACOS | Retorna o arco cosseno de um valor ou uma função | `acos(x)` |
| e | Retorna um valor de true se e somente se todos os operandos são avaliados como true. | `and(not(exists(popularity)),exists(price))` |
| ASEN | Retorna o arco seno de um valor ou uma função | `asin(x)` |
| ATAN | Retorna o arco tangente de um valor ou uma função | `atan(x)` |
| ATAN2 |  Retorna o ângulo resultante da conversão das coordenadas rectangular x, y para coordenadas polares | `atan2(x,y)` |
| cbrt | Raiz cúbica |  `cbrt(x)` |
| ceil | Arredonda para cima até um inteiro | `ceil(x)`  <br> `ceil(5.6)`-Retorna 6 |
| CoS | Cosseno de retorna de um ângulo | `cos(x)` |
| COSH | Retorna o cosseno hiperbólico de um ângulo | `cosh(x)` |
| Def | Def é curto para padrão. Retorna o valor do campo "campo" ou, se o campo não existir, retornará o valor padrão especificado e produz o primeiro valor onde: `exists()==true`. | `def(rating,5)`-Esta função def() retorna a classificação, ou se nenhuma classificação especificada no documento, retorna 5 <br> `def(myfield, 1.0)`-equivalente a`if(exists(myfield),myfield,1.0)` |
| graus | Converter radianos em graus |  `deg(x)` |
| div | `div(x,y)`divide x por y. | `div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist | Retorna a distância entre dois vetores, (pontos) em um espaço n-dimensional. Leva a potência, além de duas ou mais, instâncias de ValueSource e calcula a distância entre os dois vetores. Cada ValueSource deve ser um número. Deve haver um número par de instâncias de ValueSource passado e o método pressupõe que a primeira metade representam primeiro vetor e a segunda metade representam o segundo vetor.  | `dist(2, x, y, 0, 0)`-Calcula a distância Euclidean between,(0,0) e (x, y) para cada documento. <br> `dist(1, x, y, 0, 0)`-Calcula o Manhattan (táxi e seguir), distância entre (0,0) e (x, y) para cada documento. <br> `dist(2,,x,y,z,0,0,0)`-Euclidean distância entre (0, 0,0) e (x, y, z) para cada documento.<br>`dist(1,x,y,z,e,f,g)`-Distância Manhattan entre (x, y, z) e (e, f, g), onde cada letra é um nome de campo. |
| existe | Retorna VERDADEIRO se qualquer membro do campo existe. | `exists(author)`-Retorna verdadeiro para qualquer documento tem um valor no campo "autor".<br>`exists(query(price:5.00))`-Retorna VERDADEIRO se corresponde a "preço", "5,00". |
| EXP | Retorna o número de Euler elevado à potência x | `exp(x)` |
| ARREDMULTB | Arredonda para baixo até um inteiro | `floor(x)`  <br> `floor(5.6)`-Retorna 5 |
| hypo | Retorna sqrt(sum(pow(x,2),pow(y,2))) sem intermediário estouro positivo ou negativo | `hypo(x,y)`  <br> ` |
| Se | Permite consultas de função condicional. No `if(test,value1,value2)` -teste for ou se referir a um valor lógico ou uma expressão que retorna um valor lógico (verdadeiro ou falso).  `value1`é o valor retornado pela função se teste gera verdadeiro. `value2`é o valor retornado pela função se teste gerarem falso. Uma expressão pode ser qualquer função que gera valores booleanos ou até mesmo funções retornando valores numéricos, no qual caso valor 0 será interpretado como falso ou cadeias de caracteres, caso no qual cadeia de caracteres vazia é interpretada como false. | `if(termfreq(cat,'electronics'),popularity,42)`-Essa função verifica cada documento para o para ver se ele contém o termo "eletrônicos" no campo gato. Em caso afirmativo, em seguida, será retornado o valor do campo popularidade, caso contrário, o valor de 42 será retornado. |
| linear | Implementa `m*x+c` onde m e c são constantes e x é uma função aleatório. Isso é equivalente a `sum(product(m,x),c)`, mas um pouco mais eficiente como ele é implementado como uma única função. | `linear(x,m,c) linear(x,2,4)`Retorna`2*x+4` |
| ln| Retorna o log natural da função especificado |  `ln(x)` |
| log | Retorna o log de base 10 da função especificada. | `log(x)   log(sum(x,100))` |
| mapa | Mapeia todos os valores de uma função de entrada x que se enquadram mínimo e máximo inclusivo ao destino especificado. Os argumentos mínimo e máximo devem ser constantes. O destino de argumentos e padrão podem ser constantes ou funções. Se o valor de x não estejam entre mínimo e máximo, e será retornado o valor de x ou um valor padrão será retornado se especificado como um argumento 5ª. |  `map(x,min,max,target) map(x,0,0,1)`-Altera quaisquer valores de 0 a 1. Isso pode ser útil em manipulando valores padrão 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`-Altera todos os valores entre 0 e 100 a 1 e todos os outros valores para -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`-Altera quaisquer valores entre 0 e 100 para x + 599 e todos os outros valores para frequência do termo 'solr' no texto do campo. |
| Max | Retorna o valor numérico máximo de várias funções aninhadas ou constantes, que são especificadas como argumentos: `max(x,y,...)`. A função máximo também pode ser úteis para "bottoming-out" outra função ou campo alguns especificado constante.  Use o `field(myfield,max)` sintaxe para selecionar o valor máximo de um único campo de múltiplos valores.  | `max(myfield,myotherfield,0)` |
| min | Retorna o menor valor numérico de várias funções aninhadas de constantes, que são especificadas como argumentos: `min(x,y,...)`. A função min também pode ser útil para fornecer um "limite superior" em uma função usando uma constante. Use o `field(myfield,min)` sintaxe para selecionar o valor mínimo de um único campo de múltiplos valores. | `min(myfield,myotherfield,0)` |
| Mod | Calcula o resto da função x y função. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))`   |
| MS | Retorna milissegundos de diferença entre seus argumentos. As datas são relativo a Unix ou POSIX época de tempo, meia-noite, 1 de janeiro de 1970 UTC. Argumentos podem ser o nome de uma TrieDateField indexado, ou matemática de data com base em uma data constante ou agora. `ms()`é equivalente a `ms(NOW)`, o número de milissegundos desde a época. `ms(a)`Retorna o número de milissegundos desde a época que representa o argumento. `ms(a,b)`Retorna o número de milissegundos que b ocorre antes, que é `a - b`.| `ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| não | O valor logicamente negado da função quebrado. | `not(exists(author))`-TRUE somente quando `exists(author)` for falsa. |
| ou | Uma disjunção lógica. | `or(value1,value2)`-TRUE se valor1 ou valor2 for verdadeira. |
| pow | Eleva a base especificada à potência especificada. `pow(x,y)`eleva x à potência de y. |  `pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`-O mesmo que raiz. |
| produto | Retorna o produto de múltiplos valores ou funções, que são especificadas em uma lista separada por vírgulas. `mul(...)`pode também ser usado como um alias para essa função. | `product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip | Executa uma função recíproca com `recip(x,m,a,b)` implementação `a/(m*x+b)` onde m, a, b são constantes e x é qualquer função absolutamente complexa. Quando um e b são iguais e x > = 0, esta função tem um valor máximo de 1 que descarta como x aumenta. Aumentar o valor de um e b resultados juntos em um movimento da função toda uma parte mais simples de curva. Essas propriedades podem torná-la uma função ideal para acelerar documentos mais recentes quando x é `rord(datefield)`. | `recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| RAD | Converter graus em radianos | `rad(x)` |
| Imprimir| Arredonda para o inteiro mais próximo | `rint(x)`  <br> `rint(5.6)`-Retorna 6 |
| sen | Seno de retorna de um ângulo | `sin(x)` |
| SENH | Retorna o seno hiperbólico de um ângulo | `sinh(x)` |
| escala | Valores de escala da função x de modo que eles estejam entre a minTarget especificada e maxTarget inclusivo. A implementação atual percorre todos os valores de função para obter o mínimo e máximo, por isso, ele pode escolher a escala correta. A implementação atual não consegue distinguir quando documentos forem excluídos ou documentos que não têm valor. Ele usa 0,0 valores nesses casos. Isso significa que se valores são normalmente tudo maiores do que 0,0, um ainda pode acabar 0,0 como o valor mínimo mapear a partir. Nesses casos, um apropriado `map()` função pode ser usada como uma solução alternativa para alterar 0,0 para um valor no intervalo real, como mostrado aqui:`scale(map(x,0,0,5),1,2)` | `scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`-Escalas os valores de x, para que todos os valores será entre 1 e 2 inclusivo. |
| raiz | Retorna a raiz quadrada do valor especificado ou função. | `sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist | Calcule a distância entre duas cadeias de caracteres. Usa a interface de StringDistance do verificador de ortografia Lucene e dá suporte a todas as implementações disponíveis no pacote de, além de permite que os aplicativos plug-in seus próprios por meio do recurso do Solr recursos de carregamento. leva strdist `(string1, string2, distance measure)`. Valores possíveis para medir distância são: <br>jw: Jaro Winkler<br>Editar: distância Levenstein ou editar<br>ngram: O NGramDistance, se especificada, pode opcionalmente passar no tamanho ngram muito. Padrão é 2.<br>FQN: Totalmente qualificado classe nome para uma implementação da interface StringDistance. Deve ter um construtor de nenhum argumento.|`strdist("SOLR",id,edit)` |
| sub | Retorna x-y de `sub(x,y)`. | `sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| soma | Retorna a soma de múltiplos valores ou funções, que são especificadas em uma lista separada por vírgulas. `add(...)`pode ser usado como um alias para essa função. | `sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)`|
|termfreq | Retorna o número de vezes que o termo será exibido no campo para o documento. | termfreq(Text,'memory')|
| tan | Tangente de um ângulo retorna | `tan(x)` |
| TANH | Retorna a tangente hiperbólica de um ângulo | `tanh(x)` |



## <a name="search-field-and-facet-reference"></a>Referência de campo e faceta de pesquisa

Quando você usa o Log de pesquisa para encontrar dados, resultados exibem vários campos e aspectos. No entanto, algumas das informações que você verá podem não aparecer muito descritivas. Você pode usar as informações a seguir para ajudá-lo a compreender os resultados.



|Campo|Tipo de pesquisa|Descrição|
|---|---|---|
|TenantId|Todos os|Usado para os dados de partição|
|TimeGenerated|Todos os|Usado para direcionar o cronograma, timeselectors (na pesquisa e em outras telas). Ele representa quando a parte de dados foi gerada (normalmente no agente). O tempo é expresso no formato ISO e é sempre UTC. No caso de 'tipos' que são baseados em instrumentação existente (isto é, eventos em um log) isso normalmente seria o tempo real do log/linha/registro de entrada foi registrado em; para alguns dos outros tipos que são produzidos por meio de pacotes de gerenciamento ou na nuvem – ou seja, recomendações/alertas/updateagent/etc., esta é a hora quando essa nova parte dos dados com um instantâneo de uma configuração de algum tipo foi coletado ou um alerta de recomendação/foi produzido com base nele.|
|Iddoevento|Evento|Iddoevento no log de eventos do Windows|
|Log de eventos|Evento|Log de eventos onde o evento foi registrado pelo Windows|
|EventLevelName|Evento|Críticas / aviso / informações / sucesso|
|EventLevel|Evento|Valor numérico de críticas / aviso / informações / sucesso (use EventLevelName em vez para consultas mais fácil/mais legíveis)|
|SourceSystem|Todos os|Onde os dados oriundos (em termos de modo 'Anexar' serviço - la, isto é, o Operations Manager, OMS (= os dados são gerados na nuvem), o armazenamento do Azure (dados provenientes WAD) e assim por diante|
|ObjectName|PerfHourly|Nome de objeto de desempenho do Windows|
|Nome|PerfHourly|Nome de instância de contador de desempenho do Windows|
|CounteName|PerfHourly|Nome de contador de desempenho do Windows|
|ObjectDisplayName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Exibir o nome do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto por ideias operacionais, ou em relação a que o alerta foi gerado|
|RootObjectName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Nome de exibição do pai do pai (em um relacionamento de hospedagem duplo: isto é SqlDatabase hospedado pelo SqlInstance hospedado pelo computador com Windows) do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto por ideias operacionais, ou em relação a que o alerta foi gerado|
|Computador|A maioria dos tipos|Nome do computador que os dados pertence|
|Nome do dispositivo|ProtectionStatus|Nome do computador os dados pertence (mesmo que 'Computador')|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|Classificação de status de ameaças é uma representação numérica do status ameaças e semelhante ao códigos de resposta HTTP, podemos deixou lacunas entre os números (por isso nenhum ameaças é 150 e não 100 ou 0) para que temos algum espaço para adicionar novos estados. Quando isso um rollup para o status de ameaças e proteção, queremos mostrar o pior estado em que o computador foi durante o período de tempo selecionado. Usamos os números para classificar os diferentes estados para que possa olharmos para o registro com o maior número.|
|ThreatStatus|ProtectionStatus|Descrição do ThreatStatus, mapeia 1:1 com ThreatStatusRank|
|TypeofProtection|ProtectionStatus|Produto de antimalware que for detectado no computador: nenhum, ferramenta de remoção de Malware da Microsoft, Forefront e assim por diante|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus, RequiredUpdate|ID de HealthService para agente do computador|
|HealthServiceId|A maioria dos tipos|ID de HealthService para agente do computador|
|ManagementGroupName|A maioria dos tipos|Nome do grupo de gerenciamento de agentes anexados Operations Manager; Caso contrário, ele será null/espaço em branco|
|ObjectType|ConfigurationObject|Tipo (como 'tipo' do pacote de gerenciamento do Operations Manager / classe) para esse objeto descoberto por avaliação de configurações de análise de Log|
|UpdateTitle|RequiredUpdate|Nome da atualização encontrado não instalada|
|PublishDate|RequiredUpdate|Quando foi a atualização publicada no Microsoft update?|
|Servidor|RequiredUpdate|Nome do computador os dados pertence (mesmo que 'Computador')|
|Produto|RequiredUpdate|Produto que a atualização se aplica a|
|UpdateClassification|RequiredUpdate|Tipo de atualização (cumulativo, pacote de serviço, etc.)|
|KBID|RequiredUpdate|ID do artigo KB que descreve esta prática ou atualização recomendadas|
|WorkflowName|ConfigurationAlert|Nome da regra ou monitor que produziu o alerta|
|Gravidade|ConfigurationAlert|Gravidade do alerta|
|Prioridade|ConfigurationAlert|Prioridade do alerta|
|IsMonitorAlert|ConfigurationAlert|Este alerta é gerado por um monitor (verdadeiro) ou uma regra (FALSO)?|
|AlertParameters|ConfigurationAlert|XML com os parâmetros de alerta de análise de Log|
|Contexto|ConfigurationAlert|XML com 'contexto' de alerta de análise de Log|
|Carga de trabalho|ConfigurationAlert|Tecnologia ou 'carga de trabalho' que o alerta se refere a|
|AdvisorWorkload|Recomendação|Tecnologia ou 'carga de trabalho' que se refere a recomendação a|
|Descrição|ConfigurationAlert|Descrição do alerta (abreviada)|
|DaysSinceLastUpdate|UpdateAgent|Quantos dias atrás (relativo 'TimeGenerated' deste registro) Este agente instalar qualquer atualização do WSUS/Microsoft Update?|
|DaysSinceLastUpdateBucket|UpdateAgent|Com base em DaysSinceLastUpdate, uma categorização no 'classificações de tempo' de foi como tempo atrás de um computador última instalado qualquer atualização do WSUS/Microsoft Update|
|AutomaticUpdateEnabled|UpdateAgent|Verificação de atualização automática habilitada ou desabilitada neste agente?
|AutomaticUpdateValue|UpdateAgent|Atualização automática está verificando definido como baixar automaticamente e instalar, baixe-o somente ou apenas verificar?|
|WindowsUpdateAgentVersion|UpdateAgent|Número de versão do Microsoft Update agent|
|WSUSServer|UpdateAgent|Qual servidor WSUS é este agente de atualização-alvo?|
|OSVersion|UpdateAgent|Versão do sistema operacional que este update agent está em execução|
|Nome|Recomendação, ConfigurationObjectProperty|Nome/título da recomendação ou nome da propriedade da avaliação de configuração de análise de Log|
|Valor|ConfigurationObjectProperty|Valor de uma propriedade de avaliação de configurações de análise de Log|
|KBLink|Recomendação|URL do artigo KB que descreve esta prática ou atualização recomendadas|
|RecommendationStatus|Recomendação|Recomendações estão entre os tipos de alguns cujos registros 'atualizados', não apenas adicionados ao índice de pesquisa. Esse status é alterado se a recomendação é ativo/abrir ou se a análise de Log detecta que ele foi resolvido.|
|RenderedDescription|Evento|Descrição renderizada (texto reutilizada com parâmetros preenchidos) de um evento do Windows|
|ParameterXml|Evento|XML com os parâmetros na seção 'dados' de um evento do Windows (conforme visto no Visualizador de eventos)|
|EventData|Evento|XML com a seção inteira 'dados' de um evento do Windows (conforme visto no Visualizador de eventos)|
|Fonte|Evento|Fonte de log de eventos que gerou o evento|
|EventCategory|Evento|Categoria do evento, diretamente do log de eventos do Windows|
|Nome de usuário|Evento|Nome de usuário do evento Windows (normalmente, AUTHORITY\LOCALSYSTEM NT)|
|SampleValue|PerfHourly|Valor médio para a agregação por hora de um contador de desempenho|
|Min|PerfHourly|Valor mínimo no intervalo por hora de uma agregação por hora de contador de desempenho|
|Max|PerfHourly|Valor máximo no intervalo por hora de uma agregação por hora de contador de desempenho|
|Percentile95|PerfHourly|O valor do percentil 95 para o intervalo por hora de uma agregação por hora de contador de desempenho|
|SampleCount|PerfHourly|Quantas amostras de contador de desempenho 'bruto' foram usadas para produzir esse registro agregado por hora|
|Ameaças|ProtectionStatus|Nome do malware encontrado|
|StorageAccount|W3CIISLog|Conta de armazenamento do Azure o log foi lido a partir|
|AzureDeploymentID|W3CIISLog|ID do Azure implantação do serviço de nuvem o log de pertence|
|Função|W3CIISLog|Função do serviço de nuvem do Azure o log pertence|
|RoleInstance|W3CIISLog|RoleInstance da função Azure qual pertence o log|
|sSiteName|W3CIISLog|Site do IIS que o log pertence (notação metabase); o campo s-sitename no log de original|
|sComputerName|W3CIISLog|Campo s-computername no log de original|
|sIP|W3CIISLog|Solicitação de endereço HTTP de IP do servidor foi endereçada ao. O campo de ip s no log de original|
|csMethod|W3CIISLog|Método de HTTP (GET/POST/etc) usados pelo cliente na solicitação HTTP. O método de cs no log de original|
|cIP|W3CIISLog|Solicitação HTTP do endereço IP do cliente veio. O campo c-ip no log de original|
|csUserAgent|W3CIISLog|Agente de usuário HTTP declarados pelo cliente (navegador ou caso contrário). O cs-agente de usuário no log de original|
|scStatus|W3CIISLog|Código de Status de HTTP (200/403/500/etc) retornado pelo servidor para o cliente. O status de cs no log de original|
|TimeTaken|W3CIISLog|Como long (em milissegundos) que a solicitação levou para ser concluída. O campo timetaken no log de original|
|csUriStem|W3CIISLog|Uri relativo (sem hospedar endereço, por exemplo, ' / Pesquisar ') que foi solicitado. O campo cs-uristem no log de original|
|csUriQuery|W3CIISLog|Consulta URI. Consultas URI são necessárias apenas para páginas dinâmicas, como páginas ASP, para que esse campo geralmente contém um hífen para páginas estáticas.|
|Esporte|W3CIISLog|Porta do servidor que a solicitação HTTP foi enviada para (e IIS ouve, desde que ela selecionou)|
|csUserName|W3CIISLog|Autenticado nome de usuário, se a solicitação for autenticado e não anônimo|
|csVersion|W3CIISLog|Versão do protocolo HTTP usada na solicitação (isto é, ' HTTP / 1.1')|
|csCookie|W3CIISLog|Informações de cookie|
|csReferer|W3CIISLog|Site que o usuário última visita. Este site fornece um link para o site atual.|
|csHost|W3CIISLog|Cabeçalho de host (por exemplo, ' www.mysite.com') que foi solicitado|
|scSubStatus|W3CIISLog|Código de erro de substatus|
|scWin32Status|W3CIISLog|Código de Status do Windows|
|csBytes|W3CIISLog|Bytes enviados na solicitação do cliente para o servidor|
|scBytes|W3CIISLog|Bytes retornados volta na resposta do servidor para o cliente|
|ConfigChangeType|ConfigurationChange|Tipo de alteração (WindowsServices / Software / etc)|
|ChangeCategory|ConfigurationChange|Categoria da alteração (modificado / adicionado / removido)|
|SoftwareType|ConfigurationChange|Tipo de software (Atualizar aplicativo /)|
|SoftwareName|ConfigurationChange|Nome do software (aplicável somente às alterações de software)|
|Publisher|ConfigurationChange|Fornecedor que publica o software (aplicável somente às alterações de software)|
|SvcChangeType|ConfigurationChange|Tipo de alteração que foi aplicada em um serviço do Windows (estado / statuptype / caminho / ServiceAccount) - apenas aplicável para alterações de serviço do Windows|
|SvcDisplayName|ConfigurationChange|Nome de exibição do serviço que foi alterado|
|SvcName|ConfigurationChange|Nome do serviço que foi alterado|
|SvcState|ConfigurationChange|Novo estado (atual) do serviço|
|SvcPreviousState|ConfigurationChange|Anterior conhecido estado do serviço (aplicável somente se alterado de estado de serviço)|
|SvcStartupType|ConfigurationChange|Tipo de inicialização do serviço|
|SvcPreviousStartupType|ConfigurationChange|Tipo de inicialização de anterior serviço (aplicável somente se alterado de tipo de inicialização do serviço)|
|SvcAccount|ConfigurationChange|Conta de serviço|
|SvcPreviousAccount|ConfigurationChange|Conta de serviço anterior (aplicável somente se a conta de serviço alterada)|
|SvcPath|ConfigurationChange|Caminho para o executável do serviço Windows|
|SvcPreviousPath|ConfigurationChange|Caminho anterior do executável para o serviço do Windows (aplicável somente se ele alterado)|
|SvcDescription|ConfigurationChange|Descrição do serviço|
|Anterior|ConfigurationChange|Estado anterior deste software (versão instalado / não instalado / anterior)|
|Atual|ConfigurationChange|Estado mais recente do software (versão instalado / não instalado / atual)|

## <a name="next-steps"></a>Próximas etapas
Para obter informações adicionais sobre pesquisas de log:

- Familiarize-se com [pesquisas de log](log-analytics-log-searches.md) exibir informações detalhadas coletadas por soluções.
- Use [campos personalizados na análise de Log](log-analytics-custom-fields.md) para estender pesquisas de log.
