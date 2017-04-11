<properties
    pageTitle="Adicionar o conector de caixa aos seus aplicativos de lógica | Microsoft Azure"
    description="Visão geral do conector caixa com parâmetros de API REST"
    services=""
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

# <a name="get-started-with-the-box-connector"></a>Começar a usar o conector de caixa
Conectar-se a caixa e crie arquivos, excluir arquivos e muito mais. 

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.

Com a caixa, você pode:

- Construa seu fluxo de negócios com base nos dados que você obter da caixa. 
- Use disparadores quando um arquivo é criado ou atualizado.
- Use ações que copiar um arquivo, excluir um arquivo e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, quando um arquivo é alterado em caixa, você pode levar esse arquivo e envie por email usando o Office 365.

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações
Caixa inclui as seguintes disparadores e ações.

| Disparadores | Ações|
| --- | --- |
|<ul><li>Quando um arquivo é criado</li><li>Quando um arquivo é modificado</li></ul> | <ul><li>Criar arquivo</li><li>Quando um arquivo é criado</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair arquivo morto para pasta</li><li>Obter o conteúdo do arquivo usando id</li><li>Obter o conteúdo do arquivo usando o caminho</li><li>Obter usando id de metadados de arquivo</li><li>Obter metadados de arquivos usando o caminho</li><li>Arquivo de atualização</li><li>Quando um arquivo é modificado</li></ul>

Todos os conectores suportam de dados nos formatos JSON e XML.

## <a name="create-a-connection-to-box"></a>Criar uma conexão a caixa
Quando você adiciona esse conector seus aplicativos de lógica, você deve autorizar aplicativos lógica para se conectar à sua caixa.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Depois de criar a conexão, inserir as propriedades de caixa. A **referência de API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar esta conexão caixa mesmo em outros aplicativos de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de API REST swagger
Se aplica à versão: 1.0.

### <a name="create-file"></a>Criar arquivo
Carrega um arquivo para a caixa.  
```POST: /datasets/default/files```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|cadeia de caracteres|Sim|consulta|Nenhum |Caminho da pasta para carregar o arquivo em caixa|
|nome|cadeia de caracteres|Sim|consulta|Nenhum |Nome do arquivo para criar na caixa|
|corpo|String(Binary) |Sim|corpo|Nenhum |Conteúdo do arquivo para carregar a caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="when-a-file-is-created"></a>Quando um arquivo é criado
Aciona um fluxo quando um novo arquivo é criado em uma pasta de caixa.  
```GET: /datasets/default/triggers/onnewfile```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|cadeia de caracteres|Sim|consulta|Nenhum |Identificador exclusivo da pasta na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="copy-file"></a>Copiar arquivo
Copia um arquivo à caixa.  
```POST: /datasets/default/copyFile```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|cadeia de caracteres|Sim|consulta|Nenhum |URL para o arquivo de origem|
|destino|cadeia de caracteres|Sim|consulta| Nenhum|Caminho do arquivo de destino na caixa, incluindo o nome de arquivo de destino|
|Substituir|booliano|Não|consulta| Nenhum|Substitui o arquivo de destino se definida como 'true'|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="delete-file"></a>Excluir arquivo
Exclui um arquivo da caixa.  
```DELETE: /datasets/default/files/{id}```


| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum |Identificador exclusivo do arquivo para excluir da caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="extract-archive-to-folder"></a>Extrair arquivo morto para pasta
Extrai um arquivo em uma pasta na caixa (exemplo:. zip).  
```POST: /datasets/default/extractFolderV2```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|cadeia de caracteres|Sim|consulta| |Caminho para o arquivo morto|
|destino|cadeia de caracteres|Sim|consulta| |Caminho na caixa para extrair o conteúdo de arquivamento|
|Substituir|booliano|Não|consulta| |Substitui os arquivos de destino se definida como 'true'|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-file-content-using-id"></a>Obter o conteúdo do arquivo usando id
Recupera o conteúdo do arquivo de caixa usando id.  
```GET: /datasets/default/files/{id}/content```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum |Identificador exclusivo do arquivo na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-file-content-using-path"></a>Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo de caixa usando o caminho.  
```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia de caracteres|Sim|consulta|Nenhum |Caminho exclusivo para o arquivo na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-file-metadata-using-id"></a>Obter usando id de metadados de arquivo
Recupera metadados do arquivo de caixa usando a id de arquivo.  
```GET: /datasets/default/files/{id}```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho| Nenhum|Identificador exclusivo do arquivo na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="get-file-metadata-using-path"></a>Obter metadados de arquivos usando o caminho
Recupera metadados do arquivo de caixa usando o caminho.  
```GET: /datasets/default/GetFileByPath```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia de caracteres|Sim|consulta|Nenhum |Caminho exclusivo para o arquivo na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="update-file"></a>Arquivo de atualização
Atualiza um arquivo na caixa.  
```PUT: /datasets/default/files/{id}```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho| Nenhum|Identificador exclusivo do arquivo para atualizar na caixa|
|corpo|String(Binary) |Sim|corpo|Nenhum |Conteúdo do arquivo para atualizar na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


### <a name="when-a-file-is-modified"></a>Quando um arquivo é modificado
Aciona um fluxo quando um arquivo é modificado em uma pasta de caixa.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|cadeia de caracteres|Sim|consulta|Nenhum |Identificador exclusivo da pasta na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Nome da propriedade | Tipo de dados | Necessário|
|---|---|---|
|tabular|não definido|Não|
|blob|não definido|Não|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|fonte|cadeia de caracteres|Não|
|displayName|cadeia de caracteres|Não|
|urlEncoding|cadeia de caracteres|Não|
|tableDisplayName|cadeia de caracteres|Não|
|tablePluralName|cadeia de caracteres|Não|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|fonte|cadeia de caracteres|Não|
|displayName|cadeia de caracteres|Não|
|urlEncoding|cadeia de caracteres|Não|

#### <a name="blobmetadata"></a>BlobMetadata

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|Nome|cadeia de caracteres|Não|
|DisplayName|cadeia de caracteres|Não|
|Caminho|cadeia de caracteres|Não|
|LastModified|cadeia de caracteres|Não|
|Tamanho|número inteiro|Não|
|Tipo de mídia|cadeia de caracteres|Não|
|IsFolder|booliano|Não|
|ETag|cadeia de caracteres|Não|
|FileLocator|cadeia de caracteres|Não|

## <a name="next-steps"></a>Próximas etapas

[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
