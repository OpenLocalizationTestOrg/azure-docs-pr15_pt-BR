<properties 
    pageTitle="Perguntas frequentes | Microsoft Azure" 
    description="Perguntas frequentes (perguntas frequentes)" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>


#<a name="frequently-asked-questions"></a>Perguntas frequentes

##<a name="general-ams-faqs"></a>Perguntas frequentes do AMS geral

P: como você dimensionar indexação?

R: as unidades de reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções sobre [como escala de codificação reservadas unidades](media-services-scale-media-processing-overview.md). **Observe** que o desempenho do indexador não é afetado por tipo de unidade reservadas.

P: é carregado, codificado e publicado um vídeo. Qual seria o motivo do vídeo não é reproduzido ao tentar transmiti-lo?

R: uma das razões mais comuns é que você não tenha pelo menos uma unidade de streaming reservada alocada no ponto de extremidade streaming do qual você está tentando reprodução.  Siga as instruções sobre [como escala Streaming reservadas unidades](media-services-portal-scale-streaming-endpoints.md).

P: posso fazer composição em um fluxo ao vivo?

R: composição em fluxos ao vivo atualmente não é oferecida no serviços de mídia do Azure, portanto você precisaria previamente redigir no seu computador.

P: é possível usar o Azure CDN com Live Streaming?

R: Media Services dá suporte a integração com o Azure CDN (para obter mais informações, consulte [como gerenciar Streaming pontos de extremidade de uma conta de serviços de mídia](media-services-portal-manage-streaming-endpoints.md)).  Você pode usar o Live streaming com CDN. Serviços de mídia do Azure fornece suave saídas de Streaming, HLS e MPEG-traço. Todos esses formatos usam HTTP para transferir dados e obtém benefícios do cache de HTTP. No live contínuo real dados de áudio/vídeo são divididos em fragmentos e este fragmentos individuais obtém armazenado no CDN. Apenas dados precisa ser atualizados são os dados manifesto. CDN periodicamente atualiza dados manifesto.

P: os serviços de mídia do Azure faz suportam armazenando imagens?

R: se você quiser apenas para armazenar imagens JPEG ou PNG, você deve manter aqueles no armazenamento de Blob do Azure. Não há nenhum benefício para colocá-los em sua conta de serviços de mídia, a menos que deseje mantê-las associada ao vídeo ou áudio ativos. Ou, se você pode precisar usar imagens como sobreposições no codificador de vídeo. Mídia codificador padrão suporta sobreposições de imagens na parte superior de vídeos, e que é o que ele listas JPEG e PNG como suportados formatos de entrada. Para obter mais informações, consulte [Criando sobreposições](media-services-custom-mes-presets-with-dotnet.md#overlay).

P: como copiar ativos de uma conta de serviços de mídia para outro.

R: para copiar ativos de uma conta de serviços de mídia para outro usando .NET, use o método de extensão de [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório [Do Azure mídia serviços .NET SDK extensões](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Para obter mais informações, consulte [Este](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread de Fórum.

P: quais são os caracteres com suporte para nomear arquivos ao trabalhar com AMS?

R: Media Services usa o valor da propriedade IAssetFile.Name ao criar URLs para ver o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, a codificação de porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres de porcentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver apenas um '.' para a extensão de nome de arquivo.


P: como se conectar usando o resto?

R: depois de conectar-se a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectando-se aos serviços de mídia usando API REST](media-services-rest-connect-programmatically.md). 


P: como é possível girar um vídeo durante o processo de codificação.

R: o [Codificador de mídia padrão](media-services-dotnet-encode-with-media-encoder-standard.md) é compatível com rotação por ângulos de 90/180/270. O comportamento padrão é "Automático", onde ele tentará detectar os metadados de rotação no arquivo MP4/MOV entrado e compensar a ele. Inclua o seguinte elemento de **fontes** para uma do json predefinições definido [aqui](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
