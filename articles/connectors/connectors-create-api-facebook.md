<properties
    pageTitle="Adicionar o conector do Facebook em seus aplicativos de lógica | Microsoft Azure"
    description="Visão geral do conector do Facebook com parâmetros de API REST"
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

# <a name="get-started-with-the-facebook-connector"></a>Começar a usar o conector do Facebook
Conectar ao Facebook e postar em uma linha do tempo, obter uma página de feed e muito mais. 

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.


Com o Facebook, você pode:

- Construa seu fluxo de negócios com base nos dados que você obtenha do Facebook. 
- Use um disparador quando uma nova postagem for recebida.
- Ações de uso que postam sua linha do tempo, obtenha uma página de feed e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, quando há uma nova postagem na sua linha do tempo, você pode levar essa postagem e enviados por push para o feed do Twitter. 



Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações
O conector do Facebook inclui as seguintes disparadores e ações. 

| Disparadores | Ações|
| --- | --- |
| <ul><li>Quando há uma nova postagem no meu cronograma</li></ul> |<ul><li>Obter feed da minha linha do tempo</li><li>Postar para meu linha do tempo</li><li>Quando há uma nova postagem no meu cronograma</li><li>Obter alimentação de página</li><li>Obter o cronograma de usuário</li><li>Publique a página</li></ul>

Todos os conectores suportam de dados nos formatos JSON e XML.

## <a name="create-a-connection-to-facebook"></a>Criar uma conexão ao Facebook
Quando você adiciona esse conector seus aplicativos de lógica, você deve autorizar aplicativos lógica para se conectar à sua Facebook.

1. Entre em sua conta do Facebook
2. Selecione **Autorizar**e permitir que seus aplicativos de lógica para se conectar e usar o Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Você pode usar esta conexão Facebook mesmo em outros aplicativos de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de API REST swagger
Se aplica à versão: 1.0.

### <a name="get-feed-from-my-timeline"></a>Obter feed da minha linha do tempo
Obtém os feeds da linha do tempo do usuário conectado.  
```GET: /me/feed```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|campos|cadeia de caracteres|Não|consulta|Nenhum |Especifique os campos que você deseja que sejam retornados. Exemplo (id, nome, imagem).|
|limite|número inteiro|Não|consulta| Nenhum|Número máximo de postagens a serem recuperados|
|com|cadeia de caracteres|Não|consulta| Nenhum|Restringir a lista de postagens apenas com a localização anexada.|
|filtro|cadeia de caracteres|Não|consulta| Nenhum|Recupere somente as postagens que coincidir com um filtro de fluxo específico.|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### <a name="post-to-my-timeline"></a>Postar para meu linha do tempo
Poste uma mensagem de status à linha do tempo do usuário conectado.  
```POST: /me/feed```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|Postar|cadeia de caracteres |Sim|corpo|Nenhum |Nova mensagem a ser lançado|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>Quando há uma nova postagem no meu cronograma
Aciona um novo fluxo quando há uma nova postagem na linha do tempo do usuário conectado.  
```GET: /trigger/me/feed```

Não existem parâmetros. 

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### <a name="get-page-feed"></a>Obter alimentação de página
Obtenha postagens do feed de uma página especificada.  
```GET: /{pageId}/feed```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|pageId|cadeia de caracteres|Sim|caminho| Nenhum|ID da página da qual postagens precisam ser recuperados.|
|limite|número inteiro|Não|consulta| Nenhum|Número máximo de postagens a serem recuperados|
|include_hidden|booliano|Não|consulta|Nenhum |Se deseja ou não incluir quaisquer postagens que estavam ocultas pela página|
|campos|cadeia de caracteres|Não|consulta|Nenhum |Especifique os campos que você deseja que sejam retornados. Exemplo (id, nome, imagem).|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### <a name="get-user-timeline"></a>Obter o cronograma de usuário
Obtenha postagens do cronograma de um usuário.  
```GET: /{userId}/feed```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|ID de usuário|cadeia de caracteres|Sim|caminho|Nenhum |ID do usuário cuja linha do tempo tiver a serem recuperados.|
|limite|número inteiro|Não|consulta|Nenhum |Número máximo de postagens a serem recuperados|
|com|cadeia de caracteres|Não|consulta|Nenhum |Restringir a lista de postagens apenas com a localização anexada.|
|filtro|cadeia de caracteres|Não|consulta| Nenhum|Recupere somente as postagens que coincidir com um filtro de fluxo específico.|
|campos|cadeia de caracteres|Não|consulta| Nenhum|Especifique os campos que você deseja que sejam retornados. Exemplo (id, nome, imagem).|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### <a name="post-to-page"></a>Publique a página
Poste uma mensagem em uma Page do Facebook como o usuário conectado.  
```POST: /{pageId}/feed```

| Nome|Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|pageId|cadeia de caracteres|Sim|caminho|Nenhum |ID da página para postar.|
|Postar|muitos |Sim|corpo|Nenhum |Nova mensagem a ser lançado.|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto

#### <a name="getfeedresponse"></a>GetFeedResponse

|Nome da propriedade | Tipo de dados | Necessário|
|---|---|---|
|dados|matriz|Não|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|dados|matriz|Não|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: Uma única entrada em um perfil do feed
O perfil pode ser um usuário, página, aplicativo ou grupo. 

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|admin_creator|matriz|Não|
|legenda|cadeia de caracteres|Não|
|created_time|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|
|feed_targeting|não definido|Não|
|De|não definido|Não|
|ícone|cadeia de caracteres|Não|
|is_hidden|booliano|Não|
|is_published|booliano|Não|
|link|cadeia de caracteres|Não|
|Mensagem|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|object_id|cadeia de caracteres|Não|
|imagem|cadeia de caracteres|Não|
|Coloque|não definido|Não|
|privacidade|não definido|Não|
|Propriedades|matriz|Não|
|fonte|cadeia de caracteres|Não|
|status_type|cadeia de caracteres|Não|
|história|cadeia de caracteres|Não|
|-alvo|não definido|Não|
|Para|matriz|Não|
|tipo|cadeia de caracteres|Não|
|updated_time|cadeia de caracteres|Não|
|with_tags|não definido|Não|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: Uma única entrada em um perfil do feed
O perfil pode ser um usuário, página, aplicativo ou grupo.

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|created_time|cadeia de caracteres|Não|
|De|não definido|Não|
|Mensagem|cadeia de caracteres|Não|
|tipo|cadeia de caracteres|Não|

#### <a name="adminitem"></a>AdminItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|link|cadeia de caracteres|Não|

#### <a name="propertyitem"></a>PropertyItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|nome|cadeia de caracteres|Não|
|texto|cadeia de caracteres|Não|
|href|cadeia de caracteres|Não|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|Mensagem|cadeia de caracteres|Sim|
|link|cadeia de caracteres|Não|
|imagem|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|legenda|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|
|Coloque|cadeia de caracteres|Não|
|marcas|cadeia de caracteres|Não|
|privacidade|não definido|Não|
|object_attachment|cadeia de caracteres|Não|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|Mensagem|cadeia de caracteres|Sim|
|link|cadeia de caracteres|Não|
|imagem|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|legenda|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|
|ações|matriz|Não|
|Coloque|cadeia de caracteres|Não|
|marcas|cadeia de caracteres|Não|
|object_attachment|cadeia de caracteres|Não|
|-alvo|não definido|Não|
|feed_targeting|não definido|Não|
|publicado|booliano|Não|
|scheduled_publish_time|cadeia de caracteres|Não|
|backdated_time|cadeia de caracteres|Não|
|backdated_time_granularity|cadeia de caracteres|Não|
|child_attachments|matriz|Não|
|multi_share_end_card|booliano|Não|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|

#### <a name="profilecollection"></a>ProfileCollection

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|dados|matriz|Não|

#### <a name="useritem"></a>UserItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|Sobrenome|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|Gênero|cadeia de caracteres|Não|
|sobre|cadeia de caracteres|Não|

#### <a name="actionitem"></a>ActionItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|nome|cadeia de caracteres|Não|
|link|cadeia de caracteres|Não|

#### <a name="targetitem"></a>TargetItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|países/regiões|matriz|Não|
|localidades|matriz|Não|
|regiões|matriz|Não|
|cidades|matriz|Não|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: O objeto que controla o news feed de direcionamento para esta postagem
Qualquer pessoa nesses grupos é mais provável consulte esta postagem, outros são menos prováveis. Aplica-se às páginas somente.

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|países/regiões|matriz|Não|
|regiões|matriz|Não|
|cidades|matriz|Não|
|age_min|número inteiro|Não|
|age_max|número inteiro|Não|
|divisão de gênero|matriz|Não|
|relationship_statuses|matriz|Não|
|interested_in|matriz|Não|
|college_years|matriz|Não|
|interesses|matriz|Não|
|relevant_until|número inteiro|Não|
|education_statuses|matriz|Não|
|localidades|matriz|Não|

#### <a name="placeitem"></a>PlaceItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|overall_rating|número|Não|
|local|não definido|Não|

#### <a name="locationitem"></a>LocationItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|Cidade|cadeia de caracteres|Não|
|país/região|cadeia de caracteres|Não|
|Latitude|número|Não|
|located_in|cadeia de caracteres|Não|
|longitude|número|Não|
|nome|cadeia de caracteres|Não|
|região|cadeia de caracteres|Não|
|estado|cadeia de caracteres|Não|
|Rua|cadeia de caracteres|Não|
|ZIP|cadeia de caracteres|Não|

#### <a name="privacyitem"></a>PrivacyItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|Descrição|cadeia de caracteres|Não|
|valor|cadeia de caracteres|Sim|
|permitir|cadeia de caracteres|Não|
|Negar|cadeia de caracteres|Não|
|amigos|cadeia de caracteres|Não|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|link|cadeia de caracteres|Não|
|imagem|cadeia de caracteres|Não|
|image_hash|cadeia de caracteres|Não|
|nome|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|URL|cadeia de caracteres|Sim|
|legenda|cadeia de caracteres|Não|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Sim|
|post_id|cadeia de caracteres|Sim|

#### <a name="postvideorequest"></a>PostVideoRequest

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|videoData|cadeia de caracteres|Sim|
|Descrição|cadeia de caracteres|Sim|
|título|cadeia de caracteres|Sim|
|uploadedVideoName|cadeia de caracteres|Não|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|dados|não definido|Sim|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|URL|cadeia de caracteres|Sim|
|is_silhouette|booliano|Sim|
|altura|cadeia de caracteres|Não|
|Largura|cadeia de caracteres|Não|

#### <a name="geteventresponse"></a>GetEventResponse

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|dados|matriz|Sim|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Nome da propriedade | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Sim|
|nome|cadeia de caracteres|Sim|
|hora_de_início|cadeia de caracteres|Não|
|end_time|cadeia de caracteres|Não|
|fuso horário|cadeia de caracteres|Não|
|local|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|
|ticket_uri|cadeia de caracteres|Não|
|rsvp_status|cadeia de caracteres|Sim|


## <a name="next-steps"></a>Próximas etapas

[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
