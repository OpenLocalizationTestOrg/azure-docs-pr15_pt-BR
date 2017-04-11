<properties
    pageTitle="Adicionar o conector de usuários do Office 365 nos aplicativos de lógica | Microsoft Azure"
    description="Visão geral do conector de usuários do Office 365 com parâmetros de API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Começar a usar o conector de usuários do Office 365

Conectar-se a usuários do Office 365 para obter perfis, os usuários de pesquisa e muito mais. 

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.

Com usuários do Office 365, você pode:

- Construa seu fluxo de negócios com base nos dados que obter de usuários do Office 365. 
- Ações de uso que obter relatórios diretos, obtenha o perfil de usuário de um gerente e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, obter relatórios diretos de uma pessoa e, em seguida, fazer essas informações e atualizar um banco de dados do SQL Azure. 

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de usuários do Office 365 tem as seguintes ações disponíveis. Não há nenhum disparador.

| Disparadores | Ações|
| --- | --- |
|Nenhum | <ul><li>Obter manager</li><li>Obter meu perfil</li><li>Obtenha relatórios diretos</li><li>Obter o perfil de usuário</li><li>Procurar usuários</li></ul>|

Todos os conectores suportam de dados nos formatos JSON e XML. 


## <a name="create-a-connection-to-office-365-users"></a>Criar uma conexão para usuários do Office 365

Quando você adiciona esse conector seus aplicativos de lógica, você deve entrar em sua conta de usuário do Office 365 e permitir que aplicativos de lógica para se conectar à sua conta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Depois de criar a conexão, inserir as propriedades de usuários do Office 365, como a ID de usuário. A **referência de API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar este mesmo conexão de usuários do Office 365 em outros aplicativos de lógica.


## <a name="office-365-users-rest-api-reference"></a>Referência de API REST de usuários do Office 365
Se aplica à versão: 1.0.

### <a name="get-my-profile"></a>Obter meu perfil 
Recupera o perfil do usuário atual.  
```GET: /users/me``` 

Não existem parâmetros para esta chamada.

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


### <a name="get-user-profile"></a>Obter o perfil de usuário 
Recupera um perfil de usuário específico.  
```GET: /users/{userId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID de usuário|cadeia de caracteres|Sim|caminho|Nenhum|Id nome ou email principal do usuário|

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


### <a name="get-manager"></a>Obter manager 
Recupera o perfil de usuário para o gerente do usuário especificado.  
```GET: /users/{userId}/manager``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID de usuário|cadeia de caracteres|Sim|caminho|Nenhum|Id nome ou email principal do usuário|

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



### <a name="get-direct-reports"></a>Obtenha relatórios diretos 
Obtenha relatórios diretos.  
```GET: /users/{userId}/directReports``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID de usuário|cadeia de caracteres|Sim|caminho|Nenhum|Id nome ou email principal do usuário|

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



### <a name="search-for-users"></a>Procurar usuários 
Recupera Procurar resultados de perfis de usuário.  
```GET: /users``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|searchTerm|cadeia de caracteres|Não|consulta|Nenhum|Pesquisar a cadeia de caracteres (aplica-se a: Exibir nome, determinado nome, sobrenome, email, email apelido e usuário UPN)|

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



## <a name="object-definitions"></a>Definições de objeto

#### <a name="user-user-model-class"></a>Usuário: Classe de modelo de usuário

|Nome da propriedade | Tipo de dados |Necessário
|---|---|---|
|DisplayName|cadeia de caracteres|Não|
|GivenName|cadeia de caracteres|Não|
|Sobrenome|cadeia de caracteres|Não|
|Mail|cadeia de caracteres|Não|
|MailNickname|cadeia de caracteres|Não|
|TelephoneNumber|cadeia de caracteres|Não|
|AccountEnabled|booliano|Não|
|ID|cadeia de caracteres|Sim
|UserPrincipalName|cadeia de caracteres|Não|
|Departamento|cadeia de caracteres|Não|
|JobTitle|cadeia de caracteres|Não|
|mobilePhone|cadeia de caracteres|Não|


## <a name="next-steps"></a>Próximas etapas

[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [lista APIs](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
