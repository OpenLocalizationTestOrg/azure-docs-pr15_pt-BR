<properties
pageTitle="SendGrid | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Provedor de Conexão SendGrid permite que você enviar emails e gerenciar listas de destinatários."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Introdução ao conector SendGrid

Provedor de Conexão SendGrid permite que você enviar emails e gerenciar listas de destinatários.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de SendGrid pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de SendGrid tem as seguintes ações disponíveis. Não há nenhum disparador.

### <a name="sendgrid-actions"></a>Ações de SendGrid
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[EnviarEmail](connectors-create-api-sendgrid.md#sendemail)|Envia um email usando SendGrid API (limitado para 10.000 destinatários)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Adicionar um destinatário individual a uma lista de destinatários|


## <a name="create-a-connection-to-sendgrid"></a>Criar uma conexão para SendGrid
Para criar aplicativos de lógica com SendGrid, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|ApiKey|Sim|Fornecer sua chave de Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo.

## <a name="reference-for-sendgrid"></a>Referência do SendGrid
Se aplica à versão: 1.0

## <a name="sendemail"></a>EnviarEmail
Enviar email: envia um email usando SendGrid API (limitado para 10.000 destinatários) 

```POST: /api/mail.send.json``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|solicitação| |Sim|corpo|Nenhum|Mensagem de email para enviar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|429|Número excessivo de solicitação|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Adicionar destinatários à lista: adicionar um destinatário individual a uma lista de destinatários 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|listId|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva da lista de destinatários|
|recipientId|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva do destinatário|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="emailrequest"></a>EmailRequest


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|De|cadeia de caracteres|Sim |
|fromName|cadeia de caracteres|Não |
|Para|cadeia de caracteres|Sim |
|para nome|cadeia de caracteres|Não |
|Assunto|cadeia de caracteres|Sim |
|corpo|cadeia de caracteres|Sim |
|ishtml|booliano|Não |
|Cc|cadeia de caracteres|Não |
|ccname|cadeia de caracteres|Não |
|Cco|cadeia de caracteres|Não |
|bccname|cadeia de caracteres|Não |
|ReplyTo|cadeia de caracteres|Não |
|Data|cadeia de caracteres|Não |
|cabeçalhos|cadeia de caracteres|Não |
|arquivos|matriz|Não |
|nomes de arquivo|matriz|Não |



### <a name="emailresponse"></a>EmailResponse


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Mensagem|cadeia de caracteres|Não |



### <a name="recipientlists"></a>RecipientLists


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|listas|matriz|Não |



### <a name="recipientlist"></a>RecipientList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|nome|cadeia de caracteres|Não |
|recipient_count|número inteiro|Não |



### <a name="recipients"></a>Destinatários


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|destinatários|matriz|Não |



### <a name="recipient"></a>Destinatário


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Email|cadeia de caracteres|Não |
|Sobrenome|cadeia de caracteres|Não |
|nome|cadeia de caracteres|Não |
|ID|cadeia de caracteres|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)