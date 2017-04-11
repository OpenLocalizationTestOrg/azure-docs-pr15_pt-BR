<properties
    pageTitle="Adicionar o Microsoft Translator nos aplicativos de lógica | Microsoft Azure"
    description="Visão geral do conector Microsoft Translator com parâmetros de API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Começar a usar o conector Microsoft Translator
Conecte a Microsoft Translator para traduzir o texto, detectar um idioma e muito mais. Com o Microsoft Translator, você pode: 

- Construa seu fluxo de negócios com base nos dados que você obtenha do Microsoft Translator. 
- Use ações para traduzir o texto, detectar um idioma e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, quando um novo arquivo é criado no Dropbox, você pode traduzir o texto no arquivo para outro idioma usando o Microsoft Translator.

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações
Microsoft Translator inclui as seguintes ações. Não há nenhum disparador.

Disparadores | Ações
--- | ---
Nenhum | <ul><li>Detectar idioma</li><li>Texto em fala</li><li>Traduzir Texto</li><li>Obter idiomas</li><li>Obter idiomas de fala</li></ul>

Todos os conectores suportam de dados nos formatos JSON e XML.


## <a name="create-a-connection-to-microsoft-translator"></a>Criar uma conexão ao Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Referência de API REST swagger
Se aplica à versão: 1.0.

### <a name="detect-language"></a>Detectar idioma    
Detectar idioma de origem de dado um texto.  
```GET: /Detect```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia de caracteres|Sim|consulta|Nenhum |Texto cujo idioma será identificado|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="text-to-speech"></a>Texto em fala    
Converte um determinado texto em fala como um fluxo de áudio no formato de onda.  
```GET: /Speak```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia de caracteres|Sim|consulta|Nenhum |Texto a ser convertido|
|idioma|cadeia de caracteres|Sim|consulta|Nenhum |Código de idioma para gerar fala (exemplo: ' en-dos EUA)|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="translate-text"></a>Traduzir Texto    
Converte texto em um idioma especificado usando o Microsoft Translator.  
```GET: /Translate```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia de caracteres|Sim|consulta|Nenhum |Texto para traduzir|
|languageTo|cadeia de caracteres|Sim|consulta| Nenhum|Código de idioma de destino (exemplo: 'fr')|
|languageFrom|cadeia de caracteres|Não|consulta|Nenhum |Idioma de origem; Se não for fornecido, o Microsoft Translator tentará detecção automática. (exemplo: en)|
|categoria|cadeia de caracteres|Não|consulta|geral |Categoria de tradução (padrão: 'Geral')|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-languages"></a>Obter idiomas    
Recupera todos os idiomas que ofereça suporte Microsoft Translator.  
```GET: /TranslatableLanguages```

Não existem parâmetros para esta chamada. 

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-speech-languages"></a>Obter idiomas de fala    
Recupera os idiomas disponíveis para síntese de fala.  
```GET: /SpeakLanguages``` 

Não existem parâmetros para esta chamada.

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|

## <a name="object-definitions"></a>Definições de objeto

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Idioma: modelo de idioma para idiomas convertidos do Microsoft Translator

|Nome da propriedade | Tipo de dados | Necessário|
|---|---|---|
|Código|cadeia de caracteres|Não|
|Nome|cadeia de caracteres|Não|


## <a name="next-steps"></a>Próximas etapas

[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [lista APIs](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
