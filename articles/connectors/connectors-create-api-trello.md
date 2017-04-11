<properties
pageTitle="Trello | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Trello fornece perspectiva sobre todos os seus projetos, no trabalho e em casa.  É uma maneira fácil, gratuita, flexível e visual para gerenciar seus projetos e organizar nada.  Conectar-se a Trello para gerenciar seus quadros, listas e cartões"
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

# <a name="get-started-with-the-trello-connector"></a>Introdução ao conector Trello

Trello fornece perspectiva sobre todos os seus projetos, no trabalho e em casa.  É uma maneira fácil, gratuita, flexível e visual para gerenciar seus projetos e organizar nada.  Conecte a Trello para gerenciar seus quadros, listas e cartões.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de Trello pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de Trello tem os seguintes as ações e/ou entrarão disponível:

### <a name="trello-actions"></a>Ações de Trello
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Cartões de lista no quadro|
|[GetCard](connectors-create-api-trello.md#getcard)|Obter o cartão por id|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Cartão de atualização|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Excluir cartão|
|[CreateCard](connectors-create-api-trello.md#createcard)|Cria um novo cartão na sua conta trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Quadros de lista|
|[GetBoard](connectors-create-api-trello.md#getboard)|Obtém o quadro por Id|
|[ListLists](connectors-create-api-trello.md#listlists)|Listas de cartão no quadro de lista|
|[GetList](connectors-create-api-trello.md#getlist)|Obtém a lista por Id|
### <a name="trello-triggers"></a>Gatilhos de Trello
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um novo cartão é adicionado a um quadro|Aciona um fluxo quando um novo cartão é adicionado a um quadro|
|Quando um novo cartão é adicionado a uma lista|Aciona um fluxo quando um novo cartão é adicionado a uma lista|


## <a name="create-a-connection-to-trello"></a>Criar uma conexão para Trello
Para criar aplicativos de lógica com Trello, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais de Trello|
Depois de criar a conexão, você pode usá-lo para executar as ações e escutar os disparadores descritos neste artigo. 

>[AZURE.INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-trello"></a>Referência do Trello
Se aplica à versão: 1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
Quando um novo cartão é adicionado a um quadro: aciona um fluxo quando um novo cartão é adicionado a um quadro 

```GET: /trigger/boards/{board_id}/cards``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva do quadro para buscar cartões em|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="onnewcardinlist"></a>OnNewCardInList
Quando um novo cartão é adicionado a uma lista: aciona um fluxo quando um novo cartão é adicionado a uma lista 

```GET: /trigger/lists/{list_id}/cards``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|Id exclusiva do quadro para buscar cartões em|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva da lista para buscar cartões em|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="listcards"></a>ListCards
Cartões no quadro de lista: cartões no quadro de lista 

```GET: /boards/{board_id}/cards``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|caminho|Nenhum|ID do quadro para buscar todos os cartões|
|ações|cadeia de caracteres|Não|consulta|Nenhum|Lista as ações para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|anexos|booliano|Não|consulta|Nenhum|Mostrar anexos|
|attachment_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de anexo para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|adesivos|booliano|Não|consulta|Nenhum|Mostrar adesivos|
|membros|booliano|Não|consulta|Nenhum|Mostrar membros|
|memeber_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de membro para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|CheckItemStates|booliano|Não|consulta|Nenhum|Retornar os estados de cartão|
|Listas de verificação|cadeia de caracteres|Não|consulta|Nenhum|Mostrar listas de verificação|
|limite|número inteiro|Não|consulta|Nenhum|O número máximo de resultados para retornar, entre 1 e 1000|
|desde|cadeia de caracteres|Não|consulta|Nenhum|Buscar todos os cartões após essa data|
|antes de|cadeia de caracteres|Não|consulta|Nenhum|Buscar todos os cartões antes desta data|
|filtro|cadeia de caracteres|Não|consulta|Nenhum|Filtrar a resposta|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de cartão para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="getcard"></a>GetCard
Obter o cartão pela identificação: Get cartão por id 

```GET: /cards/{card_id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|ID do quadro para buscar cartões em|
|card_id|cadeia de caracteres|Sim|caminho|Nenhum|ID do cartão para buscar|
|ações|cadeia de caracteres|Não|consulta|Nenhum|Lista as ações para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|actions_entities|booliano|Não|consulta|Nenhum|Retornar entidades de ação|
|actions_display|booliano|Não|consulta|Nenhum|Ação de devolução exibe|
|actions_limit|número inteiro|Não|consulta|Nenhum|Número máximo de ações para retornar|
|action_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de ação para retornar para cada ação. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|action_memberCreator_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de criador de membro de ação para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|anexos|booliano|Não|consulta|Nenhum|Anexos de retorno|
|attachement_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de anexo para retornar para cada anexo. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|membros|booliano|Não|consulta|Nenhum|Retornar membros|
|member_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de membro para retornar para cada membro. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|membersVoted|booliano|Não|consulta|Nenhum|Retornar votou membros|
|memberVoted_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de membro votou para retornar para cada membro voted. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|checkItemStates|booliano|Não|consulta|Nenhum|Estados de cartão de retorno|
|checkItemState_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos de estado para retornar para cada um do estado de item de cartão. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|listas de verificação|cadeia de caracteres|Não|consulta|Nenhum|Listas de verificação de retorno|
|checklist_fields|cadeia de caracteres|Não|consulta|Nenhum|Lista de campos da lista de verificação para retornar para cada lista de verificação. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|quadro|booliano|Não|consulta|Nenhum|Retornar o quadro que o cartão pertence a|
|board_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de quadro para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|lista|booliano|Não|consulta|Nenhum|Retornar a lista que o cartão pertence a|
|list_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos da lista para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|adesivos|booliano|Não|consulta|Nenhum|Adesivos de retorno|
|sticker_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de adesivo para retornar para cada etiqueta. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de cartão para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="updatecard"></a>UpdateCard
Cartão de atualização: placa de atualização 

```PUT: /cards/{card_id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|ID do quadro para buscar cartões de|
|card_id|cadeia de caracteres|Sim|caminho|Nenhum|ID do cartão para atualizar|
|updateCard| |Sim|corpo|Nenhum|Valores de placa atualizado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="deletecard"></a>DeleteCard
Excluir cartão: excluir cartão 

```DELETE: /cards/{card_id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|ID do quadro para buscar cartões de|
|card_id|cadeia de caracteres|Sim|caminho|Nenhum|ID do cartão para excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="createcard"></a>CreateCard
Criar cartão: cria um novo cartão na sua conta trello 

```POST: /cards``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|Id exclusiva do quadro para criar cartões em|
|newCard| |Sim|corpo|Nenhum|Novo cartão para adicionar ao quadro de trello|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="listboards"></a>ListBoards
Lista de quadros: quadros de lista 

```GET: /member/me/boards``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|filtro|cadeia de caracteres|Não|consulta|Nenhum|Filtros de lista para aplicar aos resultados do quadro. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de quadro para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|ações|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de ação para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|actions_entities|booliano|Não|consulta|Nenhum|Retornar entidades de ação|
|actions_limit|número inteiro|Não|consulta|Nenhum|Número máximo de ações para retornar|
|actions_format|cadeia de caracteres|Não|consulta|Nenhum|Especifique o formato das ações para retornar|
|actions_since|cadeia de caracteres|Não|consulta|Nenhum|Ações de retorno após a data especificada|
|action_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos da ação para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|associações|cadeia de caracteres|Não|consulta|Nenhum|Especifica informações de associação para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|organização|booliano|Não|consulta|Nenhum|Especificar para retornar informações da organização|
|organization_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de organização para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|listas|cadeia de caracteres|Não|consulta|Nenhum|Especifique se deseja listas que pertencem o quadro de retorno|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="getboard"></a>GetBoard
Obtém o quadro por Id: obtém o quadro por Id 

```GET: /boards/{board_id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva do quadro para obter|
|ações|cadeia de caracteres|Não|consulta|Nenhum|Lista as ações para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|action_entities|booliano|Não|consulta|Nenhum|Especifique se deseja retornar entidades de ação|
|actions_display|booliano|Não|consulta|Nenhum|Especifique se deseja retornar a exibição de ações|
|actions_format|cadeia de caracteres|Não|consulta|Nenhum|Especifique o formato das ações para retornar|
|actions_since|cadeia de caracteres|Não|consulta|Nenhum|Retornar apenas as ações após essa data|
|actions_limit|número inteiro|Não|consulta|Nenhum|Número máximo de ações para retornar|
|action_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos para retornar com cada campo. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|action_memeber|booliano|Não|consulta|Nenhum|Especifique se deseja retornar membros de ação|
|action_member_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de membro para retornar com cada membro de ação. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|action_memberCreator|booliano|Não|consulta|Nenhum|Especifique se deseja retornar o criador de membro de ação|
|action_memberCreator_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de criador de membro de ação para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|cartões|cadeia de caracteres|Não|consulta|Nenhum|Especificar os cartões para retornar|
|card_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos para retornar para cada cartão. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|card_attachments|booliano|Sim|consulta|Nenhum|Especifique se deseja retornar anexos em placas|
|card_attachment_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de anexo para retornar para cada anexo. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|card_checklists|cadeia de caracteres|Não|consulta|Nenhum|Especificar as listas de verificação para retornar para cada cartão|
|card_stickers|booliano|Não|consulta|Nenhum|Especifique se deseja retornar adesivos de cartão|
|boardStarts|cadeia de caracteres|Não|consulta|Nenhum|Especificar as estrelas de quadro para retornar|
|rótulos|cadeia de caracteres|Não|consulta|Nenhum|Especifique os rótulos para retornar|
|label_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de rótulo para retornar para cada etiqueta. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|labels_limits|número inteiro|Não|consulta|Nenhum|Número máximo de etiquetas para retornar|
|listas|cadeia de caracteres|Não|consulta|Nenhum|Especifique as listas para retornar|
|list_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos da lista para retornar para cada lista. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|associações|cadeia de caracteres|Não|consulta|Nenhum|Liste os membros para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|memberships_member|booliano|Não|consulta|Nenhum|Especifique se deseja retornar membros de associação|
|memberships_member_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de membro para retornar para cada membro de associação. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|membros|cadeia de caracteres|Não|consulta|Nenhum|Listar os membros para retornar|
|member_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de membro para retornar para cada membro. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|membersInvited|cadeia de caracteres|Não|consulta|Nenhum|Especifique os membros convidados para retornar|
|membersInvited_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos para retornar para cada um. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|listas de verificação|cadeia de caracteres|Não|consulta|Nenhum|Especificar as listas de verificação para retornar|
|checklist_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos da lista de verificação para retornar para cada lista de verificação. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|organização|booliano|Não|consulta|Nenhum|Especifique se deseja retornar as informações da organização|
|organization_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de organização para retornar para cada organização. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|organization_memberships|cadeia de caracteres|Não|consulta|Nenhum|Liste os membros da organização para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|myPerfs|booliano|Não|consulta|Nenhum|Especifique se deseja retornar meu perfs|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="listlists"></a>ListLists
Listas de cartão no quadro de lista: listas de cartão no quadro de lista 

```GET: /boards/{board_id}/lists``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva do quadro para buscar de listas|
|cartões|cadeia de caracteres|Não|consulta|Nenhum|Especificar os cartões para retornar|
|card_fields|cadeia de caracteres|Não|consulta|Nenhum|Os campos de cartão para retornar a partir da lista. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|filtro|cadeia de caracteres|Não|consulta|Nenhum|Especificar a propriedade de filtro para listas|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="getlist"></a>GetList
Lista de obtém por Id: lista de obtém por Id 

```GET: /lists/{list_id}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|board_id|cadeia de caracteres|Sim|consulta|Nenhum|Id exclusiva do quadro para buscar as listas de|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|Id exclusiva da lista para buscar|
|cartões|cadeia de caracteres|Não|consulta|Nenhum|Especificar os cartões para retornar|
|card_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de cartão para retornar para cada cartão. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|quadro|booliano|Não|consulta|Nenhum|Especifique se deseja retornar informações de quadro|
|board_fields|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos de quadro para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|
|campos|cadeia de caracteres|Não|consulta|Nenhum|Liste os campos da lista para retornar. Especificar 'todos' ou uma lista de separada por vírgulas de valores válidos|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="card"></a>Cartão


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|checkItemStates|matriz|Não |
|fechado|booliano|Não |
|dateLastActivity|cadeia de caracteres|Não |
|desc|cadeia de caracteres|Não |
|idBoard|cadeia de caracteres|Não |
|idList|cadeia de caracteres|Não |
|idMembersVoted|matriz|Não |
|idShort|número inteiro|Não |
|idAttachmentCover|cadeia de caracteres|Não |
|manualCoverAttachment|booliano|Não |
|idLabels|matriz|Não |
|nome|cadeia de caracteres|Não |
|POS|número inteiro|Não |
|shortLink|cadeia de caracteres|Não |
|identificações de|não definido|Não |
|conclusão|cadeia de caracteres|Não |
|Email|cadeia de caracteres|Não |
|shortUrl|cadeia de caracteres|Não |
|inscrito|booliano|Não |
|URL|cadeia de caracteres|Não |



### <a name="badges"></a>Identificações de


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Votos|número inteiro|Não |
|ViewingMemberVoted|booliano|Não |
|Inscrito|booliano|Não |
|FogBugz|cadeia de caracteres|Não |
|CheckItems|número inteiro|Não |
|CheckItemsChecked|número inteiro|Não |
|Comentários|número inteiro|Não |
|Anexos|número inteiro|Não |
|Descrição|booliano|Não |
|Conclusão|cadeia de caracteres|Não |



### <a name="object"></a>Objeto


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|



### <a name="createcard"></a>CreateCard


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|idList|cadeia de caracteres|Sim |
|nome|cadeia de caracteres|Sim |
|desc|cadeia de caracteres|Não |
|POS|cadeia de caracteres|Não |
|idMembers|cadeia de caracteres|Não |
|idLabels|cadeia de caracteres|Não |
|urlSource|cadeia de caracteres|Não |
|fonte de arquivo|cadeia de caracteres|Não |
|idCardSource|cadeia de caracteres|Não |
|keepFromSource|cadeia de caracteres|Não |



### <a name="updatecard"></a>UpdateCard


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|nome|cadeia de caracteres|Não |
|desc|cadeia de caracteres|Não |
|fechado|booliano|Não |
|idMembers|cadeia de caracteres|Não |
|idAttachmentCover|cadeia de caracteres|Não |
|idList|cadeia de caracteres|Não |
|idBoard|cadeia de caracteres|Não |
|POS|cadeia de caracteres|Não |
|conclusão|cadeia de caracteres|Não |
|inscrito|booliano|Não |



### <a name="board"></a>Quadro


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|fechado|booliano|Não |
|dateLastActivity|cadeia de caracteres|Não |
|dateLastView|cadeia de caracteres|Não |
|desc|cadeia de caracteres|Não |
|idOrganization|cadeia de caracteres|Não |
|convites|matriz|Não |
|convidadas|booliano|Não |
|labelNames|não definido|Não |
|associações|matriz|Não |
|nome|cadeia de caracteres|Não |
|tarefa identificada|booliano|Não |
|powerUps|matriz|Não |
|perfs|não definido|Não |
|shortLink|cadeia de caracteres|Não |
|shortUrl|cadeia de caracteres|Não |
|estrelada|cadeia de caracteres|Não |
|inscrito|cadeia de caracteres|Não |
|URL|cadeia de caracteres|Não |



### <a name="label"></a>Rótulo


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|verde|cadeia de caracteres|Não |
|amarelo|cadeia de caracteres|Não |
|laranja|cadeia de caracteres|Não |
|vermelho|cadeia de caracteres|Não |
|roxo|cadeia de caracteres|Não |
|azul|cadeia de caracteres|Não |
|Sky|cadeia de caracteres|Não |
|verde-limão|cadeia de caracteres|Não |
|rosa|cadeia de caracteres|Não |
|preto|cadeia de caracteres|Não |



### <a name="membership"></a>Associação


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|idMember|cadeia de caracteres|Não |
|memberType|cadeia de caracteres|Não |
|não confirmados|booliano|Não |



### <a name="perfs"></a>Perfs


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|permissionLevel|cadeia de caracteres|Não |
|votação|cadeia de caracteres|Não |
|comentários|cadeia de caracteres|Não |
|convites|cadeia de caracteres|Não |
|selfJoin|booliano|Não |
|cardCovers|booliano|Não |
|calendarFeedEnabled|booliano|Não |
|plano de fundo|cadeia de caracteres|Não |
|CorDoFundo|cadeia de caracteres|Não |
|backgroundImage|cadeia de caracteres|Não |
|backgroundImageScaled|cadeia de caracteres|Não |
|backgroundTile|booliano|Não |
|backgroundBrightness|cadeia de caracteres|Não |
|canBePublic|booliano|Não |
|canBeOrg|booliano|Não |
|canBePrivate|booliano|Não |
|canInvite|booliano|Não |



### <a name="list"></a>Lista


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|nome|cadeia de caracteres|Não |
|fechado|booliano|Não |
|idBoard|cadeia de caracteres|Não |
|POS|número|Não |
|inscrito|booliano|Não |
|cartões|matriz|Não |
|quadro|não definido|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)