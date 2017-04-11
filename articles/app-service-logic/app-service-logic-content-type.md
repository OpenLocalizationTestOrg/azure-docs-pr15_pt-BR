<properties
   pageTitle="Aplicativos de lógica conteúdos digite manipulação | Microsoft Azure"
   description="Compreender como os aplicativos de lógica lida com tipos de conteúdo em design e em tempo de execução"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Manipulação de tipo de conteúdo de aplicativos de lógica

Há vários tipos de conteúdo que pode fluir por meio de um aplicativo de lógica - incluindo JSON, XML, arquivos simples e dados binários.  Enquanto todos os tipos de conteúdo forem compatíveis, alguns nativamente são compreendidas pelo mecanismo de aplicativos de lógica e outras pessoas podem exigir projeção ou conversões conforme necessário.  O artigo a seguir descreve como o mecanismo lida com diferentes tipos de conteúdo e como eles podem ser tratados corretamente conforme necessário.

## <a name="content-type-header"></a>Cabeçalho de tipo de conteúdo

Para iniciar simples, vamos examinar os dois `Content-Types` que não exigem qualquer conversão ou projeção para usar dentro de um aplicativo de lógica - `application/json` e `text/plain`.

### <a name="applicationjson"></a>Aplicativo/json

O mecanismo de fluxo de trabalho depende do `Content-Type` chamadas de cabeçalho de HTTP para determinar a manipulação apropriada.  Qualquer solicitação com o tipo de conteúdo `application/json` serão armazenados e tratado como um objeto JSON.  Além disso, o conteúdo JSON pode ser analisado por padrão sem precisar de qualquer conversão.  Portanto uma solicitação que tem o cabeçalho de tipo de conteúdo `application/json ` semelhante a esta:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

poderia ser analisados em um fluxo de trabalho com uma expressão como `@body('myAction')['foo'][0]` para obter um valor (neste caso, `bar`).  Nenhuma conversão adicional é necessária.  Se você estiver trabalhando com dados que seja JSON, mas não tiverem um cabeçalho especificado, você pode convertê-la manualmente JSON usando o `@json()` função (por exemplo: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texto/sem formatação

Semelhante a `application/json`, mensagens HTTP recebidas com o `Content-Type` cabeçalho da `text/plain` serão armazenados em sua forma bruta.  Além disso, se incluído em um ações subsequentes sem qualquer conversão a solicitação sairá com um `Content-Type`: `text/plain` cabeçalho.  Por exemplo, se trabalhar com um arquivo simples que você pode receber o seguinte conteúdo HTTP:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Se na próxima ação enviado como o corpo da solicitação de outro (`@body('flatfile')`), a solicitação teria um `text/plain` cabeçalho de tipo de conteúdo.  Se você estiver trabalhando com dados que seja texto sem formatação, mas não tiverem um cabeçalho especificado, você pode convertê-la manualmente ao texto usando o `@string()` função (por exemplo: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplicativo/xml e Application/octet-stream e funções de conversor

O mecanismo de aplicativo lógica sempre preservará a `Content-Type` que foi recebido na solicitação HTTP ou resposta.  Isso significa se um conteúdo for recebido com `Content-Type` de `application/octet-stream`, incluindo que em uma ação subsequente com nenhuma projeção resultará em uma solicitação de saída com `Content-Type`: `application/octet-stream`.  Dessa maneira, o mecanismo podem guaruntee dados não serão perdidos como ela entrará em todo o fluxo de trabalho.  No entanto, o estado de ação (entradas e saídas) são armazenados em um objeto JSON flua em todo o fluxo de trabalho.  Isso significa para preservar alguns tipos de dados, o mecanismo converterá o conteúdo em uma cadeia de caracteres binária codificação base64 com metadados apropriada que preserve ambos `$content` e `$content-type` -qual será convertido automaticamente.  Você pode também manualmente converter entre tipos de conteúdo usando baseada em funções de conversor:

* `@json()`-converterá dados`application/json`
* `@xml()`-converterá dados`application/xml`
* `@binary()`-converterá dados`application/octet-stream`
* `@string()`-converterá dados`text/plain`
* `@base64()`-Converte o conteúdo em uma cadeia de caracteres na Base 64
* `@base64toString()`-Converte uma cadeia de caracteres codificada em base64 para`text/plain`
* `@base64toBinary()`-Converte uma cadeia de caracteres codificada em base64 para`application/octet-stream`
* `@encodeDataUri()`-codifica uma cadeia de caracteres como uma matriz de bytes dataUri
* `@decodeDataUri()`-decodifica uma dataUri em uma matriz de bytes

Por exemplo, se você recebeu uma solicitação HTTP com `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Eu poderia converter e usá-la posteriormente algo parecido com `@xml(triggerBody())`, ou dentro de uma função como `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Tipos de conteúdo outros

Outros tipos de conteúdo são suportados e funcionarão com um aplicativo de lógica, mas pode exigir a recuperação manualmente o corpo da mensagem por decodificar a `$content`.  Por exemplo, se eu estava disparo fora de um `application/x-www-url-formencoded` solicitação semelhante a seguir:

```
CustomerName=Frank&Address=123+Avenue
```

como isso um não texto sem formatação ou JSON, ele será armazenado na ação como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Onde `$content` é a carga codificada como uma cadeia de caracteres na Base 64 para preservar todos os dados.  Desde que atualmente não existe uma função nativa para dados de formulário, eu ainda pode usar esses dados dentro de um fluxo de trabalho acessando manualmente os dados com uma função como `@string(body('formdataAction'))`.  Se eu quisesse minha solicitação de saída também tenha o `application/x-www-url-formencoded` cabeçalho de tipo de conteúdo, basta ele poderia ser adicionado o corpo de ação sem qualquer projeção como `@body('formdataAction')`.  No entanto, isso só funcionará se corpo é o único parâmetro no `body` entrada.  Se você tentar fazer `@body('formdataAction')` dentro de um `application/json` solicitar que você receberá um erro de tempo de execução como ele enviará o corpo codificado.
