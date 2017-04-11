<properties 
    pageTitle="Introdução ao fornecimento de conteúdo sob demanda usando REST | Microsoft Azure" 
    description="Este tutorial orienta você pelas etapas de implementação de um aplicativo de distribuição de conteúdo on demand com os serviços de mídia do Azure usando API REST." 
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
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introdução ao fornecimento de conteúdo sob demanda usando REST 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Neste início rápido orienta você pelas etapas de implementação de um aplicativo de distribuição de conteúdo de vídeo sob demanda (VoD) usando APIs REST de serviços de mídia do Azure (AMS). 

O tutorial apresenta o fluxo de trabalho de serviços de mídia básico e os objetos de programação mais comuns e tarefas necessárias para desenvolvimento de serviços de mídia. Após a conclusão do tutorial, você poderá transmitir ou progressivamente baixar um arquivo de mídia de exemplo que você carregou codificado e baixados.  

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários para começar a desenvolver com os serviços de mídia com APIs REST.

- Noções básicas sobre como desenvolver com a API de REST de serviços de mídia. Para obter mais informações, consulte [mídia--restante-visão geral dos serviços](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- Um aplicativo de sua escolha que pode enviar solicitações e respostas HTTP. Este tutorial usa [Fiddler](http://www.telerik.com/download/fiddler). 

As seguintes tarefas são mostradas neste início rápido.

1.  Crie uma conta de serviços de mídia (usando o portal do Azure).
2.  Configure o ponto de extremidade de streaming (usando o portal do Azure).
1.  Conectar-se para a conta de serviços de mídia com a API REST.
1.  Criar um novo ativo e carregar um arquivo de vídeo com a API REST.
1.  Configure unidades de streaming com a API REST.
2.  Codifica o arquivo de origem em um conjunto de arquivos de MP4 adaptativa taxa de bits com a API REST.
1.  Publica os ativos e obter streaming e URLs de download progressivo com a API REST. 
1.  Reproduzir seu conteúdo. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crie uma conta de serviços de mídia do Azure usando o portal do Azure

As etapas desta seção mostram como criar uma conta de AMS.

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **mídia + CDN** > **Serviços de mídia**.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Em **Criar conta de serviços de mídia** insira valores necessários.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, insira o nome da nova conta AMS. Um nome de conta de serviços de mídia é todos os números em minúsculas ou letras sem espaços e caracteres de 3 a 24 de comprimento.
    2. Na assinatura, selecione entre as diferentes assinaturas do Azure que você tem acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que compartilham o ciclo de vida, permissões e políticas. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Em **local**, selecione a região geográfica é usada para armazenar os registros de mídia e metadados de sua conta de serviços de mídia. Essa região é usado para processar e transmitir sua mídia. Apenas as regiões de serviços de mídia disponíveis aparecem na caixa de listagem suspensa. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blob do conteúdo mídia de sua conta de serviços de mídia. Você pode selecionar uma conta existente do armazenamento na mesma região geográfica como sua conta de serviços de mídia, ou você pode criar uma conta de armazenamento. Uma nova conta de armazenamento é criada na mesma região. As regras para nomes de contas de armazenamento são as mesmas contas de serviços de mídia.

        Saiba mais sobre armazenamento [aqui](storage-introduction.md).

    4. Selecione **Fixar no painel de controle** para ver o progresso da implantação conta.
    
7. Clique em **criar** na parte inferior do formulário.

    Depois que a conta é criada com êxito, o status é alterado para **execução**. 

    ![Configurações de serviços de mídia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerenciar sua conta de AMS (por exemplo, carregar vídeos, codificar ativos, monitorar o progresso de tarefa) use a janela **configurações** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar pontos de extremidade streaming usando o portal do Azure

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando vídeo por meio de taxa de bits adaptativa streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia fornece embalagem dinâmica, que permite que você fornecer sua taxa de bits adaptativa MP4 codificado conteúdo streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) just-in-time, sem precisar armazenar predefinidas versões de cada um desses streaming formatos.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de arquivos de taxa de bits adaptativa MP4 (as etapas de codificação são demonstrou posteriormente neste tutorial).  
- Crie pelo menos uma unidade streaming para o *fluxo de extremidade* do qual você planeja entrega seu conteúdo. As etapas a seguir mostram como alterar o número de unidades de streaming.

Com embalagem dinâmica, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente.

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:


1. Na janela **configurações** , clique em **pontos de extremidade de Streaming**. 

2. Clique no padrão streaming ponto de extremidade. 

    A janela de **Detalhes de ponto de EXTREMIDADE de STREAMING de padrão** é exibida.

3. Para especificar o número de unidades streaming, deslize o controle deslizante de **unidades de Streaming** .

    ![Unidades de streaming](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Salvar** para salvar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode levar até 20 minutos para ser concluída.

## <a id="connect"></a>Conectar-se para a conta de serviços de mídia com a API REST

Duas coisas são necessárias ao acessar serviços de mídia do Azure: um token de acesso fornecido pelo serviços de controle de acesso do Azure (ACS) e os serviços de URI de mídia em si. Você pode usar qualquer meio desejado ao criar essas solicitações, desde que você especifica os valores corretos de cabeçalho e passa no token de acesso corretamente ao chamar para serviços de mídia.

As etapas a seguir descrevem o fluxo de trabalho mais comuns ao usar a API de REST de serviços de mídia para se conectar aos serviços de mídia:

1. Obtendo um token de acesso. 
2. Conectando-se aos serviços de mídia URI.  

    Lembre-se de que, depois de conectar-se a https://media.windows.net, você recebe um redirecionamento 301 especificando outro URI de serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI. Você também pode receber uma resposta HTTP/1.1 200 que contém a descrição de metadados de API do ODATA.
3. Postando suas chamadas de API subsequentes para a nova URL. 
    
    Por exemplo, se depois de tentar se conectar, você tem o seguinte:
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Você deve publicar suas chamadas de API subsequentes para https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Obtendo um token de acesso

Para acessar os serviços de mídia diretamente através da API REST, recuperar um token de acesso do ACS e usá-lo durante a cada solicitação HTTP feita no serviço. Você não precisar quaisquer outros pré-requisitos antes de conectar-se diretamente aos serviços de mídia.

O exemplo a seguir mostra o cabeçalho de solicitação HTTP e o corpo usado para recuperar um token.

**Cabeçalho**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Corpo**:

Você precisa demonstrou os valores client_id e client_secret no corpo dessa solicitação; client_id e client_secret correspondem aos valores AccountName e AccountKey, respectivamente. Esses valores são fornecidos pela Media Services ao configurar sua conta. 

O AccountKey para a sua conta de serviços de mídia deve ser codificada como URL quando usá-lo como o valor de client_secret em sua solicitação de token de acesso.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Por exemplo: 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


O exemplo a seguir mostra a resposta HTTP que contém o acesso token no corpo da resposta.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
É recomendável armazenar em cache os valores "access_token" e "expires_in" para um armazenamento externo. Os dados de token posteriormente poderiam ser recuperados do armazenamento e reutilizados em suas chamadas de API de REST de serviços de mídia. Isso é especialmente útil para cenários em que o token pode ser compartilhado com segurança entre vários processos ou computadores.

Verifique se o valor de "expires_in" de token de acesso de monitoramento e atualizar suas chamadas de API REST com novos tokens conforme necessário.

###<a name="connecting-to-the-media-services-uri"></a>Conexão com os serviços de mídia URI

O URI para serviços de mídia raiz é https://media.windows.net/. Inicialmente, você deve se conectar a esse URI e se você receber um redirecionamento 301 volta em resposta, você deve fazer chamadas subsequentes para o novo URI. Além disso, não use qualquer lógica de auto-redirect/acompanhar em suas solicitações. Verbos HTTP e corpos de solicitação não serão encaminhados para o novo URI.

A raiz URI para carregar e baixar arquivos de ativos é https://yourstorageaccount.blob.core.windows.net/ onde o nome da conta de armazenamento é o mesmo usado durante a configuração de conta de serviços de mídia.

O exemplo a seguir demonstra solicitação HTTP para a raiz de serviços de mídia URI (https://media.windows.net/). A solicitação obtém um redirecionamento 301 novamente na resposta. A solicitação subsequente está usando o novo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Solicitação HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Resposta HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Solicitação HTTP** (usando o novo URI):
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] De agora em diante o novo URI é usado neste tutorial.

## <a id="upload"></a>Criar um novo ativo e carregar um arquivo de vídeo com a API REST

Em serviços de mídia, você pode carregar arquivos digitais em um ativo. A entidade de **ativos** pode conter vídeo, áudio, imagens, conjuntos de miniaturas, texto tracks e legenda codificada arquivos (e os metadados sobre esses arquivos.)  Depois que os arquivos são carregados para o ativo, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming. 

Um dos valores que você deve fornecer quando a criação de um ativo é opções de criação de ativos. A propriedade de **Opções** é um valor de enumeração que descreve as opções de criptografia que pode ser criado um ativo com. Um valor válido é um dos valores na lista abaixo, não uma combinação de valores dessa lista:

 
- **Nenhum** = **0** - sem criptografia é usado. Ao usar essa opção seu conteúdo não está protegido em trânsito ou inativos em armazenamento.
    Se você planeja fornecer um MP4 usando o download progressivo, use essa opção. 
- **StorageEncrypted** = **1** - criptografa seu conteúdo limpar localmente usando criptografia AES-256 e carrega o armazenamento do Azure onde ele está armazenado criptografado no restante. Ativos protegidos com criptografia de armazenamento são automaticamente não criptografados e colocados em um sistema de arquivos criptografados antes de codificação e, opcionalmente, criptografados novamente antes de carregar volta como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte inativos no disco.
- **CommonEncryptionProtected** = **2** - Use essa opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou PlayReady DRM (por exemplo, suave Streaming protegido com PlayReady DRM).
- **EnvelopeEncryptionProtected** = **4** – Use esta opção se você estiver carregando HLS criptografados com AES. Os arquivos devem foram codificados e criptografados por transformar gerente.

### <a name="create-an-asset"></a>Criar um ativo

Um ativo é um contêiner para vários tipos ou conjuntos de objetos em serviços de mídia, incluindo vídeo, áudio, imagens, conjuntos de miniaturas, controles de texto e arquivos de legenda codificada. Na API REST, a criação de um ativo requer enviar solicitação POST aos serviços de mídia e fazer qualquer informação de propriedade sobre seu ativo no corpo da solicitação.

O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

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
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Criar um AssetFile

A entidade de [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa um arquivo de áudio ou vídeo que está armazenado em um contêiner de blob. Um arquivo de ativo sempre está associado a um ativo e um ativo pode conter um ou vários AssetFiles. A tarefa de codificador de serviços de mídia falhará se um objeto de arquivo ativo não está associado um arquivo digital em um contêiner de blob.

Depois de carregar seu arquivo de mídia digital em um contêiner de blob, use a solicitação HTTP **Mesclar** para atualizar a AssetFile com informações sobre o seu arquivo de mídia (como mostrado posteriormente no tópico). 

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
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

Antes de carregar arquivos para o armazenamento de blob, defina o acesso direitos de política para gravar um ativo. Para fazer isso, POSTE uma solicitação HTTP para o conjunto de entidade AccessPolicies. Definir um valor de DurationInMinutes após a criação ou você recebe uma mensagem de erro de servidor interno 500 volta em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

O exemplo a seguir mostra como criar um AccessPolicy:
        
**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Resposta HTTP**

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
    X-Powered-By: ASP.NET
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

### <a name="get-the-upload-url"></a>Obter a URL de carregamento

Para receber a URL de carregamento real, crie um localizador de SAS. Locators definem hora de início e tipo de ponto de extremidade de conexão para clientes que deseja acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de ativos e AccessPolicy lidar com solicitações de cliente diferente e necessidades. Cada um desses Locators usa o valor de hora de início mais o valor de DurationInMinutes do AccessPolicy para determinar o período de tempo que uma URL pode ser usada. Para obter mais informações, consulte [localizador](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Uma URL de SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações se aplicar:

- Você não pode ter mais de cinco Locators exclusivos associados a um determinado ativo ao mesmo tempo. Para obter mais informações, consulte localizador.
- Se você precisar fazer o upload de arquivos imediatamente, você deve definir o valor de hora de início para cinco minutos antes da hora atual. Isso ocorre porque pode haver relógio distorção entre o seu computador cliente e os serviços de mídia. Além disso, o valor de hora de início deve estar no seguinte formato DateTime: AAAA-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").   
- Pode haver um segundo 30-40 atrasar após um localizador é criado para quando ela estiver disponível para uso. Esse problema se aplica a URL de SAS e Locators de origem.

O exemplo a seguir mostra como criar um localizador de URL SAS, conforme definido pela propriedade tipo no corpo da solicitação ("1" para um localizador SAS) e "2" para um localizador de origem On Demand. A propriedade de **caminho** retornada contém a URL que você deve usar para carregar o arquivo.
    
**Solicitação HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
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
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

Se tiver êxito, o seguinte será retornado: HTTP/1.1 204 sem conteúdo

## <a name="delete-the-locator-and-accesspolicy"></a>Excluir o localizador e AccessPolicy 

**Solicitação HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Resposta HTTP**

Se for bem-sucedida, será retornado o seguinte:

    HTTP/1.1 204 No Content 
    ...

**Solicitação HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta HTTP**

Se for bem-sucedida, será retornado o seguinte:

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurar unidades de streaming com a API REST

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando adaptativa taxa de bits streaming a seus clientes. Com o streaming de taxa de bits adaptativa, o cliente pode alternar para um fluxo de taxa de bits superiores ou inferiores como o vídeo é exibido com base na largura de banda de rede atual, a utilização de CPU e outros fatores. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso). 

Serviços de mídia fornece embalagem dinâmica, que permite que você distribuir o conteúdo de taxa de bits adaptativa MP4 ou Streaming suave codificado de streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) sem precisar empacotar novamente para esses formatos de fluxo contínuo. 

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Obtenha pelo menos uma unidade de fluxo para o **fluxo de extremidade **do qual você planeja distribuir o conteúdo (descrito nesta seção).
- codificar ou decodificar arquivo de seu mezzanine (origem) em um conjunto de taxa de bits adaptativa MP4 arquivos ou taxa de bits adaptativa Streaming suave (as etapas de codificação são demonstrou posteriormente neste tutorial),  

Com embalagem dinâmica, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente. 


>[AZURE.NOTE] Para obter informações sobre preços detalhes, consulte [Detalhes de preços de serviços de mídia](http://go.microsoft.com/fwlink/?LinkId=275107).

Para alterar o número de unidades reservadas de streaming, faça o seguinte:
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Obter o ponto de extremidade streaming que você deseja atualizar

Por exemplo, vamos primeiro ponto de extremidade streaming em sua conta (você pode ter até dois pontos de extremidade de streaming em estado de execução ao mesmo tempo.)

**Solicitação HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta HTTP**
    
Se for bem-sucedida, será retornado o seguinte:
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Dimensionar o ponto de extremidade de streaming
 
**Solicitação HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Resposta HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Verificar o status de uma operação de execução demorada

A alocação de qualquer novas unidades leva cerca de 20 minutos para ser concluída. Para verificar o status da operação, use o método de **operações** e especificar a identificação da operação. A operação de identificação retornada na resposta à solicitação de **escala** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Solicitação HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Resposta HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Codificar o arquivo de origem em um conjunto de arquivos de MP4 de taxa de bits adaptativa

Após a inclusão de que ativos para serviços de mídia, mídia podem ser codificados, transmuxed, marca d'água e assim por diante, antes ele foi entregue para clientes. Essas atividades são agendadas e executadas várias instâncias de função de plano de fundo para garantir alto desempenho e disponibilidade. Essas atividades são chamadas trabalhos e cada [trabalho](http://msdn.microsoft.com/library/azure/hh974289.aspx) é composta por tarefas atômicas que fazer o trabalho real no arquivo ativo. 

Conforme mencionado anteriormente, quando trabalhar com serviços de mídia do Azure um dos cenários mais comuns está oferecendo adaptativa taxa de bits streaming aos seus clientes. Serviços de mídia dinamicamente pode empacotar um conjunto de arquivos de taxa de bits adaptativa MP4 em um dos seguintes formatos: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso). 

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- codificar ou decodificar arquivo de seu mezzanine (origem) em um conjunto de taxa de bits adaptativa MP4 arquivos ou taxa de bits adaptativa Streaming suave,  
- Obtenha pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja distribuir seu conteúdo. 

A seção a seguir mostra como criar um trabalho que contém uma tarefa de codificação. A tarefa especifica para decodificar o arquivo mezzanine em um conjunto de adaptativa taxa de bits MP4s usando **Mídia codificador padrão**. A seção também mostra como monitorar o andamento de processamento de trabalho. Quando o trabalho for concluído, você poderá criar locators que são necessárias para obter acesso aos seus ativos. 

### <a name="get-a-media-processor"></a>Obter um processador de mídia

Em serviços de mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específico, como codificação, conversão de formato, conteúdo de mídia de criptografia ou descriptografia. Para a tarefa de codificação mostrada neste tutorial, vamos usar o padrão de codificador de mídia.

O código a seguir solicita id do codificador. 

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
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

### <a name="create-a-job"></a>Criar um trabalho

Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar trabalhos e suas tarefas relacionadas em uma de duas maneiras: tarefas podem ser definidas embutidas por meio da propriedade de navegação de tarefas em entidades de trabalho ou processamento em lotes de OData. O Media Services SDK usa processamento em lotes. No entanto, para a legibilidade dos exemplos de código neste tópico, as tarefas são definidas embutidas. Para obter informações sobre processamento em lotes, consulte [Processamento de lote de Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

O exemplo a seguir mostra como criar e publicar um trabalho com uma que tarefa definida para codificar um vídeo em uma resolução específica e a qualidade. A seção de documentação a seguir contém a lista de todas as [tarefas predefinições](http://msdn.microsoft.com/library/mt269960) suportado pelo processador mídia codificador padrão.  

**Solicitação HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Resposta HTTP**

Se tiver êxito, a seguinte resposta é retornada:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Há algumas coisas importantes nota em qualquer solicitação de trabalho:

- Propriedades de TaskBody devem usar XML literal para definir o número de entrada ou saída ativos que são usados pela tarefa. O tópico da tarefa contém a definição de esquema XML para o XML.
- Na definição de TaskBody, cada interna valor para <inputAsset> e <outputAsset> deve ser definida como JobInputAsset(value) ou JobOutputAsset(value).
- Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
- Você pode especificar JobInputAsset ou JobOutputAsset como um entrada ativo de uma tarefa.
- Tarefas não devem formam um ciclo.
- O parâmetro de valor que você passa para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades navegação InputMediaAssets e OutputMediaAssets na definição de entidade de trabalho. 

>[AZURE.NOTE] Porque os serviços de mídia é criado no OData v3, os ativos individuais coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um "__metadata: uri" par nome-valor. 

- InputMediaAssets mapeia para um ou mais ativos que você criou nos serviços de mídia. OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
- OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, o nome da OutputMediaAsset é qualquer valor de texto interna do <outputAsset> elemento for com um sufixo do valor de nome de trabalho ou o valor de Id do trabalho (no caso onde a propriedade de nome não estiver definida). Por exemplo, se você definir um valor para assetName a "Amostra", a propriedade nome do OutputMediaAsset seria definir a "Amostra". No entanto, se você não definiu um valor para assetName, mas tiver configurado o nome de trabalho para "NewJob", o nome de OutputMediaAsset seria "_NewJob JobOutputAsset (valor)". 

    O exemplo a seguir mostra como definir o atributo assetName:
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Para habilitar o encadeamento de tarefa:

    - Um trabalho deve ter pelo menos duas tarefas
    - Deve haver pelo menos uma tarefa cuja entrada é a saída de outra tarefa no trabalho.

Para obter mais informações, consulte [Criando um trabalho de codificação com a API de REST de serviços de mídia](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Monitor de progresso de processamento

Você pode recuperar o status do trabalho usando a propriedade de estado, conforme mostrado no exemplo a seguir. 

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta HTTP**

Se tiver êxito, a seguinte resposta é retornada:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar um trabalho

Serviços de mídia permite cancelar trabalhos em execução por meio da função CancelJob. Esta chamada retorna um código de erro 400 se você tentar cancelar um trabalho quando seu estado for cancelado, cancelando, erro, ou terminar.

O exemplo a seguir mostra como chamar CancelJob.


**Solicitação HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


Se for bem-sucedida, um código de 204 resposta será retornado com nenhum corpo da mensagem.

>[AZURE.NOTE] Você deve codificar a URL a identificação de trabalho (normalmente nb:jid:UUID: somevalue) ao passá-lo como um parâmetro para CancelJob.


### <a name="get-the-output-asset"></a>Obtenha o ativo de saída 

O código a seguir mostra como solicitar o ativo de saída ID. 


**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Publicar os ativos e obter streaming e URLs de download progressivo com a API REST

Para transmitir ou baixar um ativo, primeiro é necessário para o "Publicar" Criando um localizador. Locators fornecem acesso a arquivos contidos em ativo. Serviços de mídia compatível com dois tipos de localizadores: locators OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG traço, HLS ou Streaming suave) e locators de assinatura de acesso (SAS), usado para baixar arquivos de mídia.

Depois de criar os locators, você pode criar as URLs que são usadas para transmitir ou baixar arquivos. 


Uma URL de streaming do Streaming suave tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Uma URL streaming para HLS tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Uma URL de streaming de traço MPEG tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Uma URL de SAS usada para baixar arquivos tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta seção mostra como executar as seguintes tarefas necessárias para "publicação" seus ativos.  

- Criando a AccessPolicy com permissão de leitura 
- Criar uma URL de SAS para download de conteúdo 
- Criando uma URL de origem para transmitir conteúdo 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Criando a AccessPolicy com permissão de leitura

Antes de baixar ou qualquer conteúdo de mídia de streaming, primeiro definir um AccessPolicy com permissões de leitura e criar a entidade de localizador apropriada que especifica o tipo de mecanismo de entrega que você deseja habilitar para seus clientes. Para obter mais informações sobre as propriedades disponíveis, consulte [Propriedades de entidade AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

O exemplo a seguir mostra como especificar o AccessPolicy para permissões de leitura para um determinado ativo.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Se bem-sucedida, será retornado um código de 201 sucesso descrevendo a entidade de AccessPolicy que você criou. Você usar a identificação de AccessPolicy juntamente com a Id de ativos do ativo que contém o arquivo que você deseja entregar (como um ativo de saída) para criar a entidade de localizador.

>[AZURE.NOTE]
Este fluxo de trabalho básico é a mesma que ao carregar um arquivo quando a inclusão de um ativo (conforme foi discutido anteriormente neste tópico). Além disso, como carregar arquivos, se você (ou seus clientes) precisam acessar seus arquivos imediatamente, defina o valor de hora de início para cinco minutos antes da hora atual. Esta ação é necessária porque pode haver relógio distorção entre o cliente e os serviços de mídia. O valor de hora de início deve estar no seguinte formato DateTime: AAAA-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").


###<a name="creating-a-sas-url-for-downloading-content"></a>Criar uma URL de SAS para download de conteúdo 

O código a seguir mostra como obter uma URL que pode ser usada para baixar um arquivo de mídia criada e carregados anteriormente. O AccessPolicy tem o conjunto de permissões de leitura e o caminho de localizador se refere a uma URL de download do SAS.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Se tiver êxito, a seguinte resposta é retornada:

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


A propriedade de **caminho** retornada contém a URL de SAS.

>[AZURE.NOTE]
Se você baixar conteúdo armazenamento criptografado, manualmente você deve descriptografá-lo antes de renderização ou usar o armazenamento descriptografia MediaProcessor em uma tarefa de processamento para arquivos processados em Limpar para um OutputAsset de saída e, em seguida, Baixe esse ativo. Para obter mais informações sobre processamento, consulte Criando um trabalho de codificação com a API de REST de serviços de mídia. Além disso, SAS URL Locators não pode ser atualizado após eles foram criados. Por exemplo, você não pode reutilizar o localizador mesmo com um valor de hora de início atualizado. Isso é devido a maneira como as URLs de SAS são criadas. Se você quiser acessar um ativo para download após um localizador ter expirado, você deve criar um novo com uma nova hora de início.

###<a name="download-files"></a>Baixar arquivos

Quando você tiver o AccessPolicy e localizador definido, você pode baixar arquivos usando as APIs do Azure armazenamento restante.  

>[AZURE.NOTE] Você deve adicionar o nome de arquivo para o arquivo que você deseja baixar para o valor de localizador **caminho** recebido na seção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do Blob serviço](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Como resultado de trabalho de codificação que você executou anterior (codificação em conjunto de adaptativa MP4), você tem vários arquivos MP4 que você pode baixar cada vez. Por exemplo:    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Criar uma URL de streaming para transmitir conteúdo


O código a seguir mostra como criar um localizador de URL streaming:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Se tiver êxito, a seguinte resposta é retornada:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir uma URL de origem Streaming suave em um media player de streaming, você deve acrescentar o caminho de propriedade com o nome do fluxo de suavização arquivo, seguido de manifesto "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir HLS, acrescentar (formato = m3u8-aapl) após o "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir MPEG traço, acrescentar (formato = mpd-tempo-csf) após o "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproduzir seu conteúdo  

Para você transmitir vídeo, use [O reprodutor de serviços de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, IE, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Próximas etapas: Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Procurando algo mais?

Se este tópico não contêm o que você estava esperando, está faltando algo ou em alguma outra maneira não atender às suas necessidades, forneça-nos seus comentários usando o segmento Disqus abaixo.



