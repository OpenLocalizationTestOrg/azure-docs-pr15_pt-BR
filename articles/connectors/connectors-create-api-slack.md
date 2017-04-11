<properties
pageTitle=" Usar o conector de margem de atraso em seus aplicativos de lógica | Microsoft Azure"
description="Começar a usar o conector de margem de atraso em seus aplicativos de lógica de serviço de aplicativo do Microsoft Azure"
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

# <a name="get-started-with-the-slack-connector"></a>Começar a usar o conector de margem de atraso

Margem de atraso é uma ferramenta de comunicação de equipe, que une todas as comunicações de equipe em um colocam, instantaneamente pesquisável e disponível onde quer que você vá.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.

Com o conector de margem de atraso, você pode:

* Usá-lo para criar aplicativos de lógica

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Vamos falar sobre disparadores e ações

O conector de margem de atraso pode ser usado como uma ação; Não há nenhum disparador. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector de margem de atraso tem as seguintes ações e/ou entrarão disponível:

### <a name="slack-actions"></a>Ações de margem de atraso
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|PostMessage|Poste uma mensagem em um canal específico.|
## <a name="create-a-connection-to-slack"></a>Criar uma conexão a margem de atraso
Para usar o conector de margem de atraso, você primeiro cria uma **conexão** e forneça os detalhes para essas propriedades: 

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais de margem de atraso|

Siga estas etapas para entrar em margem de atraso e concluir a configuração da margem de atraso **conexão** em seu aplicativo de lógica:

1. Selecione **Recorrência**
2. Selecione uma **frequência** e insira um **intervalo**
3. Selecione **Adicionar uma ação**  
![Configurar a margem de atraso][1]  
4. Insira a margem de atraso na caixa de pesquisa e espere a pesquisa retornar todas as entradas com margem de atraso no nome
5. Selecione **margem de atraso - mensagem Post**
6. Selecione **entrar em atraso**:  
![Configurar a margem de atraso][2]
7. Fornecer suas credenciais de margem de atraso para entrar no autorizar o aplicativo    
![Configurar a margem de atraso][3]  
8. Você será redirecionado para a página de login de sua organização. **Autorize** Margem de atraso para interagir com seu aplicativo de lógica:      
![Configurar a margem de atraso][5] 
9. Após concluir a autorização você será redirecionado para o seu aplicativo de lógica para concluí-la definindo a seção **atraso - obter todas as mensagens** . Adicione outros disparadores e ações que você precisa.  
![Configurar a margem de atraso][6]
10. Salve seu trabalho selecionando **Salvar** na barra de menus acima.


>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="slack-rest-api-reference"></a>Margem de atraso referência de API REST
#### <a name="this-documentation-is-for-version-10"></a>Esta documentação destina-se versão: 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Poste uma mensagem em um canal específico.
**```POST: /chat.postMessage```** 



| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|canal|cadeia de caracteres|Sim|consulta|Nenhum|Canal, grupo particular ou canal de mensagem Instantânea para enviar a mensagem. Pode ser um nome (ex: #general) ou um ID de codificado.|
|texto|cadeia de caracteres|Sim|consulta|Nenhum|Texto da mensagem para enviar. Para opções de formatação, consulte https://api.slack.com/docs/formatting.|
|nome de usuário|cadeia de caracteres|Não|consulta|Nenhum|Nome do bot|
|as_user|booliano|Não|consulta|Nenhum|Passar true para postar a mensagem como o usuário autenticado, em vez de como um bot|
|analisar|cadeia de caracteres|Não|consulta|Nenhum|Altere como as mensagens são tratadas. Para obter detalhes, consulte https://api.slack.com/docs/formatting.|
|link_names|número inteiro|Não|consulta|Nenhum|Localizar e vincular nomes de usuário e nomes de canal.|
|unfurl_links|booliano|Não|consulta|Nenhum|Passar true para habilitar unfurling de conteúdo principalmente baseado em texto.|
|unfurl_media|booliano|Não|consulta|Nenhum|Passe falso para desativar unfurling de conteúdo de mídia.|
|icon_url|cadeia de caracteres|Não|consulta|Nenhum|URL para uma imagem para usar como um ícone para esta mensagem|
|icon_emoji|cadeia de caracteres|Não|consulta|Nenhum|Emoji para usar como um ícone para esta mensagem|


### <a name="here-are-the-possible-responses"></a>Aqui estão as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|408|Tempo limite da solicitação|
|429|Muitas solicitações|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|503|Margem de atraso serviço indisponível|
|504|Tempo limite do gateway|
|padrão|Falha na operação.|
------



## <a name="object-definitions"></a>Definições de objeto: 

 **Mensagem**: mensagem do Yammer

Propriedades necessárias para mensagem:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|ID|número inteiro|
|content_excerpt|cadeia de caracteres|
|sender_id|número inteiro|
|replied_to_id|número inteiro|
|created_at|cadeia de caracteres|
|network_id|número inteiro|
|message_type|cadeia de caracteres|
|sender_type|cadeia de caracteres|
|URL|cadeia de caracteres|
|web_url|cadeia de caracteres|
|group_id|número inteiro|
|corpo|não definido|
|thread_id|número inteiro|
|direct_message|booliano|
|client_type|cadeia de caracteres|
|client_url|cadeia de caracteres|
|idioma|cadeia de caracteres|
|notified_user_ids|matriz|
|privacidade|cadeia de caracteres|
|liked_by|não definido|
|system_message|booliano|



 **PostOperationRequest**: representa uma solicitação de postagem de conector do Yammer postar no yammer

Propriedades necessárias para PostOperationRequest:

corpo

**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|corpo|cadeia de caracteres|
|group_id|número inteiro|
|replied_to_id|número inteiro|
|direct_to_id|número inteiro|
|transmissão|booliano|
|tópico1|cadeia de caracteres|
|tópico2|cadeia de caracteres|
|topic3|cadeia de caracteres|
|topic4|cadeia de caracteres|
|topic5|cadeia de caracteres|
|topic6|cadeia de caracteres|
|topic7|cadeia de caracteres|
|topic8|cadeia de caracteres|
|topic9|cadeia de caracteres|
|topic10|cadeia de caracteres|
|topic11|cadeia de caracteres|
|topic12|cadeia de caracteres|
|topic13|cadeia de caracteres|
|topic14|cadeia de caracteres|
|topic15|cadeia de caracteres|
|topic16|cadeia de caracteres|
|topic17|cadeia de caracteres|
|topic18|cadeia de caracteres|
|topic19|cadeia de caracteres|
|topic20|cadeia de caracteres|



 **MessageList**: lista de mensagens

Propriedades necessárias para MessageList:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|mensagens|matriz|



 **MessageBody**: corpo da mensagem

Propriedades necessárias para MessageBody:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|analisados|cadeia de caracteres|
|sem formatação|cadeia de caracteres|
|avançada|cadeia de caracteres|



 **LikedBy**: Curtiu por

Propriedades necessárias para LikedBy:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Contagem|número inteiro|
|nomes|matriz|



 **YammmerEntity**: Curtiu por

Propriedades necessárias para YammmerEntity:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|tipo|cadeia de caracteres|
|ID|número inteiro|
|full_name|cadeia de caracteres|


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Definições de objeto: 

 **WebResultModel**: resultados de pesquisa do Bing web

Propriedades necessárias para WebResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia de caracteres|
|Descrição|cadeia de caracteres|
|DisplayUrl|cadeia de caracteres|
|ID|cadeia de caracteres|
|FullUrl|cadeia de caracteres|



 **VideoResultModel**: resultados de pesquisa de vídeo do Bing

Propriedades necessárias para VideoResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia de caracteres|
|DisplayUrl|cadeia de caracteres|
|ID|cadeia de caracteres|
|MediaUrl|cadeia de caracteres|
|Tempo de execução|número inteiro|
|Miniatura|não definido|



 **ThumbnailModel**: propriedades de miniatura do elemento multimídia

Propriedades necessárias para ThumbnailModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|MediaUrl|cadeia de caracteres|
|ContentType|cadeia de caracteres|
|Largura|número inteiro|
|Altura|número inteiro|
|FileSize|número inteiro|



 **ImageResultModel**: resultados de pesquisa do Bing imagens

Propriedades necessárias para ImageResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia de caracteres|
|DisplayUrl|cadeia de caracteres|
|ID|cadeia de caracteres|
|MediaUrl|cadeia de caracteres|
|SourceUrl|cadeia de caracteres|
|Miniatura|não definido|



 **NewsResultModel**: resultados de pesquisa do Bing notícias

Propriedades necessárias para NewsResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia de caracteres|
|Descrição|cadeia de caracteres|
|DisplayUrl|cadeia de caracteres|
|ID|cadeia de caracteres|
|Fonte|cadeia de caracteres|
|Data|cadeia de caracteres|



 **SpellResultModel**: resultados de sugestões de ortografia do Bing

Propriedades necessárias para SpellResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|ID|cadeia de caracteres|
|Valor|cadeia de caracteres|



 **RelatedSearchResultModel**: Bing relacionados os resultados da pesquisa

Propriedades necessárias para RelatedSearchResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Título|cadeia de caracteres|
|ID|cadeia de caracteres|
|BingUrl|cadeia de caracteres|



 **CompositeSearchResultModel**: resultados de pesquisa composto do Bing

Propriedades necessárias para CompositeSearchResultModel:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|WebResultsTotal|número inteiro|
|ImageResultsTotal|número inteiro|
|VideoResultsTotal|número inteiro|
|NewsResultsTotal|número inteiro|
|SpellSuggestionsTotal|número inteiro|
|WebResults|matriz|
|ImageResults|matriz|
|VideoResults|matriz|
|NewsResults|matriz|
|SpellSuggestionResults|matriz|
|RelatedSearchResults|matriz|


## <a name="object-definitions"></a>Definições de objeto: 

 **PostOperationResponse**: representa resposta da operação de post de conector de margem de atraso para lançamento margem de atraso

Propriedades necessárias para PostOperationResponse:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|Okey|booliano|
|canal|cadeia de caracteres|
|TS|cadeia de caracteres|
|Mensagem|não definido|
|Erro|cadeia de caracteres|



 **MessageItem**: uma mensagem de canal.

Propriedades necessárias para MessageItem:


Nenhuma das propriedades são necessárias. 


**Todas as propriedades**: 


| Nome | Tipo de dados |
|---|---|
|texto|cadeia de caracteres|
|ID|cadeia de caracteres|
|usuário|cadeia de caracteres|
|criado|número inteiro|
|excluído is_user|booliano|


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
