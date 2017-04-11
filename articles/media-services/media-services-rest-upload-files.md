<properties 
    pageTitle="Carregar arquivos para uma conta de serviços de mídia usando o resto | Microsoft Azure" 
    description="Saiba como obter o conteúdo de mídia em serviços de mídia criando e carregando ativos." 
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


# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar arquivos para uma conta de serviços de mídia usando REST

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTANTE](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

Em serviços de mídia, você pode carregar arquivos digitais em um ativo. A entidade de [ativos](https://msdn.microsoft.com/library/azure/hh974277.aspx) pode conter vídeo, áudio, imagens, conjuntos de miniaturas, texto tracks e legenda codificada arquivos (e os metadados sobre esses arquivos.)  Depois que os arquivos são carregados para o ativo, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming. 

>[AZURE.NOTE]As seguintes considerações se aplicam ao escolher um nome de arquivo ativo:
>
>- Serviços de mídia usa o valor da propriedade IAssetFile.Name quando estiver criando URLs para ver o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, a codificação de porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres de porcentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver apenas um '.' para a extensão de nome de arquivo.
>
>- O comprimento do nome não deve ser maior do que 260 caracteres.

O fluxo de trabalho básico para carregamento ativos é dividido em seções a seguir:

- Criar um ativo
- Criptografar um ativo (opcional)
- Carregar um arquivo para o armazenamento de blob

AMS também permite carregar ativos em massa. Para obter mais informações, consulte [Esta](media-services-rest-upload-files.md#upload_in_bulk) seção.

##<a name="upload-assets"></a>Carregar ativos

###<a name="create-an-asset"></a>Criar um ativo

>[AZURE.NOTE] Ao trabalhar com a API de REST de serviços de mídia, as considerações a seguir se aplicam:
>
>Quando estiver acessando entidades nos serviços de mídia, você deve definir campos de cabeçalho específico e valores em suas solicitações de HTTP. Para obter mais informações, consulte [instalação para desenvolvimento de API do resto de serviços de mídia](media-services-rest-how-to-use.md).

>Depois de conectar-se a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectando-se aos serviços de mídia usando API REST](media-services-rest-connect-programmatically.md). 
 
Um ativo é um contêiner para vários tipos ou conjuntos de objetos em serviços de mídia, incluindo vídeo, áudio, imagens, conjuntos de miniaturas, controles de texto e arquivos de legenda codificada. Na API REST, a criação de um ativo requer enviar solicitação POST aos serviços de mídia e fazer qualquer informação de propriedade sobre seu ativo no corpo da solicitação.

Uma das propriedades que você pode especificar ao criar um ativo é **Opções**. **Opções** é um valor de enumeração que descreve as opções de criptografia que pode ser criado um ativo com. Um valor válido é um dos valores na lista abaixo, não uma combinação de valores. 

- **Nenhum** = **0**: sem criptografia será usada. Este é o valor padrão. Observe que ao usar essa opção seu conteúdo não está protegido em trânsito ou inativos em armazenamento.
    Se você planeja fornecer um MP4 usando o download progressivo, use essa opção. 

- **StorageEncrypted** = **1**: Especifique se você deseja para seus arquivos sejam criptografados com criptografia AES-256 para carregar e armazenamento.

    Se seu ativo for armazenamento criptografado, você deve configurar política de entrega de ativos. Para obter mais informações, consulte [Configurando política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: especificar se você estiver carregando arquivos protegidos com um método de criptografia comum (como PlayReady). 

- **EnvelopeEncryptionProtected** = **4**: especificar se você estiver carregando HLS criptografados com arquivos AES. Observe que os arquivos devem foram codificados e criptografados por Gerenciador de transformar.

>[AZURE.NOTE]Se seu ativo usará criptografia, você deve criar uma **ContentKey** e vinculá-lo ao seu ativo, conforme descrito no tópico a seguir:[como criar um ContentKey](media-services-rest-create-contentkey.md). Observe que, depois de carregar os arquivos para o ativo, você precisa atualizar as propriedades de criptografia na entidade **AssetFile** com os valores obtido durante a criptografia de **ativos** . Fazer isso usando a solicitação HTTP **Mesclar** . 


O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny.mp4"}
    

**Resposta HTTP**

Se for bem-sucedida, será retornado o seguinte:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
###<a name="create-an-assetfile"></a>Criar um AssetFile

A entidade de [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa um arquivo de áudio ou vídeo que está armazenado em um contêiner de blob. Um arquivo de ativo sempre está associado a um ativo e um ativo pode conter um ou muitos arquivos de ativos. A tarefa de codificador de serviços de mídia falhará se um objeto de arquivo ativo não está associado um arquivo digital em um contêiner de blob.

Observe que a instância de **AssetFile** e o arquivo de mídia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Depois de carregar seu arquivo de mídia digital em um contêiner de blob, você usará a solicitação HTTP **Mesclar** para atualizar a AssetFile com informações sobre o seu arquivo de mídia (como mostrado posteriormente no tópico). 

**Solicitação HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criando a AccessPolicy com permissão de gravação. 

Antes de carregar arquivos para o armazenamento de blob, defina o acesso direitos de política para gravar um ativo. Para fazer isso, POSTE uma solicitação HTTP para o conjunto de entidade AccessPolicies. Definir um valor de DurationInMinutes após a criação ou você receberá uma mensagem de erro de servidor interno 500 volta em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

O exemplo a seguir mostra como criar um AccessPolicy:
        
**Solicitação HTTP**

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Solicitação HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

###<a name="get-the-upload-url"></a>Obter a URL de carregamento

Para receber a URL de carregamento real, crie um localizador de SAS. Locators definem hora de início e tipo de ponto de extremidade de conexão para clientes que deseja acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de ativos e AccessPolicy lidar com solicitações de cliente diferente e necessidades. Cada um desses Locators use o valor de hora de início mais o valor de DurationInMinutes do AccessPolicy para determinar o período de tempo que uma URL pode ser usada. Para obter mais informações, consulte [localizador](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Uma URL de SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações se aplicar:

- Você não pode ter mais de cinco Locators exclusivos associados a um determinado ativo ao mesmo tempo. Para obter mais informações, consulte localizador.
- Se você precisar fazer o upload de arquivos imediatamente, você deve definir o valor de hora de início para cinco minutos antes da hora atual. Isso ocorre porque pode haver relógio distorção entre o seu computador cliente e os serviços de mídia. Além disso, o valor de hora de início deve estar no seguinte formato DateTime: AAAA-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").   
- Pode haver um segundo 30-40 atrasar após um localizador é criado para quando ela estiver disponível para uso. Esse problema se aplica a URL de SAS e Locators de origem.

O exemplo a seguir mostra como criar um localizador de URL SAS, conforme definido pela propriedade tipo no corpo da solicitação ("1" para um localizador SAS) e "2" para um localizador de origem On Demand. A propriedade de **caminho** retornada contém a URL que você deve usar para carregar o arquivo.
    
**Solicitação HTTP**
    
    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta HTTP**

Se tiver êxito, a seguinte resposta é retornada:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregar um arquivo em um contêiner de armazenamento de blob
    
Quando você tiver o AccessPolicy e localizador definido, o arquivo real é carregado para um contêiner de armazenamento de Blob do Azure usando APIs REST de armazenamento do Azure. Você pode carregar na página ou bloquear blobs. 

>[AZURE.NOTE] Você deve adicionar o nome de arquivo para o arquivo que você deseja carregar para o valor de localizador **caminho** recebido na seção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do Blob serviço](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Atualizar o AssetFile 

Agora que você carregou o arquivo, atualize as informações de FileAsset tamanho (e outros). Por exemplo:
    
    MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

Se tiver êxito, o seguinte será retornado: HTTP/1.1 204 sem conteúdo

### <a name="delete-the-locator-and-accesspolicy"></a>Excluir o localizador e AccessPolicy 

**Solicitação HTTP**


    DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
**Resposta HTTP**

Se for bem-sucedida, será retornado o seguinte:

    HTTP/1.1 204 No Content 
    ...

**Solicitação HTTP**

    DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

**Resposta HTTP**

Se for bem-sucedida, será retornado o seguinte:

    HTTP/1.1 204 No Content 
    ...

##<a id="upload_in_bulk"></a>Carregar ativos em massa

###<a name="create-the-ingestmanifest"></a>Criar a IngestManifest

O IngestManifest é um contêiner para um conjunto de ativos, arquivos de ativo e informações de estatística que podem ser usadas para determinar o progresso em massa a inclusão para o conjunto.


**Solicitação HTTP**

    POST https:// media.windows.net/API/IngestManifests HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 36
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST" }

###<a name="create-assets"></a>Criar ativos

Antes de criar a IngestManifestAsset, você precisa criar o ativo que será concluído usando a inclusão em massa. Um ativo é um contêiner para vários tipos ou conjuntos de objetos em serviços de mídia, incluindo vídeo, áudio, imagens, conjuntos de miniaturas, controles de texto e arquivos de legenda codificada. Na API REST, a criação de um ativo requer enviar uma solicitação de HTTP POST aos serviços de mídia do Microsoft Azure e fazer qualquer informação de propriedade sobre seu ativo no corpo da solicitação. Neste exemplo, o ativo é criado usando a opção de StorageEncrption(1) incluída no corpo da solicitação.

**Resposta HTTP**

    POST https://media.windows.net/API/Assets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 55
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

###<a name="create-the-ingestmanifestassets"></a>Criar a IngestManifestAssets

IngestManifestAssets representam ativos em um IngestManifest que são usados com a inclusão de massa. O link basicamente o ativo no manifesto. Serviços de mídia do Azure internamente inspeções para o carregamento de arquivo com base na coleção de IngestManifestFiles associada a IngestManifestAsset. Depois que esses arquivos são carregados, o ativo seja concluído. Você pode criar um novo IngestManifestAsset com uma solicitação de HTTP POST. No corpo da solicitação, inclua a Id de IngestManifest e a Id de ativos que o IngestManifestAsset deve link juntos para a inclusão de massa.

**Resposta HTTP**

    POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 152
    Expect: 100-continue
    { "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}


###<a name="create-the-ingestmanifestfiles-for-each-asset"></a>Criar o IngestManifestFiles para cada ativo

Um IngestManifestFile representa um objeto real blob de áudio ou vídeo que será carregado como parte de massa a inclusão de um ativo. Criptografia relacionadas propriedades não são necessárias, a menos que o ativo está usando uma opção de criptografia. O exemplo usado nesta seção demonstra Criando um IngestManifestFile que usa StorageEncryption para o ativo que criou anteriormente.


**Resposta HTTP**

    POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 367
    Expect: 100-continue
    
    { "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }
    
###<a name="upload-the-files-to-blob-storage"></a>Carregue os arquivos ao armazenamento de Blob

Você pode usar qualquer aplicativo de cliente de alta velocidade capaz de carregar os arquivos de ativos ao contêiner de armazenamento de blob Uri fornecido pela propriedade BlobStorageUriForUpload a IngestManifest. Um serviço de carregamento de alta velocidade notáveis é [Aspera sob demanda para o aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=272001).

###<a name="monitor-bulk-ingest-progress"></a>Progresso de inclusão de massa de monitor

Você pode monitorar o andamento em massa a inclusão de operações para um IngestManifest pela sondagem a propriedade de estatísticas do IngestManifest. Essa propriedade é um tipo complexo, [IngestManifestStatistics](https://msdn.microsoft.com/library/azure/jj853027.aspx). Para votar a propriedade de estatísticas, envie uma solicitação GET HTTP passando o Id. IngestManifest
 

##<a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys usada para criptografia

Se seu ativo usará criptografia, você deve criar a ContentKey a ser usado para criptografia antes de criar os arquivos de ativos. Para criptografia de armazenamento, as seguintes propriedades devem ser incluídas no corpo da solicitação.
 
Propriedade de corpo da solicitação   | Descrição
---|---
ID | A identificação de ContentKey que podemos gerar nós usando o seguinte formato, "nb:kid:UUID:<NEW GUID>".
ContentKeyType | Este é o tipo de conteúdo de chave como um inteiro para esta chave de conteúdo. Podemos passar o valor 1 para criptografia de armazenamento.
EncryptedContentKey | Vamos criar um novo valor de chave conteúdo que é um valor de 256 bits (32 bytes). A chave é criptografada usando o certificado de x. 509 de criptografia de armazenamento que podemos recuperar de serviços de mídia do Microsoft Azure executando uma solicitação GET HTTP para os métodos de GetProtectionKey e GetProtectionKeyId.
ProtectionKeyId | Esta é a id de chave de proteção do certificado de x. 509 de criptografia de armazenamento que foi usado para criptografar o nosso chave de conteúdo.
ProtectionKeyType | Este é o tipo de criptografia para a chave de proteção que foi usado para criptografar a chave de conteúdo. Esse valor é StorageEncryption(1) para o nosso exemplo.
Soma de verificação |A MD5 calculada soma de verificação para a chave de conteúdo. Ele é calculado criptografando o Id de conteúdo com a chave de conteúdo. O código de exemplo demonstra como calcular a soma de verificação.


**Resposta HTTP**
    
    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 572
    Expect: 100-continue
    
    {"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### <a name="link-the-contentkey-to-the-asset"></a>Vincular a ContentKey do ativo

O ContentKey está associado a um ou mais ativos enviando uma solicitação de HTTP POST. Solicitação a seguir é um exemplo de vincular o exemplo ContentKey para o ativo de exemplo pelo ID.

**Resposta HTTP**
    
    POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 113
    Expect: 100-continue
    
    { "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

**Resposta HTTP**

    GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net



##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



 
[How to Get a Media Processor]: media-services-get-media-processor.md
 
