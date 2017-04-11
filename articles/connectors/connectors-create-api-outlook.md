<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. Você pode executar várias ações, como enviar email, agendar reuniões, adicionar contatos, etc."
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

# <a name="get-started-with-the-outlookcom-connector"></a>Introdução ao conector Outlook.com

Conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. Você pode executar várias ações, como enviar email, agendar reuniões, adicionar contatos, etc.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de Outlook.com pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de Outlook.com tem os seguintes as ações e/ou entrarão disponível:

### <a name="outlookcom-actions"></a>Ações de Outlook.com
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera emails de uma pasta|
|[EnviarEmail](connectors-create-api-outlook.md#SendEmail)|Envia um email|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Exclui um email por id|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marcar um email como tendo sido lidas|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Respostas a um email|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Recupera o anexo de email por id|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Enviar um email com várias opções e aguarde o destinatário de responder novamente com uma das opções|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Enviar um email de aprovação e aguardar uma resposta do destinatário|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera os calendários|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera itens do calendário|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Cria um novo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera um item específico de um calendário|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Exclui um item de calendário|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Parcialmente atualiza um item de calendário|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera pastas de contatos|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera os contatos de uma pasta de contatos|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Cria um novo contato|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera um contato específico de uma pasta de contatos|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Exclui um contato|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Parcialmente atualiza um contato|
### <a name="outlookcom-triggers"></a>Gatilhos de Outlook.com
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Em evento iniciando em breve|Aciona um fluxo quando um evento de calendário futuras está iniciando|
|No novo email|Aciona um fluxo quando chega um novo email|
|Em novos itens|Acionado quando um novo item de calendário é criado|
|Em itens atualizados|Acionado quando um item de calendário é modificado|


## <a name="create-a-connection-to-outlookcom"></a>Criar uma conexão com o Outlook.com
Para criar aplicativos de lógica com Outlook.com, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais do Outlook.com|
Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.  

## <a name="reference-for-outlookcom"></a>Referência do Outlook.com
Se aplica à versão: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Em evento iniciando assim: aciona um fluxo quando um evento de calendário futuras está iniciando 

```GET: /Events/OnUpcomingEvents``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|consulta|Nenhum|Identificador exclusivo do calendário|
|lookAheadTimeInMinutes|número inteiro|Não|consulta|15|Tempo (em minutos) para procurar ahead eventos futuros|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|202|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="getemails"></a>GetEmails
Receber emails: recupera emails de uma pasta 

```GET: /Mail``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|cadeia de caracteres|Não|consulta|Caixa de entrada|Caminho da pasta para recuperar emails (padrão: 'Entrada')|
|Início|número inteiro|Não|consulta|10|Número de emails para recuperar (padrão: 10)|
|fetchOnlyUnread|booliano|Não|consulta|verdadeiro|Recuperar somente os emails não lidos?|
|includeAttachments|booliano|Não|consulta|FALSO|Se definido como true, anexos também será recuperado junto com o email|
|searchQuery|cadeia de caracteres|Não|consulta|Nenhum|Consulta de pesquisa para filtrar emails|
|Ignorar|número inteiro|Não|consulta|0|Número de emails para ignorar (padrão: 0)|
|skipToken|cadeia de caracteres|Não|consulta|Nenhum|Vá token para a nova página de busca|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="sendemail"></a>EnviarEmail
Enviar email: envia um email 

```POST: /Mail``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|emailMessage| |Sim|corpo|Nenhum|Email|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="deleteemail"></a>DeleteEmail
Excluir emails: exclui um email por id 

```DELETE: /Mail/{messageId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia de caracteres|Sim|caminho|Nenhum|ID do email para excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="markasread"></a>MarkAsRead
Marcar como lida: marcar um email como tendo sido lidas 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia de caracteres|Sim|caminho|Nenhum|ID do email a ser marcado como lido|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="replyto"></a>ReplyTo
Responder ao email: respostas a um email 

```POST: /Mail/ReplyTo/{messageId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia de caracteres|Sim|caminho|Nenhum|ID do email para responder a|
|comentário|cadeia de caracteres|Sim|consulta|Nenhum|Comentário de resposta|
|responder a todos|booliano|Não|consulta|FALSO|Responder a todos os destinatários|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="getattachment"></a>GetAttachment
Obter o anexo: recupera anexo de email por id 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|cadeia de caracteres|Sim|caminho|Nenhum|ID do email|
|attachmentId|cadeia de caracteres|Sim|caminho|Nenhum|ID do anexo para baixar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="onnewemail"></a>OnNewEmail
No novo email: aciona um fluxo quando chega um novo email 

```GET: /Mail/OnNewEmail``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|cadeia de caracteres|Não|consulta|Caixa de entrada|Pasta de email para recuperar (padrão: caixa de entrada)|
|Para|cadeia de caracteres|Não|consulta|Nenhum|Endereços de e-mail do destinatário|
|De|cadeia de caracteres|Não|consulta|Nenhum|Endereço do remetente|
|prioridade|cadeia de caracteres|Não|consulta|Normal|Prioridade do email (alta, Normal, baixa) (padrão: Normal)|
|fetchOnlyWithAttachment|booliano|Não|consulta|FALSO|Recuperar somente os emails com um anexo|
|includeAttachments|booliano|Não|consulta|FALSO|Incluir anexos|
|subjectFilter|cadeia de caracteres|Não|consulta|Nenhum|Cadeia de caracteres a ser procurada no assunto|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|202|Aceita|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Enviar email com opções: enviar um email com várias opções e aguarde o destinatário de responder novamente com uma das opções 

```POST: /mailwithoptions/$subscriptions``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Sim|corpo|Nenhum|Solicitação de assinatura de email de opções|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|201|Inscrição criada|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Enviar email de aprovação: enviar um email de aprovação e aguardar uma resposta do destinatário 

```POST: /approvalmail/$subscriptions``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Sim|corpo|Nenhum|Solicitação de assinatura de email de aprovação|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|201|Inscrição criada|
|400|BadRequest|
|401|Não autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="calendargettables"></a>CalendarGetTables
Obtenha calendários: recupera os calendários 

```GET: /datasets/calendars/tables``` 

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendargetitems"></a>CalendarGetItems
Obter eventos: recupera itens do calendário 

```GET: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo do calendário para recuperar|
|$filter|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de orderBy ODATA para especificar a ordem das entradas|
|$skip|número inteiro|Não|consulta|Nenhum|Número de entradas para ignorar (padrão = 0)|
|$top|número inteiro|Não|consulta|Nenhum|Número máximo de entradas para recuperar (padrão = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendarpostitem"></a>CalendarPostItem
Criar um evento: cria um novo evento 

```POST: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|item| |Sim|corpo|Nenhum|Item de calendário para criar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendargetitem"></a>CalendarGetItem
Obter evento: recupera um item específico de um calendário 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um item de calendário para recuperar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Excluir evento: exclui um item de calendário 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo do item de calendário para excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Evento de atualização: parcialmente atualiza um item de calendário 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo do item de calendário para atualizar|
|item| |Sim|corpo|Nenhum|Item de calendário para atualizar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Em novos itens: disparada quando um novo item de calendário é criado 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|$filter|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de orderBy ODATA para especificar a ordem das entradas|
|$skip|número inteiro|Não|consulta|Nenhum|Número de entradas para ignorar (padrão = 0)|
|$top|número inteiro|Não|consulta|Nenhum|Número máximo de entradas para recuperar (padrão = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Em atualizados itens: disparada quando um item de calendário é modificado 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um calendário|
|$filter|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de orderBy ODATA para especificar a ordem das entradas|
|$skip|número inteiro|Não|consulta|Nenhum|Número de entradas para ignorar (padrão = 0)|
|$top|número inteiro|Não|consulta|Nenhum|Número máximo de entradas para recuperar (padrão = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactgettables"></a>ContactGetTables
Obtenha as pastas de contatos: recupera pastas de contatos 

```GET: /datasets/contacts/tables``` 

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactgetitems"></a>ContactGetItems
Obter contatos: recupera os contatos de uma pasta de contatos 

```GET: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo da pasta de contatos para recuperar|
|$filter|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|cadeia de caracteres|Não|consulta|Nenhum|Uma consulta de orderBy ODATA para especificar a ordem das entradas|
|$skip|número inteiro|Não|consulta|Nenhum|Número de entradas para ignorar (padrão = 0)|
|$top|número inteiro|Não|consulta|Nenhum|Número máximo de entradas para recuperar (padrão = 256)|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactpostitem"></a>ContactPostItem
Criar contato: cria um novo contato 

```POST: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de uma pasta de contatos|
|item| |Sim|corpo|Nenhum|Criar os contatos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactgetitem"></a>ContactGetItem
Obtenha o contato: recupera um contato específico de uma pasta de contatos 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de um contato para recuperar|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Excluir contato: exclui um contato 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo do contato para excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="contactpatchitem"></a>ContactPatchItem
Atualizar contato: parcialmente atualiza um contato 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|Identificador exclusivo do contato para atualizar|
|item| |Sim|corpo|Nenhum|Atualizar item de contato|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [cadeia de caracteres, objeto]]


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="object"></a>Objeto


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Anexos|matriz|Não |
|De|cadeia de caracteres|Não |
|Cc|cadeia de caracteres|Não |
|Cco|cadeia de caracteres|Não |
|Assunto|cadeia de caracteres|Sim |
|Corpo|cadeia de caracteres|Sim |
|Prioridade|cadeia de caracteres|Não |
|IsHtml|booliano|Não |
|Para|cadeia de caracteres|Sim |



### <a name="sendattachment"></a>SendAttachment


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|@odata.type|cadeia de caracteres|Não |
|Nome|cadeia de caracteres|Sim |
|ContentBytes|cadeia de caracteres|Sim |



### <a name="receivemessage"></a>ReceiveMessage


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|Foi lido|booliano|Não |
|Tem anexo|booliano|Não |
|DateTimeReceived|cadeia de caracteres|Não |
|Anexos|matriz|Não |
|De|cadeia de caracteres|Não |
|Cc|cadeia de caracteres|Não |
|Cco|cadeia de caracteres|Não |
|Assunto|cadeia de caracteres|Sim |
|Corpo|cadeia de caracteres|Sim |
|Prioridade|cadeia de caracteres|Não |
|IsHtml|booliano|Não |
|Para|cadeia de caracteres|Sim |



### <a name="receiveattachment"></a>ReceiveAttachment


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Sim |
|ContentType|cadeia de caracteres|Sim |
|@odata.type|cadeia de caracteres|Não |
|Nome|cadeia de caracteres|Sim |
|ContentBytes|cadeia de caracteres|Sim |



### <a name="digestmessage"></a>DigestMessage


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Assunto|cadeia de caracteres|Sim |
|Corpo|cadeia de caracteres|Não |
|Prioridade|cadeia de caracteres|Não |
|Resumo|matriz|Sim |
|Anexos|matriz|Não |
|Para|cadeia de caracteres|Sim |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



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



### <a name="tablemetadata"></a>TableMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|nome|cadeia de caracteres|Não |
|título|cadeia de caracteres|Não |
|x-ms-permissão|cadeia de caracteres|Não |
|x-ms-recursos|não definido|Não |
|esquema|não definido|Não |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|sortRestrictions|não definido|Não |
|filterRestrictions|não definido|Não |
|filterFunctions|matriz|Não |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|classificável|booliano|Não |
|unsortableProperties|matriz|Não |
|ascendingOnlyProperties|matriz|Não |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|filtráveis|booliano|Não |
|nonFilterableProperties|matriz|Não |
|requiredProperties|matriz|Não |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|NotificationUrl|cadeia de caracteres|Não |
|Mensagem|não definido|Não |



### <a name="messagewithoptions"></a>MessageWithOptions


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Assunto|cadeia de caracteres|Sim |
|Opções|cadeia de caracteres|Sim |
|Corpo|cadeia de caracteres|Não |
|Prioridade|cadeia de caracteres|Não |
|Anexos|matriz|Não |
|Para|cadeia de caracteres|Sim |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|recurso|cadeia de caracteres|Não |
|notificationType|cadeia de caracteres|Não |
|notificationUrl|cadeia de caracteres|Não |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|NotificationUrl|cadeia de caracteres|Não |
|Mensagem|não definido|Não |



### <a name="approvalmessage"></a>ApprovalMessage


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Assunto|cadeia de caracteres|Sim |
|Opções|cadeia de caracteres|Sim |
|Corpo|cadeia de caracteres|Não |
|Prioridade|cadeia de caracteres|Não |
|Anexos|matriz|Não |
|Para|cadeia de caracteres|Sim |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|SelectedOption|cadeia de caracteres|Não |



### <a name="tableslist"></a>TablesList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="table"></a>Tabela


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Nome|cadeia de caracteres|Não |
|DisplayName|cadeia de caracteres|Não |



### <a name="item"></a>Item


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ItemInternalId|cadeia de caracteres|Não |



### <a name="calendaritemslist"></a>CalendarItemsList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="calendaritem"></a>CalendarItem


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ItemInternalId|cadeia de caracteres|Não |



### <a name="contactitemslist"></a>ContactItemsList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="contactitem"></a>ContactItem


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ItemInternalId|cadeia de caracteres|Não |



### <a name="datasetslist"></a>DataSetsList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="dataset"></a>Conjunto de dados


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Nome|cadeia de caracteres|Não |
|DisplayName|cadeia de caracteres|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)