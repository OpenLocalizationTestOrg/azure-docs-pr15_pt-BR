<properties 
    pageTitle="Como codificar um ativo usando mídia codificador padrão | Microsoft Azure" 
    description="Saiba como usar o padrão de codificador de mídia para codificar o conteúdo de mídia em serviços de mídia. Exemplos de código usam API REST." 
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


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Como codificar um ativo usando mídia codificador padrão


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTANTE](media-services-rest-encode-asset.md)
- [Portal](media-services-portal-encode.md)

##<a name="overview"></a>Visão geral
Para distribuir vídeo digital pela internet, você deve compactar mídia. Arquivos de vídeo digital são muito grandes e podem ser muito grandes para oferecer pela internet ou para dispositivos de seus clientes exibir corretamente. Codificação é o processo de compactação de áudio e vídeo para que seus clientes possam ver sua mídia.

Codificação de trabalhos são uma das operações de processamento mais comuns nos serviços de mídia. Criar trabalhos de codificação para converter arquivos de mídia de uma codificação para outro. Quando você codificar, você pode usar o codificador interno de serviços de mídia (mídia codificador padrão). Você também pode usar um codificador fornecido por um parceiro de serviços de mídia; codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes de codificação tarefas utilizando cadeias de caracteres predefinidas definidas para seu codificador ou usando arquivos de configuração predefinido. Para ver os tipos de predefinições disponíveis, consulte [Predefinições de tarefa para mídia codificador padrão](http://msdn.microsoft.com/library/mt269960).

Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar trabalhos e suas tarefas relacionadas de duas maneiras:

- Tarefas podem ser embutida definida por meio da propriedade de navegação de tarefas em entidades de trabalho, ou
- por meio de processamento de lote do OData.


É recomendável sempre codificar seus arquivos mezzanine em uma taxa de bits adaptativa MP4 definir e, em seguida, converter o conjunto para o formato desejado usando na [Embalagem dinâmico](media-services-dynamic-packaging-overview.md). Para tirar proveito da embalagem dinâmico, você deve primeiro obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [como serviços de mídia de escala](media-services-portal-manage-streaming-endpoints.md).

Se seu ativo de saída for armazenamento criptografado, você deve configurar política de entrega de ativos. Para obter mais informações, consulte [Configurando política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Antes de começar referenciando processadores de mídia, verifique se você tem a mídia correta ID processador. Para obter mais informações, consulte [Obter processadores de mídia](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Criar um trabalho com uma única tarefa de codificação

>[AZURE.NOTE] Ao trabalhar com a API de REST de serviços de mídia, as considerações a seguir se aplicam:
>
>Quando estiver acessando entidades nos serviços de mídia, você deve definir campos de cabeçalho específico e valores em suas solicitações de HTTP. Para obter mais informações, consulte [instalação para desenvolvimento de API do resto de serviços de mídia](media-services-rest-how-to-use.md).

>Depois de conectar-se a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectando-se aos serviços de mídia usando API REST](media-services-rest-connect-programmatically.md).
>
>Quando usando JSON e especificando para usar a palavra-chave **__metadata** na solicitação (por exemplo, para referencia um objeto vinculado) que você deve definir o cabeçalho de **Aceitar** formato [JSON detalhado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): aceitar: aplicativo/json; odata = detalhado.

O exemplo a seguir mostra como criar e publicar um trabalho com uma que tarefa definida para codificar um vídeo em uma resolução específica e a qualidade. Quando a codificação com o codificador de mídia padrão, você pode usar predefinições de configuração de tarefa especificadas [aqui](http://msdn.microsoft.com/library/mt269960).

Solicitação:

POSTAGEM https://media.windows.net/API/Jobs tipo de conteúdo HTTP/1.1: aplicativo/json; odata = aceitar detalhado: aplicativo/json; odata = DataServiceVersion detalhado: MaxDataServiceVersion 3.0: 3.0 x-ms-version: autorização 2.11: portador <token value> 
 x-ms-cliente-solicitação-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Definir o nome do ativo de saída

O exemplo a seguir mostra como definir o atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Considerações

- Propriedades de TaskBody devem usar XML literal para definir o número de entrada ou saída ativos que serão usados pela tarefa. O tópico da tarefa contém a definição de esquema XML para o XML.
- Na definição de TaskBody, cada interna valor para <inputAsset> e <outputAsset> deve ser definida como JobInputAsset(value) ou JobOutputAsset(value).
- Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
- Você pode especificar JobInputAsset ou JobOutputAsset como um entrada ativo de uma tarefa.
- Tarefas não devem formam um ciclo.
- O parâmetro de valor que você passa para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades navegação InputMediaAssets e OutputMediaAssets na definição de entidade de trabalho. 
- Porque os serviços de mídia é criado no OData v3, os ativos individuais coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um "__metadata: uri" par nome-valor.
- InputMediaAssets mapeia para um ou mais ativos que você criou nos serviços de mídia. OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
- OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, o nome do OutputMediaAsset será qualquer o valor de texto interna a <outputAsset> elemento for com um sufixo do valor de nome de trabalho ou o valor de Id do trabalho (no caso onde a propriedade de nome não estiver definida). Por exemplo, se você definir um valor para assetName a "Amostra", a propriedade nome do OutputMediaAsset seria definir a "Amostra". No entanto, se você não definiu um valor para assetName, mas tiver configurado o nome de trabalho para "NewJob", o nome de OutputMediaAsset seria "_NewJob JobOutputAsset (valor)". 


##<a name="create-a-job-with-chained-tasks"></a>Criar um trabalho com tarefas encadeadas

Em muitos cenários de aplicativo, os desenvolvedores querer criar uma série de tarefas de processamento. Em serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar processadores de mídia diferente. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, executando uma sequência linear das tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos **ITask** encadeados são criados em um único objeto de **IJob** .

>[AZURE.NOTE] Atualmente, há um limite de 30 tarefas por trabalho. Se você precisar cadeia mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Considerações

Para habilitar o encadeamento de tarefa:

- Um trabalho deve ter pelo menos 2 tarefas
- Deve haver pelo menos uma tarefa cuja entrada é a saída de outra tarefa no trabalho.

## <a name="use-odata-batch-processing"></a>Usar o processamento em lotes de OData 

O exemplo a seguir mostra como usar o processamento em lotes de OData para criar um trabalho e tarefas. Para obter informações sobre processamento em lotes, consulte [Processamento de lote de Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Criar um trabalho usando um modelo de trabalho


Ao processar vários ativos usando um conjunto comum de tarefas, JobTemplates são úteis para especificar as predefinições de tarefa padrão, a ordem das tarefas e assim por diante.

O exemplo a seguir mostra como criar um modelo de trabalho com uma embutida TaskTemplate definido. O TaskTemplate usa o padrão de codificador de mídia como o MediaProcessor para codificar o arquivo ativo; No entanto, outros MediaProcessors pode ser usado também. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Ao contrário de outras entidades de serviços de mídia, você deve definir um novo identificador GUID para cada TaskTemplate e coloque-o no taskTemplateId e propriedade Id no corpo da solicitação. O esquema de identificação conteúdo deve acompanhar o esquema descrito em identificar entidades de serviços de mídia do Azure. Além disso, JobTemplates não podem ser atualizados. Em vez disso, você deve criar um novo com suas alterações atualizadas.
 

Se tiver êxito, a seguinte resposta é retornada:
    
    HTTP/1.1 201 Created
    
    . . .


O exemplo a seguir mostra como criar um trabalho fazendo referência a uma Id de modelo de trabalho:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

Se tiver êxito, a seguinte resposta é retornada:
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Próximas etapas
Agora que você sabe como criar um trabalho para codificar uma assset, vá para o tópico [Como para verificar andamento trabalho com os serviços de mídia](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Consulte também

[Obter processadores de mídia](media-services-rest-get-media-processor.md)
