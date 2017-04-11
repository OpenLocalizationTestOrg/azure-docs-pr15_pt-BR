<properties
pageTitle="Mapeamentos de campo em indexadores de pesquisa do Azure"
description="Configurar mapeamentos de campo de indexador de pesquisa do Azure para considerar as diferenças em nomes de campo e representações de dados"
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
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Mapeamentos de campo em indexadores de pesquisa do Azure

Ao usar indexadores de pesquisa do Azure, você pode ocasionalmente encontrar por conta própria situações onde seus dados de entrada bastante não corresponderem o esquema de seu índice de destino. Nesses casos, você pode usar **os mapeamentos de campo** para transformar os dados na forma desejada. 

Algumas situações em que os mapeamentos de campo são úteis:
 
- Sua fonte de dados tem um campo `_id`, mas Azure pesquisa não permitir nomes de campo que começam com um sublinhado. Um mapeamento de campo permite que você "Renomear" um campo. 
- Você deseja preencher vários campos no índice com os mesmos dados de origem dados, por exemplo, porque você deseja aplicar analyzers diferentes para esses campos. Mapeamentos de campo permitem que você "bifurcação" um campo de fonte de dados.
- Você precisa para Base64 codificar ou decodificar seus dados. Mapeamentos de campo suportam várias **funções de mapeamento**, incluindo funções para na Base 64 codificar e decodificar.   


> [AZURE.IMPORTANT] Atualmente, a funcionalidade de mapeamentos do campo é na visualização. Ele está disponível somente na API REST usando a versão **2015-02-28-visualização**. Verifique se lembrar, visualizar APIs destinam-se para testar e avaliação e não deve ser usados em ambientes de produção.

## <a name="setting-up-field-mappings"></a>Configurar mapeamentos de campo

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a API do [Indexador criar](search-api-indexers-2015-02-28-preview.md#create-indexer) . Você pode gerenciar mapeamentos de campo em um indexador indexação usando a API do [Indexador de atualização](search-api-indexers-2015-02-28-preview.md#update-indexer) . 

Um mapeamento de campo consiste em 3 partes: 

1. A `sourceFieldName`, que representa um campo em sua fonte de dados. Essa propriedade é necessária. 

2. Um recurso opcional `targetFieldName`, que representa um campo no índice de pesquisa. Se omitido, será usado o mesmo nome de fonte de dados. 

3. Um recurso opcional `mappingFunction`, que pode transformar dados usando uma das várias funções predefinidas. A lista completa das funções está [abaixo](#mappingFunctions).

Mapeamentos de campos são adicionados à `fieldMappings` matriz na definição de indexador. 

Por exemplo, aqui está como você pode acomodar diferenças em nomes de campo: 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Um indexador pode ter vários mapeamentos de campo. Por exemplo, veja como você pode "bifurcação" um campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Pesquisa Azure usa a comparação de maiusculas e minúsculas para resolver os nomes de campo e função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiusculas e minúsculas direita), mas isso significa que sua fonte de dados ou o índice não pode ter campos que diferem somente em maiusculas e minúsculas.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Essas funções são suportadas: 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Executa a codificação Base64 de *segurança de URL* de sequência de entrada. Pressupõe que a entrada é codificados UTF-8. 

#### <a name="sample-use-case"></a>Caso de uso de amostra 

Apenas os caracteres de URL confiável podem aparecer em uma chave de documento do Azure pesquisa (porque clientes devem ser capazes de atender o documento usando a API de pesquisa, por exemplo). Se seus dados contiverem caracteres não seguros URL e deseja usá-lo para preencher um campo de chave no índice de pesquisa, use esta função.   

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Executa na Base 64 decodificar da série de entrada. A entrada será considerada um *URL confiável* cadeia de caracteres codificada na Base 64. 

#### <a name="sample-use-case"></a>Caso de uso de amostra 

Valores de metadados personalizados de blob devem ser codificada como ASCII. Você pode usar a codificação na Base 64 para representar aleatório cadeias de caracteres Unicode em metadados personalizados do blob. No entanto, para fazer pesquisa significativo, você pode usar esta função para transformar dados codificados de volta em cadeias de caracteres "normais" quando você preencher o índice de pesquisa.  

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Por exemplo, se a entrada é `Jane Doe`, o `delimiter` é `" "`(espaço) e o `position` é 0, o resultado é `Jane`; Se o `position` é 1, o resultado será `Doe`. Se a posição se refere a um símbolo que não existe, um erro será retornado.

#### <a name="sample-use-case"></a>Caso de uso de amostra 

Sua fonte de dados contiver uma `PersonName` campo e você deseja indexá-la como duas separado `FirstName` e `LastName` campos. Você pode usar esta função para dividir a entrada usando o caractere de espaço como o delimitador.

#### <a name="parameters"></a>Parâmetros

- `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
- `position`: uma posição em zero inteiro do token escolher após a cadeia de caracteres de entrada é dividida.    

#### <a name="example"></a>Exemplo

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

As transformações de uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher uma `Collection(Edm.String)` campo no índice. 

Por exemplo, se a cadeia de entrada é `["red", "white", "blue"]`, em seguida, o campo de destino do tipo `Collection(Edm.String)` será preenchida com os três valores `red`, `white` e `blue`. Valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro será retornado. 

#### <a name="sample-use-case"></a>Caso de uso de amostra

Banco de dados do SQL Azure não tem um tipo de dados interno que naturalmente mapeia para `Collection(Edm.String)` campos na pesquisa do Azure. Para preencher os campos do conjunto de cadeia de caracteres, formatar seus dados de origem como uma matriz de cadeia de caracteres JSON e usar essa função. 

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar a pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para melhorias, por favor, comunique-se em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).