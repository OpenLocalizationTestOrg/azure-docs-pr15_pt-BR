<properties 
    pageTitle="Como criar um processador de mídia | Microsoft Azure" 
    description="Aprenda a criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar o conteúdo de mídia para serviços de mídia do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância de processador de mídia


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTANTE](media-services-rest-get-media-processor.md)

##<a name="overview"></a>Visão geral

Nos serviços de mídia que um processador de mídia é um componente que lida com uma tarefa de processamento específico, como codificação, formate conversão, criptografar ou descriptografar conteúdo de mídia. Você normalmente cria um processador de mídia quando você estiver criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

Nome do processador de mídia|Descrição|Mais informações
---|---|---
Padrão de codificador de mídia|Fornece recursos padrão de codificação de sob demanda. |[Visão geral e a comparação do Azure em demanda codificadores de mídia](media-services-encode-asset.md)
Fluxo de trabalho do Media codificador Premium|Permite executar tarefas de codificação usando fluxo de trabalho do Media codificador Premium.|[Visão geral e a comparação do Azure em demanda codificadores de mídia](media-services-encode-asset.md)
Indexador de mídia do Microsoft Azure| Permite que você verifique os arquivos de mídia e conteúdo pesquisável, bem como gerar faixas de legendas fechadas e palavras-chave.|[Indexador de mídia do Microsoft Azure](media-services-index-content.md)
Hyperlapse de mídia Azure (visualização)|Permite que você suaves check-out "impactos" em seu vídeo com vídeo estabilização. Também permite acelerar o seu conteúdo em um clipe de consumo.|[Hyperlapse de mídia do Microsoft Azure](media-services-hyperlapse-content.md)
Codificador de mídia do Microsoft Azure|Depreciado
Descriptografia de armazenamento| Depreciado|
Gerenciador de mídia do Microsoft Azure|Depreciado|
Criptografador de mídia do Microsoft Azure|Depreciado|

##<a name="get-mediaprocessor"></a>Obter MediaProcessor

>[AZURE.NOTE] Ao trabalhar com a API de REST de serviços de mídia, as considerações a seguir se aplicam:
>
>Quando estiver acessando entidades nos serviços de mídia, você deve definir campos de cabeçalho específico e valores em suas solicitações de HTTP. Para obter mais informações, consulte [instalação para desenvolvimento de API do resto de serviços de mídia](media-services-rest-how-to-use.md).

>Depois de conectar-se a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectando-se aos serviços de mídia usando API REST](media-services-rest-connect-programmatically.md). 


A chamada REST a seguir mostra como obter uma instância de processador de mídia por nome (neste caso, **Mídia codificador padrão**). 



    
Solicitação:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Resposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Próximas etapas

Agora que você sabe como obter uma instância de processador de mídia, vá para o tópico [como codificar um ativo](media-services-rest-get-started.md) que mostrará como usar o padrão de codificador de mídia para codificar um ativo.
