<properties
pageTitle="GitHub | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. GitHub é um repositório de gito baseado na web serviço de hospedagem. Ele oferece todas as revisões distribuído controle e fonte código management (SCM) funcionalidades de gito, bem como adicionar seus próprios recursos."
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

# <a name="get-started-with-the-github-connector"></a>Introdução ao conector GitHub

GitHub é um repositório de gito baseado na web serviço de hospedagem. Ele oferece todas as revisões distribuído controle e fonte código management (SCM) funcionalidades de gito, bem como adicionar seus próprios recursos.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de GitHub pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de GitHub tem as seguintes ações e/ou entrarão disponível:

### <a name="github-actions"></a>Ações de GitHub
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Cria um problema|
### <a name="github-triggers"></a>GitHub disparadores
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um problema é aberto|Um problema é aberto|
|Quando um problema é fechado|Um problema é fechado|
|Quando um problema é atribuído|Um problema é atribuído|


## <a name="create-a-connection-to-github"></a>Criar uma conexão para GitHub
Para criar aplicativos de lógica com GitHub, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais GitHub|
Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-github"></a>Referência do GitHub
Se aplica à versão: 1.0

## <a name="createissue"></a>CreateIssue
Criar um problema: cria um problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|repositoryOwner|cadeia de caracteres|Sim|caminho|Nenhum|Proprietário do repositório|
|repositoryName|cadeia de caracteres|Sim|caminho|Nenhum|Nome do repositório|
|issueBasicDetails| |Sim|corpo|Nenhum|Detalhes da questão|

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


## <a name="issueopened"></a>IssueOpened
Quando um problema é aberto: um problema é aberto 

```GET: /trigger/issueOpened``` 

Não existem parâmetros para esta chamada
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


## <a name="issueclosed"></a>IssueClosed
Quando um problema é fechado: um problema é fechado 

```GET: /trigger/issueClosed``` 

Não existem parâmetros para esta chamada
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


## <a name="issueassigned"></a>IssueAssigned
Quando é atribuído um problema: um problema é atribuído 

```GET: /trigger/issueAssigned``` 

Não existem parâmetros para esta chamada
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|título|cadeia de caracteres|Sim |
|corpo|cadeia de caracteres|Sim |
|destinatário|cadeia de caracteres|Sim |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|título|cadeia de caracteres|Sim |
|corpo|cadeia de caracteres|Sim |
|destinatário|cadeia de caracteres|Sim |
|número|cadeia de caracteres|Não |
|estado|cadeia de caracteres|Não |
|created_at|cadeia de caracteres|Não |
|repository_url|cadeia de caracteres|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)