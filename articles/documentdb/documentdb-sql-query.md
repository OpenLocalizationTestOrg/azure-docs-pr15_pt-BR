<properties 
    pageTitle="Sintaxe SQL e SQL da consulta para DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre sintaxe SQL, conceitos de banco de dados e consultas SQL para DocumentDB, um banco de dados NoSQL. SQL pode ser usado como uma linguagem de consulta JSON em DocumentDB." 
    keywords="sintaxe SQL, consulta sql, consultas sql, linguagem de consulta de json, conceitos de banco de dados e consultas sql, funções de agregação"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Consulta SQL e sintaxe SQL no DocumentDB
Microsoft Azure DocumentDB suporta consultar documentos usando SQL (Structured Query Language) como uma linguagem de consulta JSON. DocumentDB é realmente livre de esquema. Devido ao seu compromisso com o modelo de dados JSON diretamente no mecanismo de banco de dados, ele oferece a indexação automática de documentos JSON sem a necessidade de esquema explícita ou criação de índices secundários. 

Durante a criação da linguagem de consulta para DocumentDB tivemos dois objetivos em mente:

-   Em vez de criação de um novo idioma de consulta JSON, queremos SQL de suporte. SQL é uma das linguagens de consulta mais conhecidos e populares. DocumentDB SQL fornece um modelo de programação formal para consultas avançadas sobre documentos JSON.
-   Como JSON documento banco de dados capaz de executar o JavaScript diretamente no mecanismo de banco de dados, queremos usar o modelo de programação do JavaScript como base nossa linguagem de consulta. O SQL DocumentDB raiz no sistema de tipo do JavaScript, avaliação de expressão e invocação de função. No virada fornece um modelo de programação natural para projeções relacionais, navegação hierárquica em documentos JSON, junções auto, consultas espaciais e invocação de funções definidas pelo usuário (UDFs) escrito inteiramente em JavaScript, entre outros recursos. 

Podemos acreditar que esses recursos são fundamentais para reduzir o conflito entre o aplicativo e o banco de dados e são fundamentais para a produtividade do desenvolvedor.

Recomendamos assistindo vídeo a seguir, onde Aravind Ramachandran mostra os recursos de consulta do DocumentDB, e visitando nosso [Espaço de consulta](http://www.documentdb.com/sql/demo), onde você pode experimentar DocumentDB e executar consultas SQL em relação a nossa dataset Introdução.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Em seguida, retorne neste artigo, onde vamos começar com um tutorial de consulta SQL que o orientará durante alguns documentos JSON simples e comandos SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Introdução ao comandos SQL no DocumentDB
Para ver DocumentDB SQL no trabalho, vamos começar com alguns documentos JSON simples e percorrer algumas consultas simples em relação a ele. Considere esses dois documentos JSON sobre duas famílias. Observe que com DocumentDB, não precisamos criar qualquer esquemas ou índices secundários explicitamente. Simplesmente precisamos inserir os documentos JSON para um conjunto de DocumentDB e subsequentemente da consulta. Aqui temos uma JSON simple do documento para a família Freitas, os pais, filhos (e seus animais), informações de endereço e registro. O documento tem cadeias de caracteres, números, booleanos, matrizes e propriedades aninhadas. 

**Documento**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Aqui está um segundo documento com uma diferença sutil – `givenName` e `familyName` são usados em vez de `firstName` e `lastName`.

**Documento**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Agora vamos tentar algumas consultas estes dados para entender alguns dos principais aspectos da DocumentDB SQL. Por exemplo, a seguinte consulta retornará os documentos onde o campo id corresponde `AndersenFamily`. Como é uma `SELECT *`, a saída da consulta é o documento JSON completo:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Agora, considere o caso onde precisamos formatar a saída JSON em uma forma diferente. Essa consulta projetos um novo objeto JSON com dois campos selecionados, nome e cidade, quando cidade dos endereço tem o mesmo nome como o estado. Nesse caso, corresponde a "Nova York, NY".

**Consulta**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


A próxima consulta retorna todos os nomes de determinado de filhos da família cuja id corresponde `WakefieldFamily` ordenadas pela cidade de residência.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Podemos gostaria de chamar a atenção para alguns aspectos notável da linguagem de consulta DocumentDB através de exemplos que podemos viu até agora:  
 
-   Como DocumentDB SQL funciona em valores JSON, ela lida com árvore em forma de entidades em vez de linhas e colunas. Portanto, o idioma permite que você se referir a nós da árvore em qualquer nível aleatório, como `Node1.Node2.Node3…..Nodem`, semelhante ao SQL relacional fazendo referência a referência duas partes da `<table>.<column>`.   
-   A linguagem de consulta estruturada funciona com dados sem esquema. Portanto, o sistema de tipo precisa ser vinculado dinamicamente. A mesma expressão pode produzir tipos diferentes em diferentes documentos. O resultado de uma consulta é um valor JSON válido, mas não garantimos ser de um esquema fixo.  
-   DocumentDB só oferece suporte a documentos JSON estritas. Isso significa que o sistema de tipo e expressões são restritas lidar apenas com tipos JSON. Consulte a [especificação de JSON](http://www.json.org/) para obter mais detalhes.  
-   Uma coleção de DocumentDB é um contêiner de esquema livre de documentos JSON. As relações em entidades dentro e entre os documentos em um conjunto de dados são capturadas implicitamente pelo retenção e não pelo chave primária e relações de chave estrangeiras. Este é um aspecto importante pena apontar sabendo junções dentro do documento discutidas posteriormente neste artigo.

## <a name="documentdb-indexing"></a>Indexação de DocumentDB

Antes de entrar na sintaxe SQL de DocumentDB, vale a pena explorar o design de indexação em DocumentDB. 

A finalidade de índices de banco de dados é fazer consultas em suas várias formas e formas com consumo de recursos mínimo (como CPU e entrada/saída) enquanto proporciona boa produtividade e baixa latência. Muitas vezes, a opção de índice à direita para consultar um banco de dados requer muito planejamento e o experimento. Essa abordagem apresenta um desafio para bancos de dados sem esquema onde os dados não estão em conformidade com um esquema estrito e evoluem rapidamente. 

Portanto, quando criamos a DocumentDB indexação subsistema, podemos definir as seguintes metas:

-   Indexar documentos sem a necessidade de esquema: O subsistema de indexação não exigem qualquer informação de esquema ou fazer suposições sobre esquema dos documentos. 

-   Suporte para consultas de hierárquicas, relacionais e eficientes, sofisticadas: O índice dá suporte a linguagem de consulta DocumentDB eficiente, incluindo o suporte para projeções hierárquicos e relacionais.

-   Suporte para consultas consistentes em face de um volume contínuo de gravações: gravação altamente cargas de trabalho de produtividade com consultas consistentes, o índice é atualizado incremental com eficiência e on-line no caso de um volume contínuo de gravações. A atualização do índice consistente é fundamental para servir as consultas no nível de consistência no qual o usuário configurado o serviço de documento.

-   Suporte para multilocação: dado o modelo de reserva com base de gestão de recurso entre locatários, atualizações de índice são executadas dentro do orçamento de recursos do sistema (CPU, memória e operações de entrada/saída por segundo) alocado por réplica. 

-   Eficiência de armazenamento: eficiência de custo, a sobrecarga de armazenamento em disco do índice é limitada e previsíveis. Isso é essencial porque DocumentDB permite que o desenvolvedor tornar compensações de custo baseado entre índice sobrecarga em relação o desempenho de consulta.  

Consulte as [amostras de DocumentDB](https://github.com/Azure/azure-documentdb-net) no MSDN para exemplos mostrando como configurar a política de indexação para um conjunto. Agora, vamos nos detalhes da sintaxe SQL DocumentDB.


## <a name="basics-of-a-documentdb-sql-query"></a>Noções básicas de uma consulta SQL DocumentDB
Cada consulta consiste em uma cláusula SELECT e FROM opcional e cláusulas WHERE por padrões ANSI SQL. Normalmente, para cada consulta, a fonte na cláusula FROM é enumerada. Em seguida, o filtro na cláusula WHERE é aplicado na origem para recuperar um subconjunto dos documentos JSON. Finalmente, a cláusula SELECT é usada para projetar os valores JSON solicitados na lista de seleção.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>Cláusula FROM
O `FROM <from_specification>` cláusula é opcional, a menos que a fonte é filtrada ou projetada mais tarde na consulta. A finalidade dessa cláusula é especificar a fonte de dados no qual a consulta deve operar. Comumente todo o conjunto é a fonte, mas você pode especificar um subconjunto do conjunto de em vez disso. 

Uma consulta, como `SELECT * FROM Families` indica que toda a coleção de famílias é a fonte pela qual enumerar. Um identificador especial raiz pode ser usado para representar a coleção em vez de usar o nome da coleção. A lista a seguir contém as regras que são impostas por consulta:

- O conjunto pode ser alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Veja `f` equivale a `Families`. `AS`é o identificador de uma palavra-chave opcional para alias.

-   Observe que uma vez alias, a fonte original não pode ser vinculado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido, pois o identificador "Famílias" não pode ser resolvido mais.

-   Todas as propriedades que precisam ser referenciada devem ser totalmente qualificadas. Em ausência de cumprimento de esquema estrito, isso é imposto para evitar quaisquer associações ambíguas. Portanto, `SELECT id FROM Families f` é sintaticamente inválido desde a propriedade `id` não está ligado.
    
### <a name="sub-documents"></a>Subdocumentos
A fonte também pode ser reduzida a um subconjunto menor. Por exemplo, para enumerar somente uma árvore sub em cada documento, raiz sub poderá se tornar a fonte, conforme mostrado no exemplo a seguir.

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Enquanto o exemplo acima usado uma matriz como fonte, um objeto também pode ser usado como a fonte, o que é o que é mostrado no exemplo a seguir. Qualquer valor JSON válido (não indefinido) que pode ser encontrado na fonte será considerado para inclusão no resultado da consulta. Se não tem o algumas famílias um `address.state` valor, eles serão excluídos no resultado da consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Cláusula WHERE
A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Especifica as condições que os documentos JSON fornecidos pela fonte devem satisfazer para ser incluídos como parte do resultado. Qualquer documento JSON deve avaliar as condições especificadas como "true" para ser considerado para o resultado. A cláusula WHERE é usada pela camada de índice para determinar o menor subconjunto absoluto de documentos de origem que podem fazer parte do resultado. 

A seguinte consulta solicitações de documentos que contêm uma propriedade name cujo valor é `AndersenFamily`. Qualquer outro documento que não tem uma propriedade de nome, ou onde o valor não corresponde `AndersenFamily` é excluído. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


O exemplo anterior mostrava uma consulta simples igualdade. DocumentDB SQL também dá suporte a uma variedade de expressões escalares. Mais comumente usados são expressões binário e unário. Referências a propriedade do objeto JSON de origem também são expressões válidas. 

Os seguintes operadores binários são suportados e podem ser usados em consultas conforme mostrado nos exemplos a seguir:  
<table>
<tr>
<td>Aritmética</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit a bit</td>    
<td>|, &, ^, <<, >>, >>> (deslocamento à direita do preenchimento de zero) </td>
</tr>
<tr>
<td>Lógico</td>
<td>E, OU, NÃO</td>
</tr>
<tr>
<td>Comparação</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadeia de caracteres</td> 
<td>|| (concatenar)</td>
</tr>
</table>  

Vamos dar uma olhada em algumas consultas usando operadores binários.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Os operadores de unário +,-, ~ não também são compatíveis e podem ser usados dentro consultas conforme mostrado no exemplo a seguir:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Além dos operadores binários e unário, também são permitidas referências de propriedade. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o documento JSON que contém a propriedade `isRegistered` onde o valor da propriedade é igual ao JSON `true` valor. Quaisquer outros valores (false, nulo, indefinido, `<number>`, `<string>`, `<object>`, `<array>`, etc) leva para o documento de origem sendo excluído do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de comparação e de igualdade
A tabela a seguir mostra o resultado de comparações de igualdade em DocumentDB SQL entre os dois tipos JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Operacional</strong>
         </td>
         <td valign="top">
            <strong>Indefinido</strong>
         </td>
         <td valign="top">
            <strong>Nulo</strong>
         </td>
         <td valign="top">
            <strong>Booliano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadeia de caracteres</strong>
         </td>
         <td valign="top">
            <strong>Objeto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Indefinido<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nulo<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booliano<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadeia de caracteres<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objeto<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>Okey</strong>
         </td>
      </tr>
   </tbody>
</table>

Para outros operadores de comparação como >, > =,! =, < e < =, as seguintes regras se aplicam:   

-   Comparação entre tipos resulta em indefinido.
-   Comparação entre dois objetos ou duas matrizes resulta em indefinido.   

Se o resultado da expressão escalar no filtro é indefinido, o documento correspondente poderia não ser incluído no resultado, desde que indefinido logicamente não correspondem aos "true".

### <a name="between-keyword"></a>ENTRE palavras-chave
Você também pode usar a palavra-chave entre expressar consultas em intervalos de valores como em ANSI SQL. ENTRE pode ser usados em relação a cadeias de caracteres ou números.

Por exemplo, esta consulta retorna todos os documentos família nas quais notas da primeira criança está entre 1-5 (ambos inclusivo). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Ao contrário em ANSI-SQL, você também pode usar a cláusula entre na cláusula FROM como no exemplo a seguir.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para tempos de execução de consulta mais rápidos, lembre-se criar uma política de indexação que usa um tipo de índice de intervalo contra quaisquer propriedades/caminhos numéricos são filtrados na cláusula entre. 

A principal diferença entre o uso entre no DocumentDB e ANSI SQL é que você pode expressar consultas de intervalo em Propriedades de tipos mistos – por exemplo, você pode ter "Notas" ser um número (5) em alguns documentos e cadeias de caracteres em outros ("grade4"). Nesses casos, como em JavaScript, uma comparação entre dois resultados de diferentes tipos em "indefinido" e o documento será ignorada.

### <a name="logical-and-or-and-not-operators"></a>Lógico (e, ou e não) operadores
Operadores lógicos operam em valores booleanos. As tabelas de verdade lógicas para estes operadores são mostradas nas tabelas a seguir.

OU|True|FALSO|Indefinido
---|---|---|---
True|True|True|True
FALSO|True|FALSO|Indefinido
Indefinido|True|Indefinido|Indefinido

E|True|FALSO|Indefinido
---|---|---|---
True|True|FALSO|Indefinido
FALSO|FALSO|FALSO|FALSO
Indefinido|Indefinido|FALSO|Indefinido

NÃO|  |
---|---
True|FALSO
FALSO|True
Indefinido|Indefinido

### <a name="in-keyword"></a>Palavra-chave
A palavra-chave IN pode ser usado para verificar se um valor especificado corresponde a qualquer valor em uma lista. Por exemplo, esta consulta retorna todos os documentos família onde a identificação é um dos "WakefieldFamily" ou "AndersenFamily". 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este exemplo retorna todos os documentos onde o estado é qualquer um dos valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operadores de adesão (?) e Ternário (?)
Os operadores ternários e adesão podem ser usados para construir expressões condicionais, semelhantes ao populares linguagens de programação como c# e JavaScript. 

O operador ternário (?) pode ser muito útil ao construir novas propriedades JSON instantâneos. Por exemplo, agora você pode escrever consultas para classificar os níveis de classe em formato legível humano como iniciante/intermediário/Avançado conforme mostrado abaixo.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Você também pode aninhar as chamadas para o operador like na consulta abaixo.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como com outros operadores de consulta, se as propriedades de referência na expressão condicional estão ausentes em qualquer documento, ou se os tipos que estão sendo comparados são diferentes, em seguida, esses documentos serão excluídos nos resultados da consulta.

O operador de adesão (?) pode ser usado com eficiência verificar a presença de uma propriedade (também conhecido como é definido) em um documento. Isso é útil quando consultando semiestruturados ou dados de tipos mistos. Por exemplo, esta consulta retorna o "Sobrenome" se estiver presente, ou o "Sobrenome" se ele ainda não estiver presente.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Acesso de propriedade entre aspas
Você também pode acessar propriedades usando o operador de propriedade entre aspas `[]`. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil quando você precisar sair de uma propriedade que contém espaços, caracteres especiais, ou acontece ao compartilhar o mesmo nome que uma palavra reservada ou uma palavra-chave SQL.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Cláusula SELECT
Cláusula SELECT (**`SELECT <select_list>`**) é obrigatório e especifica quais valores serão recuperados da consulta, assim como em ANSI-SQL. O subconjunto que é foram filtrado na parte superior de documentos de origem são passados para a fase de projeção, onde os valores JSON especificados são recuperados e um novo objeto JSON é construído, para cada entrada passada para ele. 

O exemplo a seguir mostra uma consulta seleção típica. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriedades aninhadas
No exemplo a seguir, podemos são Projetando duas propriedades aninhadas `f.address.state` e `f.address.city`.

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projeção também suporta expressões de JSON, conforme mostrado no exemplo a seguir.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Vamos examinar a função do `$1` aqui. O `SELECT` cláusula precisa criar um objeto JSON e desde que nenhuma chave for fornecida, podemos usar nomes de variável implícita argumento começando com `$1`. Por exemplo, essa consulta retornará duas variáveis de argumento implícitos, rotuladas `$1` e `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Alias
Agora vamos ampliar o exemplo acima com alias explícitas de valores. COMO é a palavra-chave usada para o alias. Observe que é opcional, conforme mostrado enquanto Projetando o segundo valor como `NameInfo`. 

No caso de uma consulta tem duas propriedades com o mesmo nome, alias devem ser usada para renomear uma ou ambas as propriedades para que eles são ambiguidade removidos no resultado projetado.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressões escalares
Além de referências de propriedade, a cláusula SELECT também suporta expressões escalares como constantes, expressões aritméticas, expressões lógicas, etc. Por exemplo, aqui está uma consulta de "Olá, mundo" simples.

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


Aqui está um exemplo mais complexo do que usa uma expressão escalar.

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3   

**Resultados**

    [{
      "$1": 1.33333
    }]


No exemplo a seguir, o resultado da expressão escalar é um booliano.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Criação de objeto e de matriz
Outro recurso principal do DocumentDB SQL é a criação de matriz/objeto. No exemplo anterior, observe que criamos um novo objeto JSON. Da mesma forma, um também pode construir matrizes, conforme mostrado nos exemplos a seguir.

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>VALOR palavra-chave
A palavra-chave **valor** fornece uma maneira para retornar o valor JSON. Por exemplo, a consulta mostrada abaixo retorna a escalar `"Hello World"` em vez de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


A seguinte consulta retorna o valor JSON sem o `"address"` rótulo nos resultados.

**Consulta**

    SELECT VALUE f.address
    FROM Families f 

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

O exemplo a seguir estende isso para mostrar como retornar valores primitivos JSON (o nível folha da árvore de JSON). 

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f 

**Resultados**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* O operador
O operador especial (*) é suportado para project o documento como-é. Quando usada, ele deve ser o único campo projetado. Enquanto uma consulta como `SELECT * FROM Families f` é válido, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` não são válidos.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Operador TOP
A palavra-chave superior pode ser usada para limitar o número de valores de uma consulta. Quando superior é usado em conjunto com a cláusula ORDER BY, o conjunto de resultados está limitado ao primeiro número N de valores ordenados; Caso contrário, retorna o número de N primeiro dos resultados em uma ordem indefinida. Como prática recomendada, em uma instrução SELECT, sempre use uma cláusula ORDER BY com a cláusula TOP. Esta é a única maneira de previsíveis indicar quais linhas são afetadas pelas superior. 


**Consulta**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

INÍCIO pode ser usado com um valor constante (como mostrado acima) ou com um valor de variável usando consultas com parâmetros. Para obter mais detalhes, consulte abaixo de consultas com parâmetros.

## <a name="order-by-clause"></a>Cláusula ORDER BY
Como em ANSI-SQL, você pode incluir uma cláusula Order By opcional ao consultar. A cláusula pode incluir um argumento Crescente/Decrescente opcional para especificar a ordem na qual os resultados devem ser recuperados. Para obter uma visão mais detalhada de Order By, consulte [DocumentDB ordem por instruções passo a passo](documentdb-orderby.md).

Por exemplo, aqui está uma consulta que recupera famílias em ordem de nome de cidade residente.

**Consulta**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

E aqui está uma consulta que recupera famílias em ordem de data de criação, que é armazenada como um número que representa a época tempo, ou seja, o tempo decorrido desde 1º de janeiro de 1970 em segundos.

**Consulta**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Conceitos de banco de dados avançada e consultas SQL
### <a name="iteration"></a>Iteração
Uma nova construção foi adicionada via a palavra-chave **IN** no DocumentDB SQL para oferecer suporte à iteração sobre matrizes JSON. A fonte de oferece suporte para iteração. Vamos começar com o exemplo a seguir:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Agora vamos observar a outra consulta que executa iteração sobre filhos da coleção. Observe a diferença na matriz de resultado. Este exemplo divide `children` e nivela os resultados em uma única matriz.  

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Isso pode ser mais usado para filtrar em cada entrada individual da matriz, conforme mostrado no exemplo a seguir.

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Junções
Em um banco de dados relacional, a necessidade de ingressar em tabelas é muito importante. É o resultado lógico à criação de esquemas normalizados. DocumentDB lida com o modelo de dados desordenados de documentos sem esquema. Este é o equivalente lógico de uma "associação a mesmo".

A sintaxe compatível com o idioma é junção de junção < from_source2 > < from_source1 >... INGRESSE em < from_sourceN >. Em geral, isso retorna um conjunto de **N**- tuplas (tupla com valores de **N** ). Cada tupla tem produzidos por iteração todos os aliases de conjunto sobre seus respectivos conjuntos de valores. Em outras palavras, este é um produto cruzado completo dos conjuntos de participar da junção.

Os exemplos a seguir mostram como funciona a cláusula JOIN. No exemplo a seguir, o resultado é vazio desde o produto cruzado das cada documento de origem e um conjunto vazio está vazio.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


No exemplo a seguir, a junção é entre a raiz do documento e o `children` sub raiz. É um produto cruzado entre dois objetos JSON. O fato de que seus filhos é uma matriz não é eficaz na associação desde que estamos lidando com uma única raiz que é a matriz de filhos. Portanto, o resultado contém apenas dois resultados, desde que o produto cruzado de cada documento com a matriz produz exatamente apenas um documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


O exemplo a seguir mostra uma junção mais convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



A primeira coisa a observar é que o `from_source` da **INGRESSAR** cláusula é um iterador. Portanto, o fluxo nesse caso é da seguinte maneira:  

-   Expanda cada elemento filho **c** na matriz.
-   Aplica um produto cruzado com a raiz do documento **f** com cada elemento filho **c** que foi nivelados na primeira etapa.
-   Por fim, projeto a propriedade de nome de objeto **f** raiz sozinha. 

O primeiro documento (`AndersenFamily`) contém apenas um elemento filho, portanto, o conjunto de resultados contém apenas um único objeto correspondente a este documento. O segundo documento (`WakefieldFamily`) contém dois filhos. Portanto, o produto cruzado produz um objeto separado para cada filho, assim, resultando em dois objetos, uma para cada filho correspondente a este documento. Observe que os campos de raiz nos dois esses documentos será o mesmos, assim como você poderia esperar em um produto cruzado.

O utilitário real da junção é tuplas de formulário do produto cruzado em uma forma que caso contrário, é difícil de projeto. Além disso, como veremos no exemplo abaixo, você pode filtrar a combinação de uma tupla que permite que o usuário escolhe uma condição atendida pelas tuplas geral.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este exemplo é uma extensão natural do exemplo anterior e executa uma junção dupla. Portanto, o produto cruzado pode ser exibido como o seguinte código pseudo.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`tem um filho que tenha um pet. Portanto, o produto cruzado gerarem uma linha (1*1*1) da família. No entanto, WakefieldFamily tem dois filhos, mas apenas um filho "Jesse" tem animais. Jesse tem 2 animais apesar. Portanto, o produto cruzado produz 1*1*2 = 2 linhas dessa família.

No próximo exemplo, há um filtro adicional no `pet`. Isso exclui todos as tuplas onde o nome de pet não é "Sombra". Observe que nós são capazes de criar tuplas de matrizes, filtro em qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto. 

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Integração de JavaScript
DocumentDB fornece um modelo de programação para executar lógica de aplicativo JavaScript com base diretamente nas coleções de arquivos em termos de procedimentos armazenados e disparadores. Isso permite que ambos:

-   Capacidade de fazer operações CRUD transações de alto desempenho e consultas em documentos em um conjunto devido a profunda integração do tempo de execução de JavaScript diretamente no mecanismo de banco de dados. 
-   Uma modelagem natural de fluxo de controle, variável escopo e atribuição e integração de primitivos com transações de banco de dados de manipulação de exceção. Para obter mais detalhes sobre o suporte de DocumentDB para integração com o JavaScript, consulte a documentação de programação do JavaScript server lado.

###<a name="user-defined-functions-udfs"></a>Funções (UDFs) definidas pelo usuário
Juntamente com os tipos de já definidos neste artigo, DocumentDB SQL oferece suporte para funções de definidas pelo usuário (UDF). Em particular, UDFs escalares são suportados onde os desenvolvedores podem passar em zero ou muitos argumentos e retornar um resultado único argumento novamente. Cada um desses argumentos são verificados para sendo valores JSON legais.  

A sintaxe SQL de DocumentDB está estendida para oferecer suporte a lógica de aplicativo personalizado usando estas funções de definidas pelo usuário. UDFs podem ser registrados com DocumentDB e, em seguida, ser referenciadas como parte de uma consulta SQL. Na verdade, os UDFs exquisitely foram projetados para ser chamado pela consultas. Como um resultado para essa opção, UDFs não tem acesso ao objeto de contexto que têm os outros tipos de JavaScript (procedimentos armazenados e disparadores). Como executar consultas como somente leitura, eles podem executar em principal ou em réplicas secundárias. Portanto, UDFs foram projetados para executar em réplicas secundárias Diferentemente de outros tipos de JavaScript.

Abaixo está um exemplo de como uma UDF pode ser registrada no banco de dados DocumentDB, especificamente em um conjunto de documentos.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
O exemplo anterior cria uma UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores de cadeia de caracteres JSON `input` e `pattern` e verifica se as correspondências primeira o padrão especificado no segundo usando a função de string do JavaScript.


Agora podemos usar esta UDF em uma consulta em uma projeção. UDFs devem ser qualificados com o prefixo diferencia maiusculas de minúsculas "udf." Quando chamado de consultas. 

>[AZURE.NOTE] Antes de 17/3/2015, DocumentDB com suporte a chamadas UDF sem o "udf". Selecione REGEX_MATCH() como o prefixo. Esse padrão de chamada foi substituída.  

**Consulta**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

O UDF também pode ser usado dentro de um filtro conforme mostrado no exemplo abaixo, também qualificado com o "udf". prefixo:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Em essência, UDFs são expressões escalares válidas e podem ser usados em projeções e filtros. 

Para expandir o poder dos UDFs, vejamos outro exemplo com lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Abaixo está um exemplo que exercícios UDF.

**Consulta**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Como os exemplos anteriores showcase, UDFs integram potência de linguagem JavaScript com o SQL DocumentDB para fornecer uma interface programável avançada para fazer a lógica complexa de procedimento, condicional com a Ajuda de recursos de tempo de execução de JavaScript embutidas.

DocumentDB SQL fornece os argumentos para os UDFs para cada documento na fonte no estágio atual (cláusula WHERE ou cláusula SELECT) de processamento UDF. O resultado é incorporado diretamente no pipeline de execução geral. Se as propriedades referido por UDF parâmetros não estão disponíveis no valor JSON, o parâmetro é considerado como indefinido e, portanto, a invocação de UDF inteiramente é ignorada. Da mesma forma se o resultado do UDF estiver indefinido, ele não será incluído no resultado. 

Em resumo, UDFs são excelentes ferramentas para fazer a lógica comercial complexa como parte da consulta.

### <a name="operator-evaluation"></a>Avaliação de operador
DocumentDB, o porque de ser um banco de dados JSON desenha parallels com os operadores JavaScript e sua semântica de avaliação. Enquanto DocumentDB tenta preservar a semântica de JavaScript em termos de suporte JSON, a avaliação de operação diverge em algumas situações.

Em DocumentDB SQL, ao contrário no SQL tradicional, os tipos de valores geralmente não são conhecidos até que os valores são realmente recuperados do banco de dados. Para executar consultas com eficiência, a maioria dos operadores têm requisitos de tipo estrito. 

DocumentDB SQL não executará conversões implícitas, ao contrário de JavaScript. Por exemplo, uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponde a documentos que contêm uma propriedade de idade cujo valor é 21. Qualquer outro documento cuja propriedade idade corresponde variações possivelmente infinita de cadeia de caracteres "21" ou outros como "021", "21.0", "0021", "00021", não haverá correspondência de etc. Isso é em contraste com o JavaScript onde os valores de cadeia de caracteres são implicitamente converter números (com base em operador, ex: = =). Essa opção é essencial para índice eficiente correspondência em DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>Consultas SQL com parâmetros
DocumentDB oferece suporte a consultas com parâmetros expressados com familiar @ notação. SQL parametrizada fornece robusto manipulando e escape de entrada do usuário, impedindo exposição acidental de dados por meio da inclusão de SQL. 

Por exemplo, você pode escrever uma consulta que usa o último nome e endereço estado como parâmetros e executá-la para vários valores de sobrenome e estado de endereço com base na entrada do usuário.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Essa solicitação pode, em seguida, ser enviada para DocumentDB como uma consulta parametrizada do JSON como mostrado abaixo.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

O argumento de início pode ser definido usando consultas com parâmetros como mostrado abaixo.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Valores de parâmetro podem ser qualquer JSON válido (cadeias de caracteres, números, boolianos, nulos, mesmo matrizes ou aninhada JSON). Também como DocumentDB é sem esquema, parâmetros não são validados em relação a qualquer tipo.

##<a name="built-in-functions"></a>Funções internas
DocumentDB também oferece suporte a um número de funções internas para operações comuns, que podem ser usadas dentro de consultas como funções definidas pelo usuário (UDFs).

<table>
<tr>
<td>Funções matemáticas</td> 
<td>ABS, teto, EXP, PLANTAS, LOG, LOG10, POWER, ARRED, entrada, raiz, quadrado, TRUNC, ACOS, ASEN, ATAN, ATN2, COS, COT, graus, PI, radianos, sen e TAN</td>
</tr>
<tr>
<td>Tipo de verificação de funções</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funções de cadeia de caracteres</td>   
<td>CONCAT, contém, ENDSWITH, INDEX_OF, à esquerda, comprimento, inferior, LTRIM, substituir, REPLICAR, REVERSÃO, direita, RTRIM, STARTSWITH, SUBCADEIA e superior</td>
</tr>
<tr>
<td>Funções de matriz</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE</td>
</tr>
<tr>
<td>Funções espaciais</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID e ST_ISVALIDDETAILED</td>
</tr>
</table>  

Se estiver usando uma função definida pelo usuário (UDF) para o qual uma função interna agora está disponível, você deve usar a função interna correspondente como ele vai ser mais rápido executar e com mais eficiência. 

### <a name="mathematical-functions"></a>Funções matemáticas
Funções matemáticas realizar um cálculo, geralmente com base em valores de entrada que são fornecidos como argumentos e retornam um valor numérico. Aqui está uma tabela de funções matemáticas internas suportadas.

<table>
<tr>
<td><strong>Uso</strong></td>
<td><strong>Descrição</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Retorna o valor absoluto (positivo) da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">TETO (num_expr)</a></td> 
<td>Retorna o menor valor de número inteiro maior ou igual a, a expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">ARREDMULTB (num_expr)</a></td> 
<td>Retorna o maior inteiro menor ou igual à expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Retorna o expoente da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Retorna o logaritmo natural da expressão numérica especificada ou o logaritmo usando a base especificada</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Retorna o valor de logarítmico de base 10 da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ARRED (num_expr)</a></td> 
<td>Retorna um valor numérico, arredondado para o valor de número inteiro mais próximo.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">Truncar (num_expr)</a></td> 
<td>Retorna um valor numérico, truncado para o valor de número inteiro mais próximo.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">RAIZ (num_expr)</a></td>   
<td>Retorna a raiz quadrada da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">Quadrado (num_expr)</a></td>   
<td>Retorna o quadrado da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POTÊNCIA (num_expr, num_expr)</a></td>   
<td>Retorna o poder da expressão numérica especificada para o valor especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SINAL (num_expr)</a></td>   
<td>Retorna o valor de entrada (-1, 0, 1) da expressão numérica especificada.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>Retorna o ângulo em radianos, cujo cosseno é a expressão numérica especificada; também chamado de arco cosseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASEN (num_expr)</a></td>   
<td>Retorna o ângulo em radianos, cujo seno é a expressão numérica especificada. Isso se chama o arco seno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>Retorna o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isso se chama o arco tangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Retorna o ângulo em radianos, entre o positivos do eixo x e o raio a partir da origem ponto (y, x), onde x e y são os valores das duas expressões flutuar especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Retorna o cosseno trigonométrica do ângulo especificado, em radianos, na expressão especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRAUS (num_expr)</a></td> 
<td>Retorna o ângulo correspondente em graus de um ângulo especificado em radianos.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Retorna o valor da constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANOS (num_expr)</a></td> 
<td>Retorna radianos quando uma expressão numérica, em graus, é inserida.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SEN (num_expr)</a></td> 
<td>Retorna o seno trigonométrica do ângulo especificado, em radianos, na expressão especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Retorna a tangente de expressão de entrada, na expressão especificada.</td>
</tr>

</table> 

Por exemplo, agora você pode executar consultas semelhante ao seguinte:

**Consulta**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

A principal diferença entre as funções do DocumentDB em comparação com o ANSI SQL é que eles são criados para funcionar bem com dados de esquema sem esquema e mistas. Por exemplo, se você tiver um documento onde a propriedade tamanho está ausente ou tem um valor não numérico, como "desconhecida", o documento será ignorado, em vez de retornar um erro.

### <a name="type-checking-functions"></a>Tipo de verificação de funções
As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL. Funções de verificação de tipo podem ser usadas para determinar o tipo de propriedades dentro de documentos dinamicamente quando ela é variável ou desconhecido. Aqui está uma tabela de funções de verificação de tipo interna com suporte.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é uma matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é um booliano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é um número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é um objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é uma cadeia de caracteres.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Retorna um booliano que indica se a propriedade foi atribuída um valor.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Retorna um booliano que indica se o tipo do valor é uma cadeia de caracteres, o número, o booliano ou o nulo.</td>
</tr>

</table>

Usando essas funções, agora você pode executar consultas semelhante ao seguinte:

**Consulta**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funções de cadeia de caracteres
As seguintes funções escalares efetuar uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, o valor numérico ou booliano. Aqui está uma tabela de funções de cadeia de caracteres interno:

Uso|Descrição
---|---
[COMPRIMENTO (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Retorna o número de caracteres da expressão de cadeia de caracteres especificado
[CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Retorna uma cadeia de caracteres que é o resultado de concatenar dois ou mais valores de cadeia de caracteres.
[Subcadeia de caracteres (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Retorna a parte de uma expressão de cadeia de caracteres.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Retorna um valor booliano que indica se a primeira cadeia expressão termina com a segunda
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Retorna um valor booliano que indica se a primeira cadeia expressão termina com a segunda
[CONTÉM (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Retorna um valor booliano que indica se a primeira cadeia expressão contiver o segundo.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Retorna a posição inicial da primeira ocorrência da segunda cadeia expressão dentro da primeira expressão de cadeia de caracteres especificado ou -1 se a cadeia de caracteres não for encontrada.
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Retorna a parte esquerda de uma cadeia de caracteres com o número especificado de caracteres.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Retorna a parte direita de uma cadeia de caracteres com o número especificado de caracteres.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Retorna uma expressão de cadeia de caracteres após ele remove espaços em branco à esquerda.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Retorna uma expressão de cadeia de caracteres após truncar tudo em branco à direita.
[INFERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Retorna uma expressão de cadeia de caracteres depois de converter dados de caractere maiusculo em minúsculas.
[SUPERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Retorna uma expressão de cadeia de caracteres após converter dados de caractere minúsculas para maiusculas.
[Substituir (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Substitui todas as ocorrências de um valor de cadeia de caracteres especificado por outro valor de cadeia de caracteres.
[DUPLICAR (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Repete um valor de cadeia de caracteres de um número especificado de vezes.
[REVERSÃO (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Retorna a ordem inversa de um valor de cadeia de caracteres.

Usando essas funções, agora você pode executar consultas semelhante ao seguinte. Por exemplo, você pode retornar o nome da família em maiusculas da seguinte maneira:

**Consulta**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou concatenar cadeias de caracteres como neste exemplo:

**Consulta**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funções de cadeia de caracteres também podem ser usadas na cláusula WHERE para filtrar os resultados, como no exemplo a seguir:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funções de matriz
As seguintes funções escalares efetuam uma operação em um valor de entrada de matriz e retorno numérico, valor booliano ou matriz. Aqui está uma tabela de funções de matriz interno:

Uso|Descrição
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Retorna o número de elementos da expressão matriz especificada.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Retorna uma matriz que é o resultado de concatenar dois ou mais valores de matriz.
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Retorna um booliano que indica se a matriz contém o valor especificado.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Retorna a parte de uma expressão de matriz.

Funções de matriz podem ser usadas para manipular matrizes dentro JSON. Por exemplo, aqui está uma consulta que retorna todos os documentos onde um dos pais é "Robin Wakefield". 

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Aqui está outro exemplo que usa ARRAY_LENGTH para obter o número de filhos por família.

**Consulta**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funções espaciais

DocumentDB é compatível com as seguintes funções internas de abrir geoespaciais Consortium OGC () para consultar geoespaciais. Para obter mais detalhes sobre geoespaciais suporte em DocumentDB, consulte [trabalhar com dados geoespaciais em DocumentDB do Azure](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Retorna a distância entre as duas expressões de ponto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retorna uma expressão booliana que indica se o ponto de GeoJSON especificado no primeiro argumento é dentro do polígono GeoJSON no segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retorna um valor booliano que indica se a expressão especificada de ponto ou polígono GeoJSON é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Retorna um valor JSON contendo um booliano valor se a expressão especificada de ponto ou polígono GeoJSON é válida e inválido, além do motivo como um valor de cadeia de caracteres.</td>
</tr>
</table>

Funções espaciais podem ser usadas para executar consultas de proximidade em relação aos dados espaciais. Por exemplo, aqui está uma consulta que retorna todos os documentos família que estão dentro de 30 km do local especificado usando a função interna ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se você incluir indexação espacial em sua política de indexação, em seguida, "consultas distância" serão atendidas com eficiência por meio do índice. Para obter mais detalhes sobre indexação espacial, consulte a seção abaixo. Se você não tiver um índice espacial para caminhos especificado, você ainda pode executar consultas espaciais especificando `x-ms-documentdb-query-enable-scan` cabeçalho de solicitação com o valor definido como "true". No .NET, isso pode ser feito passando o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como true. 

ST_WITHIN pode ser usado para verificar se um ponto está dentro de um polígono. Polígonos são usados para representar limites como códigos postais, limites de estado ou formações naturais. Novamente se você incluir indexação espacial em sua política de indexação, em seguida, "comem" consultas serão atendidas com eficiência por meio do índice. 

Argumentos de polígono em ST_WITHIN podem conter apenas um único toque, ou seja, os polígonos não devem conter furos neles. Verifique os [limites de DocumentDB](documentdb-limits.md) para o número máximo de pontos permitidos em um polígono para uma consulta ST_WITHIN.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Semelhante a como incompatíveis works tipos na consulta de DocumentDB, se o valor de local especificado em um argumento é inválido ou formado, em seguida, ele irá avaliar **indefinido** e avaliado documento para ser ignorado dos resultados da consulta. Se sua consulta não retornar resultados, execute o ST_ISVALIDDETAILED para a depuração, por que o tipo de spatail é inválido.     

ST_ISVALID e ST_ISVALIDDETAILED podem ser usados para verificar se um objeto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com uma valor de latitude de intervalo (-132.8) de ausência. ST_ISVALID retorna apenas um valor booliano e ST_ISVALIDDETAILED retorna o booleanos e uma cadeia de caracteres que contém o motivo por que ele é considerado inválido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Essas funções também podem ser usadas para validar polígonos. Por exemplo, aqui usamos ST_ISVALIDDETAILED para validar um polígono que não é fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Que quebra o espaciais funções e a sintaxe SQL para DocumentDB. Agora vamos dar uma olhada em como LINQ consultando funciona e como ele interage com a sintaxe temos visto até o momento.

## <a name="linq-to-documentdb-sql"></a>LINQ para SQL DocumentDB
LINQ é um modelo de programação do .NET que expresse computação como consultas em fluxos de objetos. DocumentDB fornece um cliente para a biblioteca de lado para interface com LINQ, facilitando uma conversão entre objetos JSON e .NET e um mapeamento de um subconjunto do LINQ consulta às consultas de DocumentDB. 

A imagem abaixo mostra a arquitetura de oferecer suporte a consultas LINQ usando DocumentDB.  Usando o cliente de DocumentDB, os desenvolvedores podem criar um objeto de **IQueryable** que consulta diretamente o provedor de consulta DocumentDB, que traduz a consulta LINQ para uma consulta de DocumentDB. A consulta é então passada ao servidor DocumentDB para recuperar um conjunto de resultados no formato JSON. Resultados retornados estão desserializados em um fluxo de objetos .NET no lado do cliente.

![Arquitetura de oferecer suporte a consultas LINQ usando DocumentDB - sintaxe SQL, linguagem de consulta JSON, conceitos de banco de dados e consultas SQL][1]
 


### <a name="net-and-json-mapping"></a>Mapeamento de JSON e .NET
O mapeamento entre objetos .NET e documentos JSON é natural - cada campo de membro de dados é mapeado para um objeto JSON, onde o nome do campo é mapeado para a parte de "chave" do objeto e a parte de "valor" é repetidamente mapeado para a parte do valor do objeto. Considere o exemplo a seguir. O objeto de família criado é mapeado para o documento JSON, conforme mostrado abaixo. E vice-versa, o documento JSON é mapeado para um objeto .NET.

**Classe c#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL
O provedor de consultas DocumentDB executa um mapeamento de esforço melhor de uma consulta LINQ em uma consulta de DocumentDB SQL. Na seguinte descrição, vamos supor que o leitor tenha familiaridade básica do LINQ.

Primeiro, para o sistema de tipo, oferecemos suporte para todos os tipos de primitivos JSON – tipos numéricos, booliano, cadeia de caracteres e nulo. Somente esses tipos JSON são suportados. As seguintes expressões escalares são suportadas.

-   Valores de constantes – estes inclui valores constantes dos tipos de dados primitivos no momento em que a consulta é avaliada.

-   Expressões de índice de matriz/propriedade – essas expressões referem-se para a propriedade de um objeto ou um elemento de matriz.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Expressões aritméticas - incluem expressões aritméticas comuns em valores numéricos e booleanos. Para uma lista completa, consulte a especificação de SQL.

        2 * family.children[0].grade;
        x + y;

-   Expressão de comparação de cadeia de caracteres - incluem comparando um valor de cadeia de caracteres a algum valor constante de cadeia de caracteres.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Objeto/matriz expressão de criação - essas expressões retorno um objeto do tipo de valor composto ou anônima ou uma matriz de tais objetos. Esses valores podem ser aninhados.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Lista de operadores LINQ com suporte
Aqui está uma lista de operadores LINQ com suporte no provedor LINQ incluída com o SDK do .NET DocumentDB.

-   **Selecione**: projeções traduzem para SQL SELECT incluindo construção de objeto
-   **Onde**: filtros traduzir para o SQL WHERE e suporte a tradução entre & &, | | e! para os operadores de SQL
-   **SelectMany**: permite desenrolamento de matrizes à cláusula SQL JOIN. Pode ser usado para cadeia/aninhar expressões para filtrar em elementos de matriz
-   **OrderBy e OrderByDescending**: traduz para ORDER BY crescente/decrescente:
-   **CompareTo**: traduz para comparações de intervalo. Comumente usados para cadeias de caracteres, pois elas não foram comparáveis no .NET
-   **Levar**: converte para o início de SQL para limitar os resultados de uma consulta
-   **Funções matemáticas**: suporta tradução de. Abs, Acos, ASEN do líquido, Atan, teto, Cos, Exp, plantas, Log, Log10, Pow, ARRED, entrada, sen, raiz, Tan, truncar às funções internas SQL equivalentes.
-   **Funções de cadeia de caracteres**: suporta tradução de. Concat, contém, EndsWith do líquido, IndexOf, contagem, ToLower, TrimStart, substituir, reversão, TrimEnd, StartsWith, subcadeia, ToUpper às funções internas SQL equivalentes.
-   **Funções de matriz**: suporta tradução de. Do líquido Concat, contém e contar para as funções internas do SQL equivalentes.
-   **Funções de extensão geoespaciais**: dá suporte a tradução de métodos de esboço distância, dentro, IsValid e IsValidDetailed para as funções internas do SQL equivalentes.
-   **Função de extensão de função definida pelo usuário**: função definida pelo tradução suporta do método esboço UserDefinedFunctionProvider.Invoke para o usuário correspondente.
-   **Diversos**: suporta tradução dos adesão e operadores condicionais. Pode traduzir contém a cadeia de caracteres contém, ARRAY_CONTAINS ou o SQL IN dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
Aqui estão alguns exemplos que ilustram como alguns dos operadores de consulta padrão do LINQ são convertidos para baixo até DocumentDB consultas.

#### <a name="select-operator"></a>Selecione o operador
A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Expressão de lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expressão de lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expressão de lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operador SelectMany
A sintaxe é `input.SelectMany(x => f(x))`, onde `f` é uma expressão escalar que retorna um tipo de conjunto.

**Expressão de lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Onde operador
A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar que retorna um valor booliano.

**Expressão de lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expressão de lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Composto consultas SQL
Os operadores acima podem ser compostos por para formar consultas mais eficazes. Como DocumentDB oferece suporte a coleções aninhadas, a composição pode ser concatenada ou aninhada.

#### <a name="concatenation"></a>Concatenação 

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um recurso opcional `SelectMany` consulta seguido de vários `Select` ou `Where` operadores.


**Expressão de lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expressão de lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expressão de lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expressão de lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` onde p é um `Select`, `SelectMany`, ou `Where` operador.

Em uma consulta aninhada, a consulta interna é aplicada a cada elemento da coleção externa. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos na coleção externa como autojunções.

**Expressão de lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expressão de lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expressão de lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Consultas SQL em execução
DocumentDB expõe recursos por meio de uma API REST que pode ser chamado por qualquer linguagem capaz de fazer solicitações HTTP/HTTPS. Além disso, DocumentDB oferece bibliotecas de programação para vários idiomas populares como .NET, Node, JavaScript e Python. Suportam a API REST e várias bibliotecas consultando através de SQL. O SDK do .NET suporta LINQ consultando além de SQL.

Os exemplos a seguir mostram como criar uma consulta e enviá-lo em relação a uma conta de banco de dados DocumentDB.

### <a name="rest-api"></a>API REST
DocumentDB oferece um modelo de programação RESTful aberto sobre HTTP. Contas de banco de dados podem ser provisionadas usando uma assinatura do Azure. O modelo de recursos DocumentDB consiste em um conjuntos de recursos em uma conta de banco de dados, cada um dos quais é de endereçamento usando um URI lógico e estável. Um conjunto de recursos é conhecido como um feed neste documento. Uma conta de banco de dados consiste em um conjunto de bancos de dados, cada um contendo vários conjuntos, cada uma das quais no virada conter documentos, UDFs e outros tipos de recursos.

O modelo de interação básica com esses recursos é por meio dos verbos HTTP GET, colocar, POSTAGEM e excluir com seu interpretação padrão. O verbo POST é usado para a criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta de DocumentDB. Consultas são sempre lidos somente operações sem efeitos de lado.

Os exemplos a seguir mostram uma POSTAGEM para uma consulta de DocumentDB feita uma coleção contendo os dois documentos de amostra que analisamos até o momento. A consulta tem um filtro simples na propriedade nome JSON. Observe o uso da `x-ms-documentdb-isquery` e tipo de conteúdo: `application/query+json` cabeçalhos para indicar que a operação é uma consulta.


**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


O segundo exemplo mostra uma consulta mais complexa que retorne vários resultados de junção.

**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Se não se ajusta a resultados de uma consulta dentro de uma única página de resultados, então a API REST retornará um token de continuação através do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem pagina resultados incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado por meio do `x-ms-max-item-count` cabeçalho número.

Para gerenciar a política de consistência de dados para consultas, use o `x-ms-consistency-level` cabeçalho como todas as solicitações de API REST. Para a consistência de sessão, é necessário também repetir a última `x-ms-session-token` cabeçalho de Cookie na solicitação de consulta. Observe que a política de indexação da coleção consultado também pode influenciar a consistência dos resultados da consulta. Com o padrão indexação configurações de política, conjuntos do índice é sempre atual com o conteúdo do documento e resultados de consulta corresponderão a consistência escolhida para os dados. Se a política de indexação é relaxada para Lazy, consultas podem retornar resultados obsoletos. Para obter mais informações, consulte [Níveis de consistência DocumentDB] [consistency-levels].

Se a política de indexação configurada na coleção não oferecer suporte a consulta especificada, o servidor de DocumentDB retorna 400 "solicitação inválida". Isso é retornado para consultas de intervalo em caminhos configurados para pesquisas de hash (igualdade) e para caminhos explicitamente excluídos da indexação de. O `x-ms-documentdb-query-enable-scan` cabeçalho pode ser especificado para permitir a consulta executar uma verificação quando um índice não está disponível.

### <a name="c-net-sdk"></a>SDK DO C# (.NET)
O SDK do .NET suporta LINQ e SQL consultando. O exemplo a seguir mostra como executar a consulta de filtro simples introduzida anteriormente neste documento.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Este exemplo compara duas propriedades de igualdade dentro de cada documento e usa projeções anônimas. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


O próximo exemplo mostra ligações, expressadas por meio de LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



O cliente .NET automaticamente itera em todas as páginas de resultados da consulta nos blocos foreach como mostrado acima. As opções de consulta introduzidas na seção API REST também estão disponíveis no SDK do .NET usando o `FeedOptions` e `FeedResponse` classes no método CreateDocumentQuery. O número de páginas pode ser controlado usando o `MaxItemCount` configuração. 

Você pode controlar também explicitamente paginação criando `IDocumentQueryable` usando o `IQueryable` objeto, em seguida, lendo o` ResponseContinuationToken` valores e passando-los de volta como `RequestContinuationToken` em `FeedOptions`. `EnableScanInQuery`podem ser definidas para habilitar verificações quando a consulta não forem aceitas à política de indexação configurada. Para conjuntos de particionado, você pode usar `PartitionKey` para executar a consulta em relação a uma única partição (embora DocumentDB pode automaticamente extrair isso o texto da consulta), e `EnableCrossPartitionQuery` para executar consultas que talvez precise ser executado em várias partições. 

Consulte [exemplos de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net) para obter mais amostras que contêm consultas. 

### <a name="javascript-server-side-api"></a>API de servidor de JavaScript 
DocumentDB fornece um modelo de programação para executar lógica de aplicativo JavaScript com base diretamente nas coleções de arquivos usando procedimentos armazenados e disparadores. A lógica de JavaScript registrada em um nível de conjunto, em seguida, pode executar operações de banco de dados as operações nos documentos do conjunto de determinado. Essas operações são empacotadas em ambiente transações ACID.

O exemplo a seguir mostram como usar o queryDocuments na API do servidor de JavaScript para fazer consultas a partir do interior procedimentos armazenados e disparadores.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Funções de agregação

Suporte nativo para funções agregadas está em andamento, mas se você precisar funcionalidade count ou sum enquanto isso, você pode obter o mesmo resultado usando métodos diferentes.  

No caminho de leitura:

- Você pode executar funções de agregação, recuperar os dados e fazendo uma contagem localmente. Ela tem aconselhável para usar uma projeção de consulta barato como `SELECT VALUE 1` em vez de documento completo como `SELECT * FROM c`. Isso ajuda a maximizar o número de documentos processados em cada página de resultados, evitando assim adicionais ida e volta para o serviço, se necessário.
- Você também pode usar um procedimento armazenado para minimizar latência de rede em idas repetidas. Para um exemplo de procedimento armazenado que calcula a contagem de uma consulta de determinado filtro, consulte [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). O procedimento armazenado pode habilitar os usuários combinar lógica de negócios sofisticados juntamente com fazendo agregações de maneira eficiente.

No caminho de gravação:

- Outro padrão comum é previamente agregar os resultados no caminho "gravação". Isso é especialmente atraente quando o volume de solicitações de "lido" é maior do que de solicitações de "escrever". Uma vez previamente agregado, os resultados estão disponíveis com um único ponto solicitação de leitura.  A melhor maneira de agregar previamente no DocumentDB é configurar um disparador que é invocado com cada "gravação" e atualizar um documento de metadados que tenha os resultados mais recentes para a consulta que está sendo materializada. Por exemplo, examine o exemplo [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) , que atualiza o minSize, maxSize e totalSize do documento para o conjunto de metadados. O exemplo pode ser estendido para atualizar um contador, soma, etc.

##<a name="references"></a>Referências
1.  [Introdução ao Azure DocumentDB][introduction]
2.  [Especificação DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Exemplos de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
4.  [Níveis de consistência DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Especificação de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Técnicas de avaliação de consulta para grandes bancos de dados [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Processamento em sistemas de banco de dados relacional paralelo, computador IEEE sociedade Press, 1994 da consulta
11. Lu, Ooi, Tan processamento nos sistemas de banco de dados relacional paralelo, computador IEEE sociedade Press, 1994 da consulta.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Paulo Tomkins: latino porco: não-para-idioma estrangeiro para processamento de dados, SIGMOD 2008.
13.     G. Graefe. A estrutura em cascata para otimização de consulta. Eng IEEE dados. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
