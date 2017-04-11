<properties
pageTitle="Project Online | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Projeto Online é uma solução flexível online para gerenciamento de portfólio de projetos (PPM) e o trabalho diário da Microsoft. Entregue através do Office 365, Project Online permite que as organizações começar rapidamente com recursos de gerenciamento de projeto poderosos para planejar, priorizar e gerenciar projetos e investimentos em portfólio — de praticamente qualquer lugar em praticamente qualquer dispositivo."
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

# <a name="get-started-with-the-projectonline-connector"></a>Começar a usar o conector do Project Online

Projeto Online é uma solução flexível online para gerenciamento de portfólio de projetos (PPM) e o trabalho diário da Microsoft. Entregue através do Office 365, Project Online permite que as organizações começar rapidamente com recursos de gerenciamento de projeto poderosos para planejar, priorizar e gerenciar projetos e investimentos em portfólio — de praticamente qualquer lugar em praticamente qualquer dispositivo.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector do Project Online pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector do Project Online tem as seguintes ações e/ou entrarão disponível:

### <a name="projectonline-actions"></a>Ações do Project Online
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Lista os projetos no seu site de projeto online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Cria um novo projeto no seu site de projeto online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Cria uma nova tarefa no projeto|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Cria um recursos da empresa no seu site de projeto online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Lista as tarefas publicadas em um projeto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Check-out de um projeto no seu site|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Fazer check-in e publicar e existente projeto no seu site|
### <a name="projectonline-triggers"></a>Disparadores do Project Online
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um novo projeto é criado|Aciona um fluxo sempre que um novo projeto é criado|
|Quando um novo recurso for criado|Dispara um novo fluxo quando um novo recurso é criado|
|Quando uma nova tarefa é criada|Aciona um fluxo quando uma nova tarefa é criada|


## <a name="create-a-connection-to-projectonline"></a>Criar uma conexão ao Project Online
Para criar aplicativos de lógica com o Project online, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais do Project Online|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-projectonline"></a>Referência do Project Online
Se aplica à versão: 1.0

## <a name="onnewproject"></a>OnNewProject
Quando um novo projeto é criado: aciona um fluxo sempre que um novo projeto é criado 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Quando um novo recurso for criado: dispara um novo fluxo quando um novo recurso é criado 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Quando uma nova tarefa é criada: aciona um fluxo quando uma nova tarefa é criada 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Listar projetos: lista os projetos no seu site de projeto online 

```GET: /_api/ProjectServer/Projects``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Cria um novo projeto: cria um novo projeto no seu site de projeto online 

```POST: /_api/ProjectServer/Projects``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJ| |Sim|corpo|Nenhum|Novo projeto para criar|

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


## <a name="createtask"></a>CreateTask
Cria nova tarefa: cria uma nova tarefa no projeto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia de caracteres|Sim|caminho|Nenhum|ID exclusiva do projeto para adicionar a tarefa|
|tarefa| |Sim|corpo|Nenhum|Nova tarefa para adicionar ao projeto|

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


## <a name="createresource"></a>CreateResource
Criar novo recurso: cria um recursos da empresa no seu site de projeto online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|recurso| |Sim|corpo|Nenhum|Novo recurso da empresa para adicionar ao projeto|

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


## <a name="listtasks"></a>ListTasks
Lista tarefas: lista as tarefas publicadas em um projeto 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia de caracteres|Sim|caminho|Nenhum|ID exclusiva do projeto para buscar tarefas|

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


## <a name="checkoutproject"></a>CheckoutProject
Check-out um projeto: check-out de um projeto no seu site 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia de caracteres|Sim|caminho|Nenhum|ID exclusiva do projeto para adicionar a tarefa|

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


## <a name="publishproject"></a>PublishProject
Fazer check-in e publicar projeto: Fazer Check-in e publicar e existente projeto no seu site 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|cadeia de caracteres|Sim|consulta|Nenhum|Raiz url de site do seu site de projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadeia de caracteres|Sim|caminho|Nenhum|ID exclusiva do projeto para fazer check-in|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="triggerproject"></a>TriggerProject


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ProjectStartDate|cadeia de caracteres|Não |
|ProjectFinishDate|cadeia de caracteres|Não |
|ProjectCreatedDate|cadeia de caracteres|Não |
|ProjectId|cadeia de caracteres|Não |
|ProjectModifiedDate|cadeia de caracteres|Não |
|ProjectType|número inteiro|Não |
|NomeDoProjeto|cadeia de caracteres|Não |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="triggerresource"></a>TriggerResource


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Identificação de recurso|cadeia de caracteres|Não |
|ResourceBaseCalendar|cadeia de caracteres|Não |
|ResourceBookingType|número inteiro|Não |
|ResourceCanLevel|booliano|Não |
|ResourceCostPerUse|número|Não |
|ResourceCreatedDate|cadeia de caracteres|Não |
|ResourceEarliestAvailableFrom|cadeia de caracteres|Não |
|ResourceEmail|cadeia de caracteres|Não |
|ResourceInitials|cadeia de caracteres|Não |
|ResourceIsActive|booliano|Não |
|ResourceIsGeneric|booliano|Não |
|ResourceLatestAvailableTo|cadeia de caracteres|Não |
|ResourceModifiedDate|cadeia de caracteres|Não |
|ResourceName|cadeia de caracteres|Não |
|ResourceStatsuName|cadeia de caracteres|Não |
|Tipo de recurso|número inteiro|Não |
|TypeDescription|cadeia de caracteres|Não |
|TypeName|cadeia de caracteres|Não |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="triggertask"></a>TriggerTask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ProjectId|cadeia de caracteres|Não |
|TaskId|cadeia de caracteres|Não |
|NomeDoProjeto|cadeia de caracteres|Não |
|Nome de tarefa|cadeia de caracteres|Não |
|TaskCreatedDate|cadeia de caracteres|Não |
|TaskModifieddate|cadeia de caracteres|Não |
|TaskStartDate|cadeia de caracteres|Não |
|TaskFinishDate|cadeia de caracteres|Não |
|TaskPriority|número inteiro|Não |
|TaskIsActive|booliano|Não |



### <a name="newproject"></a>NewProject


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Nome|cadeia de caracteres|Sim |
|Descrição|cadeia de caracteres|Não |
|Iniciar|cadeia de caracteres|Não |



### <a name="newreource"></a>NewReource


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Nome|cadeia de caracteres|Sim |
|IsBudget|booliano|Não |
|IsGeneric|booliano|Não |
|IsInactive|booliano|Não |



### <a name="project"></a>Projeto


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ApprovedStart|cadeia de caracteres|Não |
|ApprovedEnd|cadeia de caracteres|Não |
|CheckedOutDate|cadeia de caracteres|Não |
|CheckOutDescription|cadeia de caracteres|Não |
|CheckOutId|cadeia de caracteres|Não |
|CreatedDate|cadeia de caracteres|Não |
|ID|cadeia de caracteres|Não |
|IsCheckedOut|booliano|Não |
|LastPublishedDate|cadeia de caracteres|Não |
|LastSavedDate|cadeia de caracteres|Não |
|OptimizerDecision|número inteiro|Não |
|PlannerDecision|número inteiro|Não |
|ProjectType|número inteiro|Não |
|Nome|cadeia de caracteres|Não |
|WinprojVersion|cadeia de caracteres|Não |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="newtask"></a>NewTask


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|parâmetros|não definido|Sim |



### <a name="taskparameters"></a>TaskParameters


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Nome|cadeia de caracteres|Sim |
|Anotações|cadeia de caracteres|Não |
|Iniciar|cadeia de caracteres|Não |
|Duração|cadeia de caracteres|Não |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|CanLevel|booliano|Não |
|Código|cadeia de caracteres|Não |
|CostAccrual|número inteiro|Não |
|CostCenter|cadeia de caracteres|Não |
|Criado|cadeia de caracteres|Não |
|DefaultBookingType|número inteiro|Não |
|Email|cadeia de caracteres|Não |
|ExternalId|cadeia de caracteres|Não |
|Grupo|cadeia de caracteres|Não |
|HireDate|cadeia de caracteres|Não |
|ID|cadeia de caracteres|Não |
|Iniciais|cadeia de caracteres|Não |
|IsActive|booliano|Não |
|IsBudget|booliano|Não |
|IsCheckedOut|booliano|Não |
|IsGeneric|booliano|Não |
|IsTeam|booliano|Não |
|MaterialLabel|cadeia de caracteres|Não |
|Modificado|cadeia de caracteres|Não |
|Nome|cadeia de caracteres|Não |
|Fonética|cadeia de caracteres|Não |
|Tipo de recurso|número inteiro|Não |
|TerminationDate|cadeia de caracteres|Não |



### <a name="taskswrapper"></a>TasksWrapper


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="task"></a>Tarefa


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Criado|cadeia de caracteres|Não |
|Modificado|cadeia de caracteres|Não |
|Iniciar|cadeia de caracteres|Não |
|Término|cadeia de caracteres|Não |
|Nome|cadeia de caracteres|Não |
|ID|cadeia de caracteres|Não |
|Prioridade|número inteiro|Não |
|PercentComplete|número inteiro|Não |
|Anotações|cadeia de caracteres|Não |
|Contato|cadeia de caracteres|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)