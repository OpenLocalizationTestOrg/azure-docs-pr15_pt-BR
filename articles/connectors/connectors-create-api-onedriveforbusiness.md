<properties
pageTitle="OneDrive for Business | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Conecte ao OneDrive for Business para gerenciar seus arquivos. Você pode executar várias ações como carregar, atualizar, obter e excluir arquivos."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-onedrive-for-business-connector"></a>Introdução ao OneDrive for Business connector

Conecte ao OneDrive for Business para gerenciar seus arquivos. Você pode executar várias ações como carregar, atualizar, obter e excluir arquivos.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O OneDrive for Business connector pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O OneDrive for Business connector tem as seguintes ações e/ou entrarão disponível:

### <a name="onedrive-for-business-actions"></a>OneDrive para ações de negócios
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|Recupera metadados de um arquivo no OneDrive for Business usando id|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|Atualiza um arquivo no OneDrive for Business|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|Exclui um arquivo do SkyDrive Pro|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|Recupera metadados de um arquivo no OneDrive for Business usando caminho|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|Recupera o conteúdo de um arquivo no OneDrive for Business usando caminho|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|Recupera o conteúdo de um arquivo no OneDrive for Business usando id|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|Carrega um arquivo no OneDrive for Business|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|Copia um arquivo para o OneDrive for Business|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|Arquivos de listas em uma OneDrive para pasta de negócios|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|Lista os arquivos no OneDrive para a pasta raiz de negócios|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|Extrai uma pasta onedrive for Business|
### <a name="onedrive-for-business-triggers"></a>Aciona do SkyDrive Pro
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um arquivo é criado|Dispara um fluxo quando um novo arquivo é criado em uma pasta OneDrive for Business|
|Quando um arquivo é modificado|Aciona um fluxo quando um arquivo é modificado em uma pasta OneDrive for Business|


## <a name="create-a-connection-to-onedrive-for-business"></a>Criar uma conexão para o OneDrive for Business
Para criar aplicativos de lógica com OneDrive for Business, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer OneDrive para credenciais de negócios|
Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo. 

>[AZURE.INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-onedrive-for-business"></a>Referência do SkyDrive Pro
Se aplica à versão: 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
Obter metadados de arquivo usando id: recupera metadados de um arquivo no OneDrive for Business usando id 

```GET: /datasets/default/files/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Especifique o arquivo|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="updatefile"></a>UpdateFile
Arquivo de atualização: atualiza um arquivo no OneDrive for Business 

```PUT: /datasets/default/files/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Especifique o arquivo para atualizar|
|corpo| |Sim|corpo|Nenhum|Conteúdo do arquivo para atualizar no OneDrive for Business|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="deletefile"></a>DeleteFile
Excluir arquivo: exclui um arquivo do SkyDrive Pro 

```DELETE: /datasets/default/files/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Especificar o arquivo para excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Obter metadados de arquivos usando o caminho: recupera metadados de um arquivo no OneDrive for Business usando caminho 

```GET: /datasets/default/GetFileByPath``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia de caracteres|Sim|consulta|Nenhum|Caminho exclusivo para o arquivo no OneDrive for Business|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="getfilecontentbypath"></a>GetFileContentByPath
Obter o conteúdo do arquivo usando caminho: recupera o conteúdo de um arquivo no OneDrive for Business usando caminho 

```GET: /datasets/default/GetFileContentByPath``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia de caracteres|Sim|consulta|Nenhum|Caminho exclusivo para o arquivo no OneDrive for Business|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="getfilecontent"></a>GetFileContent
Obter o conteúdo do arquivo usando id: recupera o conteúdo de um arquivo no OneDrive for Business usando id 

```GET: /datasets/default/files/{id}/content``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Especifique o arquivo|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="createfile"></a>CreateFile
Criar arquivo: carrega um arquivo no OneDrive for Business 

```POST: /datasets/default/files``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|cadeia de caracteres|Sim|consulta|Nenhum|Caminho da pasta para carregar o arquivo no OneDrive for Business|
|nome|cadeia de caracteres|Sim|consulta|Nenhum|Nome do arquivo para criar no OneDrive for Business|
|corpo| |Sim|corpo|Nenhum|Conteúdo do arquivo para carregar no SkyDrive Pro|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="copyfile"></a>CopyFile
Copiar arquivo: copia um arquivo para o OneDrive for Business 

```POST: /datasets/default/copyFile``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|cadeia de caracteres|Sim|consulta|Nenhum|URL para o arquivo de origem|
|destino|cadeia de caracteres|Sim|consulta|Nenhum|Caminho do arquivo de destino no OneDrive for Business, incluindo o nome de arquivo de destino|
|Substituir|booliano|Não|consulta|FALSO|Substitui o arquivo de destino se definida como 'true'|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="onnewfile"></a>OnNewFile
Quando um arquivo é criado: aciona um fluxo quando um novo arquivo é criado em uma pasta OneDrive for Business 

```GET: /datasets/default/triggers/onnewfile``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|cadeia de caracteres|Sim|consulta|Nenhum|Especificar uma pasta|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="onupdatedfile"></a>OnUpdatedFile
Quando um arquivo é modificado: aciona um fluxo quando um arquivo é modificado em uma pasta OneDrive for Business 

```GET: /datasets/default/triggers/onupdatedfile``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|cadeia de caracteres|Sim|consulta|Nenhum|Especificar uma pasta|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="listfolder"></a>ListFolder
Lista de arquivos na pasta: lista os arquivos em uma OneDrive para pasta de negócios 

```GET: /datasets/default/folders/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Especificar a pasta|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="listrootfolder"></a>ListRootFolder
Pasta raiz de lista: lista os arquivos no OneDrive para a pasta raiz de negócios 

```GET: /datasets/default/folders``` 

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="extractfolderv2"></a>ExtractFolderV2
Extrair pasta: extrai uma pasta onedrive for Business 

```POST: /datasets/default/extractFolderV2``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|cadeia de caracteres|Sim|consulta|Nenhum|Caminho para o arquivo morto|
|destino|cadeia de caracteres|Sim|consulta|Nenhum|Caminho do OneDrive for Business extrair o conteúdo de arquivamento|
|Substituir|booliano|Não|consulta|FALSO|Substitui os arquivos de destino se definida como 'true'|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|tabular|não definido|Não |
|blob|não definido|Não |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|fonte|cadeia de caracteres|Não |
|displayName|cadeia de caracteres|Não |
|urlEncoding|cadeia de caracteres|Não |
|tableDisplayName|cadeia de caracteres|Não |
|tablePluralName|cadeia de caracteres|Não |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|fonte|cadeia de caracteres|Não |
|displayName|cadeia de caracteres|Não |
|urlEncoding|cadeia de caracteres|Não |



### <a name="blobmetadata"></a>BlobMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|Nome|cadeia de caracteres|Não |
|DisplayName|cadeia de caracteres|Não |
|Caminho|cadeia de caracteres|Não |
|LastModified|cadeia de caracteres|Não |
|Tamanho|número inteiro|Não |
|Tipo de mídia|cadeia de caracteres|Não |
|IsFolder|booliano|Não |
|ETag|cadeia de caracteres|Não |
|FileLocator|cadeia de caracteres|Não |



### <a name="object"></a>Objeto


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)