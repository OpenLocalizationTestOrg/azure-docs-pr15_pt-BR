<properties
pageTitle="RSS | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. Conector RSS permite que os usuários publiquem e recuperar itens de feed. Ele também permite que os usuários disparar operações quando um novo item é publicado no feed."
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

# <a name="get-started-with-the-rss-connector"></a>Começar a usar o conector RSS
RSS é um formato de agregação de populares da web usado para publicar conteúdo atualizado com frequência – como entradas de blog e títulos de notícias.  Muitos editores conteúdos fornecem um RSS feed para permitir que usuários assiná-lo.  Use o conector RSS para recuperar feeds fluxos de informações e disparador quando novos itens forem publicados em um RSS feed.

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. 

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector RSS pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML. 

 O conector RSS tem as seguintes ações e/ou entrarão disponível:

### <a name="rss-actions"></a>Ações de RSS
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obtenha todos os RSS feeds itens.|
### <a name="rss-triggers"></a>Gatilhos de RSS
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um novo item de feed publicado|Aciona um fluxo de trabalho quando um novo feed é publicado|


## <a name="create-a-connection-to-rss"></a>Criar uma conexão RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-rss"></a>Referência do RSS
Se aplica à versão: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Quando um novo item de feed publicado: aciona um fluxo de trabalho quando um novo feed é publicado 

```GET: /OnNewFeed``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|feedUrl|cadeia de caracteres|Sim|consulta|Nenhum|Url do feed|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="listfeeditems"></a>ListFeedItems
Listar todos os RSS feeds itens.: obter todos os RSS feeds itens. 

```GET: /ListFeedItems``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|feedUrl|cadeia de caracteres|Sim|consulta|Nenhum|Url do feed|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|valor|matriz|Não |



### <a name="feeditem"></a>FeedItem


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Sim |
|título|cadeia de caracteres|Sim |
|conteúdo|cadeia de caracteres|Sim |
|links|matriz|Não |
|updatedOn|cadeia de caracteres|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)