<properties
pageTitle="Usar o conector de vídeo do Office 365 em seus aplicativos de lógica | Microsoft Azure"
description="Começar a usar o conector de vídeo do Office 365 em seus aplicativos de lógica de serviço de aplicativo do Microsoft Azure"
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

# <a name="get-started-with-the-office365-video-connector"></a>Começar a usar o conector de vídeo do Office 365
Conecte a vídeo do Office 365 para obter informações sobre o Office 365 vídeo, obter uma lista de vídeos e muito mais. O conector de vídeo do Office 365 pode ser usado com:

- Aplicativos de lógica 

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica. Esse conector não é suportado em qualquer versões anteriores do esquema.

Com o vídeo do Office 365, você pode:

- Construa seu fluxo de negócios com base nos dados que você obtenha do vídeo do Office 365. 
- Use ações que verificar o status de portal de vídeo, obter uma lista de todos os vídeo em um canal e muito mais. Essas ações obtém uma resposta e, em seguida, disponibilizar a saída para outras ações. Por exemplo, você pode usar o conector de pesquisa do Bing para procurar vídeos do Office 365 e depois use o conector de vídeo do Office 365 para obter informações sobre o que o vídeo. Se o vídeo atende às suas necessidades, você pode postar este vídeo no Facebook. 

Para adicionar uma operação lógica aplicativos, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de vídeo do Office 365 tem as seguintes ações disponíveis. Não há nenhum disparador.

| Disparadores | Ações|
| --- | --- |
| Nenhum | <ul><li>Verifica o status de portal de vídeo</li><li>Obter todos os canais visualizável</li><li>Obter a url de reprodução do manifesto serviços de mídia do Azure para um vídeo</li><li>Obter o token de portador para obter acesso a descriptografar o vídeo</li><li>Obtém informações sobre um determinado office365 vídeo</li><li>Lista todos os vídeos de office365 presentes em um canal</li></ul>

Todos os conectores suportam de dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Criar uma conexão ao conector de vídeo do Office 365
Quando você adiciona esse conector seus aplicativos de lógica, você deve entrar em sua conta de vídeo do Office 365 e permitir que aplicativos de lógica para se conectar à sua conta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Depois de criar a conexão, insira as propriedades de vídeo do Office 365, como o nome do locatário ou canal ID. A **referência de API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar este mesmo conexão de vídeo do Office 365 em outros aplicativos de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de API REST swagger
Se aplica à versão: 1.0.

### <a name="checks-video-portal-status"></a>Verifica o status de portal de vídeo 
Verifica o status de portal de vídeo para ver se os serviços de vídeo estão habilitados.  
```GET: /{tenant}/IsEnabled``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|



### <a name="get-all-viewable-channels"></a>Obter todos os canais visualizável 
Obtém todos os canais que o usuário tenha exibindo acessem.  
```GET: /{tenant}/Channels``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Lista todos os vídeos de office365 presentes em um canal 
Lista todos os vídeos de office365 presentes em um canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|
|channelId|cadeia de caracteres|Sim|caminho|Nenhum|A id de canal do qual vídeos precisam ser obtidos|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|




### <a name="gets-information-about-a-particular-office365-video"></a>Obtém informações sobre um determinado office365 vídeo 
Obtém informações sobre um determinado office365 vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|
|channelId|cadeia de caracteres|Sim|caminho|Nenhum|A id de canal|
|videoId|cadeia de caracteres|Sim|caminho|Nenhum|A id de vídeo|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obter a url de reprodução do manifesto serviços de mídia do Azure para um vídeo 
Obter a url de reprodução do manifesto serviços de mídia do Azure para um vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|
|channelId|cadeia de caracteres|Sim|caminho|Nenhum|A id de canal|
|videoId|cadeia de caracteres|Sim|caminho|Nenhum|A id de vídeo|
|streamingFormatType|cadeia de caracteres|Sim|consulta|Nenhum|Tipo de formato de streaming. 1 - suave Streaming ou MPEG-traço. 0 - HLS Streaming|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obter o token de portador para obter acesso a descriptografar o vídeo 
Obter o token de portador para obter acesso a descriptografar o vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|cadeia de caracteres|Sim|caminho|Nenhum|O nome do locatário para o diretório do usuário faz parte de|
|channelId|cadeia de caracteres|Sim|caminho|Nenhum|A id de canal|
|videoId|cadeia de caracteres|Sim|caminho|Nenhum|A id de vídeo|


#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Operação foi bem-sucedida|
|400|BadRequest|
|401|Não autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto

#### <a name="channel-channel-class"></a>Canal: Classe de canal

| Nome | Tipo de dados | Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|Título|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|


#### <a name="video"></a>Vídeo 

| Nome | Tipo de dados |Necessário|
|---|---|---|
|ID|cadeia de caracteres|Não|
|Título|cadeia de caracteres|Não|
|Descrição|cadeia de caracteres|Não|
|CreationDate|cadeia de caracteres|Não|
|Proprietário|cadeia de caracteres|Não|
|ThumbnailUrl|cadeia de caracteres|Não|
|VideoUrl|cadeia de caracteres|Não|
|VideoDuration|número inteiro|Não|
|VideoProcessingStatus|número inteiro|Não|
|ViewCount|número inteiro|Não|


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
