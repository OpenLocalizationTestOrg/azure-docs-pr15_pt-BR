<properties
pageTitle="Adicionar o conector do Yammer em seus aplicativos de lógica | Microsoft Azure"
description="Visão geral do conector do Yammer com parâmetros de API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-yammer-connector"></a>Começar a usar o conector do Yammer

Conecte ao Yammer a conversas de acesso em sua rede corporativa.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.

Com o Yammer, você pode:

- Construa seu fluxo de negócios com base nos dados que você obtenha do Yammer. 
- Usar disparadores para quando há uma nova mensagem em um grupo ou um feed o seguinte.
- Use ações para postar uma mensagem, obtenha todas as mensagens e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, quando uma nova mensagem for exibida, você pode enviar um email usando o Office 365.

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações
Yammer inclui os seguintes disparadores e ações. 

Disparadores | Ações
--- | ---
<ul><li>Quando há uma nova mensagem em um grupo</li><li>Quando há uma nova mensagem no meu seguinte feed</li></ul>| <ul><li>Obter todas as mensagens</li><li>Obtém mensagens em um grupo</li><li>Obtém que as mensagens da minha seguinte feed</li><li>Mensagem de postagem</li><li>Quando há uma nova mensagem em um grupo</li><li>Quando há uma nova mensagem no meu seguinte feed</li></ul>

Todos os conectores suportam de dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-yammer"></a>Criar uma conexão para o Yammer
Para usar o conector do Yammer, você primeiro cria uma **conexão** e forneça os detalhes para essas propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais do Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="yammer-rest-api-reference"></a>Referência de API REST do Yammer
Esta documentação destina-se versão: 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obter todas as mensagens públicas em rede do Yammer do usuário conectado
Corresponde a "Todos" conversas da interface de web do Yammer.  
```GET: /messages.json```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|older_then|número inteiro|Não|consulta|Nenhum|Retorna mensagens mais antigas do que a ID de mensagem especificada como uma cadeia de caracteres numérica. Isso é útil para mensagens de paginação. Por exemplo, se você está exibindo atualmente 20 mensagens e o mais antigo é número 2912, você poderá acrescentar "? older_than = 2912″ à sua solicitação para obter as mensagens de 20 antes aqueles que você está vendo.|
|newer_then|número inteiro|Não|consulta|Nenhum|Retorna mensagens mais recentes do que a ID de mensagem especificada como uma cadeia de caracteres numérica. Isso deve ser usado quando pesquisando para novas mensagens. Se você estiver vendo mensagens e a mensagem mais recente retornado é 3516, você pode fazer uma solicitação com o parâmetro "? newer_than = 3516″ para garantir que você não obtiver cópias duplicadas de mensagens já na sua página.|
|limite|número inteiro|Não|consulta|Nenhum|Retorne apenas o número especificado de mensagens.|
|página|número inteiro|Não|consulta|Nenhum|Acessar a página especificada. Se retornado dados forem maiores que o limite, você pode usar este campo para acessar páginas subsequentes|


### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|408|Tempo limite da solicitação|
|429|Muitas solicitações|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|503|Serviço indisponível do Yammer|
|504|Tempo limite do gateway|
|padrão|Falha na operação.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Postar uma mensagem para um grupo ou empresa todos Feed
Se ID do grupo for fornecido, mensagem será lançada no grupo especificado mais que ela será lançada no Feed todos da empresa.    
```POST: /messages.json``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|entrada| |Sim|corpo|Nenhum|Solicitação de mensagem de postagem|


### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|201|Criado|



## <a name="object-definitions"></a>Definições de objeto

#### <a name="message-yammer-message"></a>Mensagem: Mensagem do Yammer

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|ID|número inteiro|Não|
|content_excerpt|cadeia de caracteres|Não|
|sender_id|número inteiro|Não|
|replied_to_id|número inteiro|Não|
|created_at|cadeia de caracteres|Não|
|network_id|número inteiro|Não|
|message_type|cadeia de caracteres|Não|
|sender_type|cadeia de caracteres|Não|
|URL|cadeia de caracteres|Não|
|web_url|cadeia de caracteres|Não|
|group_id|número inteiro|Não|
|corpo|não definido|Não|
|thread_id|número inteiro|Não|
|direct_message|booliano|Não|
|client_type|cadeia de caracteres|Não|
|client_url|cadeia de caracteres|Não|
|idioma|cadeia de caracteres|Não|
|notified_user_ids|matriz|Não|
|privacidade|cadeia de caracteres|Não|
|liked_by|não definido|Não|
|system_message|booliano|Não|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Representa uma solicitação de postagem de conector do Yammer postar no yammer

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|corpo|cadeia de caracteres|Sim|
|group_id|número inteiro|Não|
|replied_to_id|número inteiro|Não|
|direct_to_id|número inteiro|Não|
|transmissão|booliano|Não|
|tópico1|cadeia de caracteres|Não|
|tópico2|cadeia de caracteres|Não|
|topic3|cadeia de caracteres|Não|
|topic4|cadeia de caracteres|Não|
|topic5|cadeia de caracteres|Não|
|topic6|cadeia de caracteres|Não|
|topic7|cadeia de caracteres|Não|
|topic8|cadeia de caracteres|Não|
|topic9|cadeia de caracteres|Não|
|topic10|cadeia de caracteres|Não|
|topic11|cadeia de caracteres|Não|
|topic12|cadeia de caracteres|Não|
|topic13|cadeia de caracteres|Não|
|topic14|cadeia de caracteres|Não|
|topic15|cadeia de caracteres|Não|
|topic16|cadeia de caracteres|Não|
|topic17|cadeia de caracteres|Não|
|topic18|cadeia de caracteres|Não|
|topic19|cadeia de caracteres|Não|
|topic20|cadeia de caracteres|Não|

#### <a name="messagelist-list-of-messages"></a>MessageList: Lista de mensagens

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|mensagens|matriz|Não|


#### <a name="messagebody-message-body"></a>MessageBody: Corpo da mensagem

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|analisados|cadeia de caracteres|Não|
|sem formatação|cadeia de caracteres|Não|
|avançada|cadeia de caracteres|Não|

#### <a name="likedby-liked-by"></a>LikedBy: Curtiu por

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|Contagem|número inteiro|Não|
|nomes|matriz|Não|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: Curtiu por

| Nome | Tipo de dados | Necessário |
|---|---| --- | 
|tipo|cadeia de caracteres|Não|
|ID|número inteiro|Não|
|full_name|cadeia de caracteres|Não|


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
