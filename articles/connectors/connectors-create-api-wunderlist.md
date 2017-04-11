<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Wunderlist fornecer um gerente de lista e tarefas de todo para ajudar as pessoas a realizar suas tarefas.  Se você estiver compartilhando uma lista de compras com um familiares, trabalhando em um projeto ou planejamento férias, Wunderlist torna mais fácil capturar, compartilhar e concluir sua to¬dos. Wunderlist instantaneamente sincroniza entre seu telefone, tablet e do computador, para poder acessar todas as tarefas de praticamente qualquer lugar."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Introdução ao conector Wunderlist

Wunderlist fornecer um gerente de lista e tarefas de todo para ajudar as pessoas a realizar suas tarefas.  Se você estiver compartilhando uma lista de compras com um familiares, trabalhando em um projeto ou planejamento férias, Wunderlist torna mais fácil capturar, compartilhar e concluir sua to¬dos. Wunderlist instantaneamente sincroniza entre seu telefone, tablet e do computador, para poder acessar todas as tarefas de praticamente qualquer lugar.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de Wunderlist pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de Wunderlist tem os seguintes as ações e/ou entrarão disponível:

### <a name="wunderlist-actions"></a>Ações de Wunderlist
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recupere as listas associadas a sua conta.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Crie uma lista.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recupere tarefas de uma lista específica.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Criar uma tarefa|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recupere subtarefas de uma lista específica ou de uma tarefa específica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Criar uma subtarefa dentro de uma tarefa específica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recuperar anotações para uma lista específica ou uma tarefa específica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Adicionar uma anotação a uma tarefa específica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recupere comentários de tarefa para uma lista específica ou uma tarefa específica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Adicionar um comentário a uma tarefa específica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recupere lembretes para uma lista específica ou uma tarefa específica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Defina um lembrete.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recupere arquivos para uma lista específica ou uma tarefa específica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera uma lista específica|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Exclui uma lista|
|[AtualizarLista](connectors-create-api-wunderlist.md#updatelist)|Atualizar uma lista específica|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera uma tarefa específica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Atualiza uma tarefa específica|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Exclui uma tarefa específica|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera uma subtarefa específico|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Atualiza uma subtarefa específico|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Exclui uma subtarefa específico|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recuperar uma anotação específica|
|[AtualizaçãoObservação](connectors-create-api-wunderlist.md#updatenote)|Atualizar uma anotação específica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Excluir uma anotação específica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recuperar um comentário de tarefa específica|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Atualizar um lembrete específico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Excluir um lembrete específico|
### <a name="wunderlist-triggers"></a>Gatilhos de Wunderlist
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando uma tarefa deva ser|Aciona um novo fluxo de quando uma tarefa na lista vence|
|Quando uma nova tarefa é criada|Aciona um novo fluxo de quando uma nova tarefa é criada na lista|
|Quando ocorre um lembrete|Aciona um novo fluxo quando ocorre um lembrete|


## <a name="create-a-connection-to-wunderlist"></a>Criar uma conexão para Wunderlist
Para criar aplicativos de lógica com Wunderlist, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais de Wunderlist|
Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-wunderlist"></a>Referência do Wunderlist
Se aplica à versão: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Quando uma tarefa deva ser: aciona um novo fluxo de quando uma tarefa na lista vence 

```GET: /trigger/tasksdue``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|


## <a name="triggertasknew"></a>TriggerTaskNew
Quando uma nova tarefa é criada: aciona um novo fluxo de quando uma nova tarefa é criada na lista 

```GET: /trigger/tasksnew``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|


## <a name="triggerreminder"></a>TriggerReminder
Quando ocorre um lembrete: aciona um novo fluxo quando ocorre um lembrete 

```GET: /trigger/reminders``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|


## <a name="retrievelists"></a>RetrieveLists
Obter listas: recuperar as listas associadas a sua conta. 

```GET: /lists``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createlist"></a>CreateList
Criar uma lista: criar uma lista. 

```POST: /lists``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Nova lista seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|padrão|Falha na operação.|


## <a name="listtasks"></a>ListTasks
Obter tarefas: recuperar tarefas de uma lista específica. 

```GET: /tasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|concluída|booliano|Não|consulta|Nenhum|Concluída|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createtask"></a>CreateTask
Criar uma tarefa: criar uma tarefa 

```POST: /tasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Nova tarefa a ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listsubtasks"></a>ListSubTasks
Obtenha as subtarefas: recuperar subtarefas de uma lista específica ou de uma tarefa específica. 

```GET: /subtasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|
|concluída|booliano|Não|consulta|Nenhum|Concluída|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createsubtask"></a>CreateSubTask
Criar uma subtarefa: criar uma subtarefa dentro de uma tarefa específica 

```POST: /subtasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Novo subtarefa seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listnotes"></a>ListNotes
Ter anotações: recuperar anotações para uma lista específica ou uma tarefa específica. 

```GET: /notes``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createnote"></a>CreateNote
Criar uma anotação: adicionar uma anotação a uma tarefa específica 

```POST: /notes``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Nova anotação a ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="listcomments"></a>ListComments
Obter comentários de tarefa: recuperar comentários de tarefa para uma lista específica ou uma tarefa específica. 

```GET: /task_comments``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createcomment"></a>CreateComment
Adicionar um comentário a uma tarefa: adicionar um comentário a uma tarefa específica 

```POST: /task_comments``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Novo comentário de tarefa a ser criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## <a name="retrievereminders"></a>RetrieveReminders
Receber lembretes: recuperar lembretes para uma lista específica ou uma tarefa específica. 

```GET: /reminders``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="createreminder"></a>CreateReminder
Definir um lembrete: definir um lembrete. 

```POST: /reminders``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar| |Sim|corpo|Nenhum|Novo lembrete seja criado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|padrão|Falha na operação.|


## <a name="retrievefiles"></a>RetrieveFiles
Obter os arquivos: recuperar arquivos para uma lista específica ou uma tarefa específica. 

```GET: /files``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|TASK_ID|número inteiro|Não|consulta|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|Solicitação inválida|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="getlist"></a>GetList
Obter lista: recupera uma lista específica 

```GET: /lists/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|ID da lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="deletelist"></a>DeleteList
Excluir lista: exclui uma lista 

```DELETE: /lists/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da lista|
|revisão|número inteiro|Sim|consulta|Nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Nenhum conteúdo|


## <a name="updatelist"></a>AtualizarLista
Atualizar uma lista: atualizar uma lista específica 

```PATCH: /lists/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da lista|
|Postar| |Sim|corpo|Nenhum|Detalhes da lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="gettask"></a>GetTask
Obter tarefa: recupera uma tarefa específica 

```GET: /tasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|ID|número inteiro|Sim|caminho|Nenhum|ID da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="updatetask"></a>UpdateTask
Atualizar uma tarefa: atualiza uma tarefa específica 

```PATCH: /tasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|ID|número inteiro|Sim|caminho|Nenhum|ID da tarefa|
|Postar| |Sim|corpo|Nenhum|Detalhes da tarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="deletetask"></a>DeleteTask
Excluir tarefa: exclui uma tarefa específica 

```DELETE: /tasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|número inteiro|Sim|consulta|Nenhum|ID da lista|
|ID|número inteiro|Sim|caminho|Nenhum|ID da tarefa|
|revisão|número inteiro|Sim|consulta|Nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Nenhum conteúdo|


## <a name="getsubtask"></a>GetSubTask
Obter subtarefa: recupera uma subtarefa específico 

```GET: /subtasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|ID da subtarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="updatesubtask"></a>UpdateSubTask
Atualizar uma subtarefa: atualiza uma subtarefa específico 

```PATCH: /subtasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da subtarefa|
|Postar| |Sim|corpo|Nenhum|Detalhes da subtarefa|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="deletesubtask"></a>DeleteSubTask
Excluir uma subtarefa: exclui uma subtarefa específico 

```DELETE: /subtasks/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da subtarefa|
|revisão|número inteiro|Sim|consulta|Nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Nenhum conteúdo|


## <a name="getnote"></a>GetNote
Obtenha uma anotação: recuperar uma anotação específica 

```GET: /notes/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|ID da nota|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="updatenote"></a>AtualizaçãoObservação
Atualizar uma observação: atualizar uma anotação específica 

```PATCH: /notes/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da nota|
|Postar| |Sim|corpo|Nenhum|Detalhes de anotação|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="deletenote"></a>DeleteNote
Excluir uma nota: excluir uma anotação específica 

```DELETE: /notes/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID da nota|
|revisão|número inteiro|Sim|consulta|Nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Nenhum conteúdo|


## <a name="getcomment"></a>GetComment
Obter o comentário de tarefa: recuperar um comentário de tarefa específica 

```GET: /task_comments/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia de caracteres|Sim|caminho|Nenhum|ID do comentário|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="updatereminder"></a>UpdateReminder
Atualizar um lembrete: atualizar um lembrete específico 

```PATCH: /reminders/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID de lembrete|
|Postar| |Sim|corpo|Nenhum|Detalhes de lembrete|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|


## <a name="deletereminder"></a>DeleteReminder
Excluir um lembrete: excluir um lembrete específico 

```DELETE: /reminders/{id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID|número inteiro|Sim|caminho|Nenhum|ID do lembrete.|
|revisão|número inteiro|Sim|consulta|Nenhum|Revisão|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|204|Nenhum conteúdo|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="list"></a>Lista


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|título|cadeia de caracteres|Não |
|list_type|cadeia de caracteres|Não |
|tipo|cadeia de caracteres|Não |
|revisão|número inteiro|Não |



### <a name="createdlist"></a>CreatedList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|título|cadeia de caracteres|Não |
|revisão|número inteiro|Não |
|tipo|cadeia de caracteres|Não |



### <a name="task"></a>Tarefa


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|assignee_id|número inteiro|Não |
|assigner_id|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|created_by_id|número inteiro|Não |
|data_vencimento|cadeia de caracteres|Não |
|list_id|número inteiro|Não |
|revisão|número inteiro|Não |
|estrelada|booliano|Não |
|título|cadeia de caracteres|Não |



### <a name="subtask"></a>Subtarefa


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|TASK_ID|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|created_by_id|número inteiro|Não |
|revisão|cadeia de caracteres|Não |
|título|cadeia de caracteres|Não |



### <a name="note"></a>Observação


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|TASK_ID|número inteiro|Não |
|conteúdo|cadeia de caracteres|Não |
|created_at|cadeia de caracteres|Não |
|updated_at|cadeia de caracteres|Não |
|revisão|número inteiro|Não |



### <a name="comment"></a>Comentário


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|TASK_ID|número inteiro|Não |
|revisão|número inteiro|Não |
|texto|cadeia de caracteres|Não |
|tipo|cadeia de caracteres|Não |
|created_at|cadeia de caracteres|Não |



### <a name="reminder"></a>Lembrete


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|Data|cadeia de caracteres|Não |
|TASK_ID|número inteiro|Não |
|revisão|número inteiro|Não |
|tipo|cadeia de caracteres|Não |
|created_at|cadeia de caracteres|Não |
|updated_at|cadeia de caracteres|Não |



### <a name="createdreminder"></a>CreatedReminder


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|Data|cadeia de caracteres|Não |
|TASK_ID|número inteiro|Não |
|revisão|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|updated_at|cadeia de caracteres|Não |



### <a name="file"></a>Arquivo


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|número inteiro|Não |
|URL|cadeia de caracteres|Não |
|TASK_ID|número inteiro|Não |
|list_id|número inteiro|Não |
|USER_ID|número inteiro|Não |
|file_name|cadeia de caracteres|Não |
|content_type|cadeia de caracteres|Não |
|file_size|número inteiro|Não |
|local_created_at|cadeia de caracteres|Não |
|created_at|cadeia de caracteres|Não |
|updated_at|cadeia de caracteres|Não |
|tipo|cadeia de caracteres|Não |
|revisão|número inteiro|Não |



### <a name="newtask"></a>NewTask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|número inteiro|Sim |
|título|cadeia de caracteres|Sim |
|assignee_id|número inteiro|Não |
|concluída|booliano|Não |
|recurrence_type|cadeia de caracteres|Não |
|recurrence_count|número inteiro|Não |
|data_vencimento|cadeia de caracteres|Não |
|estrelada|booliano|Não |



### <a name="newlist"></a>NewList


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|título|cadeia de caracteres|Sim |



### <a name="newsubtask"></a>NewSubtask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|título|cadeia de caracteres|Sim |
|concluída|booliano|Não |



### <a name="newnote"></a>NewNote


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|conteúdo|cadeia de caracteres|Sim |



### <a name="newcomment"></a>Novo_comentário


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|texto|cadeia de caracteres|Sim |



### <a name="newreminder"></a>NewReminder


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|número inteiro|Sim |
|TASK_ID|número inteiro|Sim |
|Data|cadeia de caracteres|Sim |



### <a name="updatetask"></a>UpdateTask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|revisão|número inteiro|Não |
|título|cadeia de caracteres|Não |
|assignee_id|número inteiro|Não |
|concluída|booliano|Não |
|recurrence_type|cadeia de caracteres|Não |
|recurrence_count|número inteiro|Não |
|data_vencimento|cadeia de caracteres|Não |
|estrelada|booliano|Não |



### <a name="updatelist"></a>AtualizarLista


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|revisão|número inteiro|Não |
|título|cadeia de caracteres|Não |



### <a name="updatesubtask"></a>UpdateSubtask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|revisão|número inteiro|Não |
|título|cadeia de caracteres|Não |
|concluída|booliano|Não |



### <a name="updatenote"></a>AtualizaçãoObservação


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|revisão|número inteiro|Não |
|conteúdo|cadeia de caracteres|Não |



### <a name="updatereminder"></a>UpdateReminder


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Data|cadeia de caracteres|Não |
|revisão|número inteiro|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)