<properties
   pageTitle="Funções do Gerenciador de recursos de modelo | Microsoft Azure"
   description="Descreve as funções para usar em um modelo do Gerenciador de recursos do Azure para recuperar valores, trabalhar com cadeias de caracteres e caracteres alfanuméricos e recuperar informações de implantação."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-template-functions"></a>Funções de modelo do Azure Gerenciador de recursos

Este tópico descreve todas as funções que você pode usar em um modelo do Gerenciador de recursos do Azure.

Funções de modelo e seus parâmetros diferenciam maiusculas de minúsculas. Por exemplo, o gerente de recursos resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma. Quando avaliada, a menos que a função expressamente modifica caso (como toUpper ou toLower), a função preserva as maiusculas e minúsculas. Determinados tipos de recursos podem ter requisitos casos independentemente de como as funções são avaliadas.

## <a name="numeric-functions"></a>Funções numéricas

Gerenciador de recursos fornece as seguintes funções para trabalhar com números inteiros:

- [Adicionar](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [Mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### <a name="add"></a>Adicionar

**Adicionar (operand1, operand2)**

Retorna a soma dos dois inteiros fornecidos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| operand1                           |   Sim    | Primeiro inteiro para adicionar.
| operand2                           |   Sim    | Segundo inteiro para adicionar.

O exemplo a seguir adiciona dois parâmetros.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### <a name="copyindex"></a>copyIndex

**copyIndex(offset)**

Retorna o índice atual de um loop de iteração. 

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| deslocamento                           |   Não    | O valor a ser adicionado ao valor de iteração atual.

Esta função sempre é usada com um objeto de **cópia** . Para obter uma descrição completa de como você usar **copyIndex**, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md).

O exemplo a seguir mostra um loop de cópia e o valor de índice incluído no nome. 

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### <a name="div"></a>div

**div (operand1, operand2)**

Retorna a divisão dos dois inteiros fornecidos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| operand1                           |   Sim    | Inteiro seja dividido.
| operand2                           |   Sim    | Número inteiro que é usado para dividir. Não pode ser 0.

O exemplo a seguir divide um parâmetro por outro parâmetro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### <a name="int"></a>int

**Int(valueToConvert)**

Converte o valor especificado em inteiro.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Sim    | O valor a ser convertido para inteiro. O tipo de valor só pode ser String ou Integer.

O exemplo a seguir converte o valor de parâmetro fornecido pelo usuário para inteiro.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### <a name="mod"></a>Mod

**Mod (operand1, operand2)**

Retorna o resto da divisão inteiro usando os dois inteiros fornecidos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| operand1                           |   Sim    | Inteiro seja dividido.
| operand2                           |   Sim    | Número inteiro que é usado para dividir, deve ser diferente de 0.

O exemplo a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### <a name="mul"></a>mul

**mul (operand1, operand2)**

Retorna a multiplicação dos dois inteiros fornecidos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| operand1                           |   Sim    | Primeiro inteiro para multiplicar.
| operand2                           |   Sim    | Segundo inteiro para multiplicar.

O exemplo a seguir multiplica um parâmetro por outro parâmetro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### <a name="sub"></a>sub

**sub (operand1, operand2)**

Retorna a subtração dos dois inteiros fornecidos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| operand1                           |   Sim    | Número inteiro que é subtraído do.
| operand2                           |   Sim    | Número inteiro que é subtraído.

O exemplo a seguir subtrai um parâmetro de outro parâmetro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## <a name="string-functions"></a>Funções de cadeia de caracteres

Gerenciador de recursos fornece as seguintes funções para trabalhar com cadeias de caracteres:

- [na Base 64](#base64)
- [concat](#concat)
- [comprimento](#lengthstring)
- [padLeft](#padleft)
- [Substituir](#replace)
- [Ignorar](#skipstring)
- [Dividir](#split)
- [cadeia de caracteres](#string)
- [subcadeia de caracteres](#substring)
- [leve](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [Cortar](#trim)
- [uniqueString](#uniquestring)
- [URI](#uri)


<a id="base64" />
### <a name="base64"></a>na Base 64

**na Base 64 (inputString)**

Retorna a representação de sequência de entrada base 64.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| inputString                        |   Sim    | O valor de cadeia de caracteres para retornar como uma representação na Base 64.

O exemplo a seguir mostra como usar a função na Base 64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### <a name="concat---string"></a>concat - cadeia de caracteres

**concat (cadeia1, cadeia2 string3,....)**

Combina vários valores de cadeia de caracteres e retorna a cadeia de caracteres concatenada. 

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| cadeia1                        |   Sim    | Um valor de cadeia de caracteres para concatenar.
| cadeias de caracteres adicionais             |   Não     | Valores para concatenar de cadeia de caracteres.

Esta função pode realizar qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes para os parâmetros. Para obter um exemplo de concatenar matrizes, consulte [concat - matriz](#concatarray).

O exemplo a seguir mostra como combinar vários valores de cadeia de caracteres para retornar uma cadeia de caracteres concatenada.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### <a name="length---string"></a>comprimento - cadeia de caracteres

**Length(String)**

Retorna o número de caracteres em uma cadeia de caracteres.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| cadeia de caracteres                        |   Sim    | O valor de cadeia de caracteres a ser usado para obter o número de caracteres.

Para obter um exemplo do uso de comprimento com uma matriz, consulte [comprimento - matriz](#length).

O exemplo a seguir retorna o número de caracteres em uma cadeia de caracteres. 

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### <a name="padleft"></a>padLeft

**padLeft (valueToPad, totalLength, paddingCharacter)**

Retorna uma cadeia de alinhado à direita, adicionando caracteres à esquerda até atingir o comprimento especificado total.
  
| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| valueToPad                         |   Sim    | A cadeia de caracteres ou int para alinhar à direita.
| totalLength                        |   Sim    | O número total de caracteres na cadeia de caracteres retornada.
| paddingCharacter                   |   Não     | O caractere a ser usado para a esquerda de preenchimento até que o comprimento total é alcançado. O valor padrão é um espaço.

O exemplo a seguir mostra como acrescentar o valor do parâmetro fornecido pelo usuário, adicionando o caractere zero, até que a cadeia de caracteres atinja 10 caracteres. Se o valor de parâmetro original é mais de 10 caracteres, sem caracteres são adicionados.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### <a name="replace"></a>Substituir

**Substituir (originalString, oldCharacter, newCharacter)**

Retorna uma nova cadeia de caracteres com todas as ocorrências de um caractere na cadeia especificada substituída por outro caractere.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| originalString                     |   Sim    | A cadeia de caracteres que tem todas as ocorrências de um caractere substituído por outro caractere.
| oldCharacter                       |   Sim    | O caractere ser removido da cadeia de caracteres original.
| newCharacter                       |   Sim    | O caractere para adicionar no lugar do caractere removido.

O exemplo a seguir mostra como remover todos os traços de cadeia de caracteres fornecido pelo usuário.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### <a name="skip---string"></a>Ignorar - cadeia de caracteres
**Ignorar (originalValue, numberToSkip)**

Retorna uma cadeia de caracteres com todos os caracteres após o número especificado na cadeia de caracteres.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sim    | A cadeia de caracteres a ser usado para pular.
| numberToSkip                       |   Sim    | O número de caracteres para ignorar. Se esse valor for 0 ou menos, todos os caracteres na cadeia de caracteres são retornados. Se for maior que o comprimento da cadeia de caracteres, uma cadeia de caracteres vazia é retornada. 

Para obter um exemplo do uso de ignorar com uma matriz, consulte [Ignorar - matriz](#skip).

O exemplo a seguir ignora o número especificado de caracteres na cadeia de caracteres.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### <a name="split"></a>Dividir

**Dividir (inputString, delimiterString)**

**Dividir (inputString, delimiterArray)**

Retorna uma matriz de cadeias de caracteres que contém as subsequências da entrada cadeia de caracteres que são delimitadas por delimitadores especificados.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| inputString                        |   Sim    | A cadeia de caracteres para dividir.
| delimitador                          |   Sim    | O delimitador usar, pode ser uma única cadeia de caracteres ou uma matriz de cadeias de caracteres.

O exemplo a seguir divide a cadeia de caracteres de entrada com uma vírgula.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

O exemplo a seguir divide a cadeia de caracteres de entrada com uma vírgula ou ponto e vírgula.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### <a name="string"></a>cadeia de caracteres

**String(valueToConvert)**

Converte o valor especificado em uma cadeia de caracteres.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Sim    | O valor para converter a cadeia de caracteres. Qualquer tipo de valor pode ser convertido, incluindo matrizes e objetos.

O exemplo a seguir converte os valores de parâmetro fornecido pelo usuário cadeias de caracteres.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### <a name="substring"></a>subcadeia de caracteres

**subcadeia de caracteres (stringToParse, startIndex, comprimento)**

Retorna uma subcadeia de caracteres que começa na posição do caractere especificado e contém o número de caracteres especificado.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| stringToParse                     |   Sim    | A cadeia de caracteres original do qual a subcadeia de caracteres é extraída.
| startIndex                         | Não      | Com base em zero caractere posição inicial para a subcadeia de caracteres.
| comprimento                             | Não      | O número de caracteres para a subcadeia de caracteres.

O exemplo a seguir extrai os três primeiros caracteres de um parâmetro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### <a name="take---string"></a>leve - cadeia de caracteres
**leve (originalValue, numberToTake)**

Retorna uma cadeia de caracteres com o número especificado de caracteres do início da cadeia de caracteres.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sim    | A cadeia de caracteres para tirar os caracteres da.
| numberToTake                       |   Sim    | O número de caracteres para tirar. Se esse valor for 0 ou menos, uma cadeia de caracteres vazia será retornada. Se for maior que o comprimento da cadeia de caracteres especificado, todos os caracteres na cadeia de caracteres são retornados.

Para obter um exemplo do uso de fazer com uma matriz, consulte [levar - matriz](#take).

O exemplo a seguir apresenta o número especificado de caracteres de cadeia de caracteres.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### <a name="tolower"></a>toLower

**toLower(stringToChange)**

Converte a cadeia especificada em minúsculas.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Sim    | A cadeia de caracteres para converter em minúsculas.

O exemplo a seguir converte o valor de parâmetro fornecido pelo usuário para minúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### <a name="toupper"></a>toUpper

**toUpper(stringToChange)**

Converte a cadeia especificada em maiusculas.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Sim    | A cadeia de caracteres para converter em maiusculas.

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em maiusculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### <a name="trim"></a>Cortar

**ARRUMAR (stringToTrim)**

Remove todos os caracteres de espaço em branco à direita e à esquerda a cadeia especificada.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| stringToTrim                       |   Sim    | A cadeia de caracteres para aparar.

O exemplo a seguir remove os caracteres de espaço em branco do valor de parâmetro fornecido pelo usuário.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### <a name="uniquestring"></a>uniqueString

**uniqueString (baseString,...)**

Cria uma cadeia de hash determinante com base nos valores fornecidos como parâmetros. 

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| baseString      |   Sim    | A cadeia de caracteres usada na função hash para criar uma cadeia de caracteres exclusiva.
| parâmetros adicionais conforme necessário    | Não       | Você pode adicionar quantas cadeias de caracteres conforme necessário para criar o valor que especifica o nível de exclusividade.

Esta função é útil quando você precisa criar um nome exclusivo para um recurso. Você forneça os valores de parâmetro que limitam o escopo de exclusividade para o resultado. Você pode especificar se o nome é exclusivo para baixo até a assinatura, grupo de recursos ou implantação. 

O valor retornado não é uma cadeia de caracteres aleatória, mas em vez disso, o resultado de uma função de hash. O valor retornado é 13 caracteres. Não é exclusivo. Talvez você queira combinar o valor com um prefixo a partir de sua convenção de nomenclatura para criar um nome que seja significativo. O exemplo a seguir mostra o formato do valor retornado. Claro, o valor real variará por parâmetros fornecidos.

    tcvhiyu5h2o5o

Os exemplos a seguir mostram como usar uniqueString para criar um valor exclusivo para níveis comumente usados.

Exclusivo com escopo de assinatura

    "[uniqueString(subscription().subscriptionId)]"

Exclusivo com escopo de grupo de recursos

    "[uniqueString(resourceGroup().id)]"

Exclusivo com escopo de implantação para um grupo de recursos

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
O exemplo a seguir mostra como criar um nome exclusivo para uma conta de armazenamento com base em seu grupo de recursos (dentro do grupo de recursos o nome não é exclusiva se construída da mesma maneira).

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### <a name="uri"></a>URI

**URI (baseUri, relativeUri)**

Cria um URI absoluto combinando o baseUri e a cadeia de caracteres relativeUri.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| baseUri                            |   Sim    | A cadeia de caracteres de uri de base.
| relativeUri                        |   Sim    | A cadeia de caracteres de uri relativo para adicionar à cadeia de caracteres uri base.

O valor para o parâmetro **baseUri** pode incluir um arquivo específico, mas somente o caminho de base é usado ao construir o URI. Por exemplo, passando **http://contoso.com/resources/azuredeploy.json** como o baseUri resultados de parâmetro em um URI base de **http://contoso.com/resources/**.

O exemplo a seguir mostra como construir um link para um modelo aninhado baseado no valor do modelo pai.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## <a name="array-functions"></a>Funções de matriz

Gerenciador de recursos fornece várias funções para trabalhar com valores de matriz.

- [concat](#concatarray)
- [comprimento](#length)
- [Ignorar](#skip)
- [leve](#take)

Para obter uma matriz de valores de cadeia de caracteres delimitados por um valor, consulte [Dividir](#split).

<a id="concatarray" />
### <a name="concat---array"></a>concat - matriz

**concat (Matriz1, matriz2, matriz3,...)**

Combina várias matrizes e retorna a matriz concatenada. 

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| Matriz1                        |   Sim    | Uma matriz para concatenar.
| conjuntos adicionais             |   Não     | Matrizes para concatenar.

Esta função pode realizar qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes para os parâmetros. Para um exemplo de concatenar valores de cadeia de caracteres, consulte [concat - cadeia de caracteres](#concat).

O exemplo a seguir mostra como combinar duas matrizes.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### <a name="length---array"></a>comprimento - matriz

**Length(array)**

Retorna o número de elementos em uma matriz.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| matriz                        |   Sim    | A matriz a ser usado para obter o número de elementos.

Você pode usar esta função com uma matriz para especificar o número de iterações ao criar recursos. No exemplo a seguir, o parâmetro **siteNames** pode se referir a uma matriz de nomes para usar ao criar os sites.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Para obter mais informações sobre como usar essa função com uma matriz, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md). 

Para obter um exemplo do uso de comprimento com um valor de cadeia de caracteres, consulte [comprimento - cadeia de caracteres](#lengthstring).

<a id="skip" />
### <a name="skip---array"></a>pule - matriz
**Ignorar (originalValue, numberToSkip)**

Retorna uma matriz com todos os elementos após o número especificado na matriz.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sim    | A matriz a ser usado para pular.
| numberToSkip                       |   Sim    | O número de elementos para ignorar. Se esse valor for 0 ou menos, todos os elementos na matriz são retornados. Se for maior que o comprimento da matriz, uma matriz vazia é retornada. 

Para obter um exemplo do uso de ignorar com uma cadeia de caracteres, consulte [Ignorar - cadeia de caracteres](#skipstring).

O exemplo a seguir ignora o número especificado de elementos na matriz.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### <a name="take---array"></a>levar - matriz
**leve (originalValue, numberToTake)**

Retorna uma matriz com o número especificado de elementos desde o início da matriz.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sim    | A matriz para tirar os elementos de.
| numberToTake                       |   Sim    | O número de elementos para tirar. Se esse valor for 0 ou menos, uma matriz vazia será retornada. Se for maior que o comprimento da matriz determinado, todos os elementos na matriz são retornados.

Para obter um exemplo do uso leve com uma cadeia de caracteres, consulte [levar - cadeia de caracteres](#takestring).

O exemplo a seguir usa o número especificado de elementos da matriz.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## <a name="deployment-value-functions"></a>Funções de valor de implantação

Gerenciador de recursos fornece as funções a seguir para obter valores de seções do modelo e valores relacionados à implantação:

- [implantação](#deployment)
- [parâmetros](#parameters)
- [variáveis](#variables)

Para obter valores de recursos, grupos de recursos ou assinaturas, consulte [funções do recurso](#resource-functions).

<a id="deployment" />
### <a name="deployment"></a>implantação

**Deployment()**

Retorna informações sobre a operação de implantação atual.

Esta função retorna o objeto que é passado durante a implantação. As propriedades no objeto retornado diferem com base em se o objeto de implantação é passado como um link ou como um objeto em linha. 

Quando o objeto de implantação é passado em linha, como ao usar o parâmetro **- TemplateFile** no Azure PowerShell para apontar para um arquivo local, o objeto retornado tem o seguinte formato:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Quando o objeto é passado como um link, como ao usar o parâmetro **- TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato. 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

O exemplo a seguir mostra como usar deployment() para vincular a outro modelo com base no URI do modelo pai.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### <a name="parameters"></a>parâmetros

**parâmetros (nome do parâmetro)**

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção parâmetros do modelo.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| nome do parâmetro                      |   Sim    | O nome do parâmetro para retornar.

O exemplo a seguir mostra um uso simplificado da função parâmetros.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### <a name="variables"></a>variáveis

**variáveis (NOMEDAVARIÁVEL)**

Retorna o valor de variável. O nome da variável especificado deve ser definido na seção variáveis do modelo.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| Nome da variável                      |   Sim    | O nome da variável para retornar.

O exemplo a seguir usa um valor de variável.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## <a name="resource-functions"></a>Funções do recurso

Gerenciador de recursos fornece as funções a seguir para obter valores de recursos:

- [listKeys e lista {valor}](#listkeys)
- [provedores](#providers)
- [referência](#reference)
- [resourceGroup](#resourcegroup)
- [identificação de recurso](#resourceid)
- [assinatura](#subscription)

Para obter valores de parâmetros, variáveis ou a implantação atual, consulte [funções de valor de implantação](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />
### <a name="listkeys-and-listvalue"></a>listKeys e lista {valor}

**listKeys (resourceName ou resourceIdentifier, apiVersion)**

**lista {valor} (resourceName ou resourceIdentifier, apiVersion)**

Retorna os valores para qualquer tipo de recurso que ofereça suporte a operação de lista. O uso mais comum é **listKeys**. 
  
| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier |   Sim    | Identificador exclusivo para o recurso.
| apiVersion                         |   Sim    | Versão da API do estado de tempo de execução do recurso.

Qualquer operação que começa com **lista** pode ser usada uma função em seu modelo. As operações disponíveis incluem não apenas **listKeys**, mas também operações como **lista** **listAdminKeys**e **listStatus**. Para determinar quais tipos de recursos tem uma operação de lista, use o seguinte comando do PowerShell.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Ou, recuperar a lista com CLI do Azure. O exemplo a seguir recupera todas as operações de **apiapps**e usa o utilitário JSON [jq](http://stedolan.github.io/jq/download/) para filtrar apenas as operações de lista.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"

A identificação de recurso pode ser especificada, usando a [função de identificação de recurso](#resourceid) ou usando o formato **{providerNamespace} / {tipo de recurso} / {resourceName}**.

O exemplo a seguir mostra como as chaves primárias e secundárias de retorno de uma conta de armazenamento da seção de saídas.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

O objeto retornado de listKeys tem o seguinte formato:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### <a name="providers"></a>provedores

**provedores (providerNamespace, [tipo de recurso])**

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se você não fornecer um tipo de recurso, a função retorna todos os tipos com suporte para o provedor de recursos.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   Sim    | Namespace do provedor
| tipo de recurso                       |   Não     | O tipo de recurso no namespace especificado.

Cada tipo suportado é retornado no seguinte formato. Não há garantia ordenação de matriz.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

O exemplo a seguir mostra como usar a função de provedor de serviços:

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />
### <a name="reference"></a>referência

**referência (resourceName ou resourceIdentifier, [apiVersion])**

Retorna um objeto que representa o estado de tempo de execução do outro recurso.

| Parâmetro                          | Necessário | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier |   Sim    | Nome ou identificador exclusivo de um recurso.
| apiVersion                         |   Não     | Versão da API do recurso especificado. Inclua este parâmetro quando o recurso não está provisionado no mesmo modelo.

A função de **referência** deriva seu valor de estado de runtime e, portanto, não pode ser usada na seção variáveis. Ele pode ser usado na seção de saídas de um modelo.

Usando a função de referência, você declarar implicitamente que um recurso depende de outro recurso se o recurso referenciado está provisionado no mesmo modelo. Você não precisa também utilizar a propriedade **dependsOn** . A função não é avaliada até que o recurso referenciado concluiu implantação.

O exemplo a seguir faz referência a uma conta de armazenamento que é implantada no mesmo modelo.

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

O exemplo a seguir faz referência a uma conta de armazenamento que não seja implantada neste modelo, mas existe no mesmo grupo de recursos como os recursos que está sendo implantado.

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }

Você pode recuperar um valor específico de objeto retornado, como o ponto de extremidade do blob URI, conforme mostrado no exemplo a seguir.

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

O exemplo a seguir faz referência a uma conta de armazenamento em um grupo de recurso diferente.

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

As propriedades do objeto retornado da função **referência** variam por tipo de recurso. Para ver os nomes de propriedade e valores de um tipo de recurso, crie um modelo simple que retorna o objeto da seção de **saídas** . Se você tiver um recurso existente desse tipo, o seu modelo retorna apenas o objeto sem implantar os novos recursos. Se você não tiver um recurso existente desse tipo, seu modelo implanta somente desse tipo e retorna o objeto. Em seguida, adicione essas propriedades para outros modelos que precisa recuperar dinamicamente os valores durante a implantação. 

<a id="resourcegroup" />
### <a name="resourcegroup"></a>resourceGroup

**resourceGroup()**

Retorna um objeto que representa o grupo de recursos atual. 

O objeto retornado é no seguinte formato:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

O exemplo a seguir usa o local do grupo de recursos para atribuir o local de um site.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### <a name="resourceid"></a>identificação de recurso

**identificação de recurso ([subscriptionId], [resourceGroupName], tipo de recurso, resourceName1, [resourceName2]...)**

Retorna o identificador exclusivo de um recurso. 
      
| Parâmetro         | Necessário | Descrição
| :---------------: | :------: | :----------
| subscriptionId    |   Não     | Valor padrão é a assinatura atual. Especifique esse valor quando você precisa recuperar um recurso em outra assinatura.
| resourceGroupName |   Não     | Valor padrão é atual grupo de recursos. Especifique esse valor quando você precisa recuperar um recurso em outro grupo de recursos.
| tipo de recurso      |   Sim    | Tipo de recurso incluindo namespace do provedor de recursos.
| resourceName1     |   Sim    | Nome do recurso.
| resourceName2     |   Não     | Próximo recurso nome segmento se recurso estiver aninhado.

Use esta função quando o nome do recurso é ambíguos ou não provisionado no mesmo modelo. O identificador é retornado no seguinte formato:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

O exemplo a seguir mostra como recuperar os ids de recursos para um site e um banco de dados. O site existe em um grupo de recursos denominado **myWebsitesGroup** e o banco de dados existe no grupo de recursos atual para este modelo.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
Muitas vezes, você precisa usar esta função quando usando uma conta de armazenamento ou a rede virtual em um grupo de recurso alternativo. A conta de armazenamento ou a rede virtual pode ser usada em vários grupos de recursos; Portanto, você não deseja excluí-las ao excluir um grupo de recursos único. O exemplo a seguir mostra como um recurso de um grupo de recursos externos pode ser facilmente usado:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### <a name="subscription"></a>assinatura

**Subscription()**

Retorna detalhes sobre a inscrição no seguinte formato.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

O exemplo a seguir mostra a função de assinatura chamada na seção saídas. 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## <a name="next-steps"></a>Próximas etapas
- Para obter uma descrição das seções em um modelo do Gerenciador de recursos do Azure, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md)
- Para mesclar vários modelos, consulte [usando modelos vinculados com o Gerenciador de recursos do Azure](resource-group-linked-templates.md)
- Para fazer a iteração um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md)
- Para ver como implantar o modelo que você criou, consulte [implantar um aplicativo com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md)

