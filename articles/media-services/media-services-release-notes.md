<properties 
    pageTitle="Notas de versão de serviços de mídia | Microsoft Azure" 
    description="Notas de serviços de mídia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Notas de versão de serviços de mídia do Azure

Estas notas resumem alterações de versões anteriores e problemas conhecidos.

>[AZURE.NOTE] Queremos ouvir de nossos clientes e focalizar corrigindo problemas que afetam a você. Para relatar um problema ou fazer perguntas, poste no [Fórum de MSDN dos serviços de mídia do Azure].


##<a id="issues"></a>Problemas conhecidos no momento

### <a id="general_issues"></a>Problemas gerais de serviços de mídia

Problema|Descrição
---|---
Diversos cabeçalhos comuns de HTTP não são fornecidos na API REST.|Se você desenvolver aplicativos de serviços de mídia usando a API REST, você descobre que alguns campos comuns dos cabeçalhos HTTP (incluindo cliente-solicitação-ID, ID da solicitação e retorno-cliente-solicitação-ID) não são suportados. Os cabeçalhos serão adicionados em uma atualização futura.
Não é permitida a codificação de porcentagem.|Serviços de mídia usa o valor da propriedade IAssetFile.Name quando estiver criando URLs para ver o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, a codificação de porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres de porcentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver apenas um '.' para a extensão de nome de arquivo.
O método ListBlobs que faz parte da falha de 3. x versão SDK de armazenamento do Azure.|Serviços de mídia gera URLs SAS baseado na versão [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Se você quiser usar o SDK de armazenamento do Azure para blobs de lista em um contêiner de blob, use o método de [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que faz parte de versão SDK de armazenamento do Azure 2. x. O método ListBlobs que faz parte da versão SDK de armazenamento do Azure 3. x falhará.
Otimização do mecanismo de serviços de mídia restringe o uso de recursos para aplicativos que fazer excessiva solicitação para o serviço. O serviço pode retornar o código de status do serviço indisponível (503) HTTP.|Para obter mais informações, consulte a descrição do código de status HTTP 503 no tópico [Códigos de erro de serviços de mídia do Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Ao consultar entidades, há um limite de 1000 entidades retornadas de uma vez porque público v2 restante limita os resultados de consulta aos resultados de 1000. | Você precisa usar **Ignorar** e **levar** (.NET) / **superior** (REST), como descrito [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Alguns clientes podem se originar em um problema de repetição marca no manifesto Streaming suave.|Para obter mais informações, consulte [Esta](media-services-deliver-content-overview.md#known-issues) seção.
Objetos de mídia serviços .NET SDK Azure não podem ser serializados e como resultado não funcionam com o cache do Azure.|Se você tentar serializar o objeto AssetCollection SDK para adicioná-lo cache do Azure, uma exceção é lançada.
Trabalhos de codificação falharem com uma cadeia de caracteres de mensagem "estágio: DownloadFile. Código: estão ".|O fluxo de trabalho de codificação típico é carregar arquivos de vídeo de entrada em um ativo de entrada e enviar um ou mais trabalhos de codificação para essa entrada ativo, sem modificar ainda mais que a entrada ativo. No entanto, se você modificar o ativo de entrada (por exemplo, ao adicionar/excluir/renomeando arquivos dentro do ativo), trabalhos subsequentes podem falhar com um erro de DownloadFile. A solução alternativa é excluir o entrada ativo e carregue novamente arquivos de entrada para um novo ativo. 

##<a id="rest_version_history"></a>Histórico de versão da API REST

Para obter informações sobre o histórico de versão da API de REST de serviços de mídia, consulte [Referência API do restante dos serviços de mídia do Azure].

##<a id="july_changes16"></a>Lançamento de julho de 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações de arquivo de manifesto (*. ISM) gerado pelo codificação de tarefas

Quando uma tarefa de codificação é enviada para mídia codificador padrão ou codificador de mídia do Azure, a tarefa de codificação gera um [arquivo de manifesto streaming](media-services-deliver-content-overview.md) (* .ism) arquivo na saída ativo. Com a versão mais recente do serviço, a sintaxe deste arquivo manifesto streaming foi atualizada.

>[AZURE.NOTE]A sintaxe do arquivo manifesto (.ism) streaming é reservada para uso interno e está sujeita a alterações em versões futuras. Não modifique ou manipular o conteúdo deste arquivo.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. Arquivo ISMC) é gerado na saída ativo quando uma tarefa de codificação produz um ou mais arquivos MP4

Começando com a versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um mais MP4 arquivos, a saída ativos também conterá um arquivo de manifesto (*.ismc) streaming do cliente. O arquivo de .ismc ajuda a melhorar o desempenho do streaming dinâmico. 

>[AZURE.NOTE]A sintaxe do arquivo de manifesto (.ismc) do cliente é reservada para uso interno e está sujeita a alterações em versões futuras. Não modifique ou manipular o conteúdo deste arquivo.

Para obter mais informações, consulte [Este](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Problemas conhecidos

Alguns clientes podem se originar aross um problema de repetição marca no manifesto Streaming suave. Para obter mais informações, consulte [Esta](media-services-deliver-content-overview.md#known-issues) seção.

##<a id="apr_changes16"></a>Lançamento de abril de 2016

### <a name="azure-media-analytics"></a>Análise de mídia do Microsoft Azure

Azure Servces de mídia introduziu a análise de mídia do Azure para inteligência de vídeo avançada. Para obter informações detalhadas, consulte [Visão geral de análise de serviços de mídia do Azure](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (prévia)

Serviços de mídia do Azure agora permite que você criptografe dinamicamente seu HTTP Live Streaming (HLS) conteúdo com FairPlay da Apple. Você também pode usar o serviço de entrega de licença AMS para entregar FairPlay licenças para clientes. Para obter informações mais detalhadas, consulte [usar serviços de mídia do Azure para transmitir sua HLS conteúdo protegido com o Apple FairPlay ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Lançamento de fevereiro de 2016

Versão mais recente do SDK de serviços de mídia do Azure para .NET (3.5.3) contém uma correção de erros Widevine relacionada. O problema foi: AssetDeliveryPolicy não pôde ser reutilizada para vários ativos criptografados com Widevine. Como parte dessa correção de bugs a propriedade a seguir foi adicionada ao SDK: **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Lançamento de janeiro de 2016

Codificação de unidades reservadas renomeados para reduzir a confusão com nomes de codificador.

As unidades de reservadas codificação Basic, Standard e Premium são renomeadas para S1, S2, e S3 reservados unidades, respectivamente.  Clientes que usam o RUs básicas de codificação hoje verá S1 como o rótulo no Portal do Azure (e na fatura), ao padrão e Premium verão os rótulos S2 e S3 respectivamente. 

##<a id="dec_changes_15"></a>Lançamento de dezembro de 2015

A equipe do Azure SDK publicado uma nova versão do pacote [SDK do Azure para PHP](http://github.com/Azure/azure-sdk-for-php) que contém atualizações e novos recursos para serviços de mídia do Microsoft Azure. Em particular, o SDK de serviços de mídia do Azure para PHP agora oferece suporte para os recursos mais recentes de [proteção de conteúdo](media-services-content-protection-overview.md) : criptografia dinâmico com o AES e DRM (PlayReady e Widevine) com e sem restrição de Token. Ele também suporta [Unidades de codificação](media-services-dotnet-encoding-units.md)de dimensionamento.

Para obter mais informações, consulte:

- Blog do [Microsoft Azure Media Services SDK para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- Os [exemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) a seguir ajudar você a começar rapidamente:
    - **vodworkflow_aes.PHP**: Este é um arquivo PHP que mostra como usar a criptografia dinâmico AES de 128 e serviço de entrega de chave. Ele se baseia na amostra .NET explicada [neste](media-services-protect-with-aes128.md) artigo.
    - **vodworkflow_aes.PHP**: Este é um arquivo PHP que mostra como usar a criptografia dinâmico PlayReady e serviço de entrega de licença. Ele se baseia na amostra .NET explicada [neste](media-services-protect-with-drm.md) artigo.
    - **scale_encoding_units.PHP**: Este é um arquivo PHP que mostra como dimensionar codificação unidade reservada.


##<a id="nov_changes_15"></a>Lançamento de novembro de 2015

Agora, os serviços de mídia do Azure oferece serviço de entrega de licença do Google Widevine na nuvem. Para obter mais detalhes, consulte [Este blog de lançamento](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Consulte também [deste tutorial](media-services-protect-with-drm.md) e [GitHub repositório](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Observe que Widevine serviços de entrega de licença fornecidos pelo serviços de mídia do Azure está no modo de visualização. Para obter mais informações, consulte [Este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Lançamento de outubro de 2015

Serviços de mídia Azure (AMS) agora está ao vivo nas seguintes data centers: Sul Brasil, Índia Oeste, Índia Sul e Índia Central. Agora você pode usar o Portal de clássico do Azure para [criar contas de serviço de mídia](media-services-portal-create-account.md) e realizar várias tarefas descritas [aqui](https://azure.microsoft.com/documentation/services/media-services/). No entanto, o Live codificação não está habilitado nesses centros de dados. Além disso, nem todos os tipos de codificação reservadas unidades estão disponíveis nesses centros de dados.

- Brasil Sul: Apenas padrão e unidades reservadas codificação básicas estão disponíveis
- Índia Oeste, Índia Sul e Índia Central: somente codificação reservadas unidades básicas estão disponíveis


##<a id="september_changes_15"></a>Lançamento de setembro de 2015 

- AMS agora oferece a capacidade de proteger vídeo sob demanda (VOD) e fluxos ao vivo com a tecnologia de DRM Modular Widevine. Você pode usar os seguintes parceiros de serviços de entrega para ajudá-lo a oferecer Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, consulte [Este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Você pode usar o [SDK do .NET AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou API REST para configurar seu AssetDeliveryConfiguration para usar Widevine.  

- AMS adicionou suporte para vídeos Apple ProRes. Agora você pode carregar arquivos de vídeos de origem do QuickTime que usam Apple ProRes ou outros codecs. Para obter mais informações, consulte [Este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Agora você pode usar uma mídia codificador padrão para fazer a extração do arquivo morto subtipo de recorte e ao vivo. Para obter mais informações, consulte [Este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- As seguintes atualizações de filtragem foram feitas: 

    - Agora você pode usar o formato de Apple HTTP Live Streaming (HLS) com filtro somente de áudio. Essa atualização permite que você remova controlar somente de áudio, especificando (somente áudio = false) na URL.
    - Ao definir filtros para seus ativos, agora você tem a capacidade de combinar vários filtros (até 3) em uma única URL.

    Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS agora suporta quadros eu em HLS v4. Suporte de quadro I otimiza operações de avanço e retroceder. Por padrão, todas as saídas de v4 HLS incluem reprodução de quadro I (EXT-X-I-FRAME-STREAM-INF).
 
    Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>Lançamento de agosto de 2015

- Azure Media Services SDK para Java V0.8.0 lançamento e novos exemplos agora estão disponíveis. Para obter mais informações, consulte:

    - [Postagem de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Repositório de amostras de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Atualização de Media Player Azure com suporte a vários áudio fluxo. Para obter mais informações, consulte:
    - [Postagem de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Lançamento de julho de 2015

- Anunciando a disponibilidade geral do padrão de codificador de mídia. Para obter mais informações, consulte [esta postagem de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Mídia codificador padrão usa predefinições descritas [nesta](http://go.microsoft.com/fwlink/?LinkId=618336) seção. Observe que, quando usando um valor predefinido para 4k codifica, você deve obter o tipo de unidade **Premium** reservado. Para obter mais informações, consulte [como codificação de escala](media-services-scale-media-processing-overview.md).
- Visualização legendas em tempo real com serviços de mídia do Azure e o Player. Para obter mais informações, consulte [esta postagem de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>.NET SDK atualizações de serviços de mídia

Azure Media Services .NET SDK agora é versão 3.4.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

- Implementado suporte para arquivamento dinâmico. Observe que você não é possível baixar um ativo que contém um arquivo morto ao vivo.
- Suporte implementado filtros dinâmicos.
- Funcionalidade implementada que permite aos usuários manter o contêiner de armazenamento ao excluir ativo.
- Correções de bugs relacionadas a repetir políticas em canais.
- Habilitado para **fluxo de trabalho do Media codificador Premium**.

##<a id="june_changes_15"></a>Lançamento de junho de 2015

###<a name="media-services-net-sdk-updates"></a>.NET SDK atualizações de serviços de mídia

Azure Media Services .NET SDK agora é versão 3.3.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

- suporte para especificação de OpenId conectar descoberta,
- suporte para lidar com sobreposição de chaves no lado do provedor de identidade. 

Se você estiver usando um provedor de identidade que expõe OpenID conectar o documento de descoberta (como os seguintes provedores: Active Directory do Azure, Google, Salesforce), você pode instruir o serviços de mídia do Azure para obter chaves de assinatura para especificação de descoberta de conexão de validação de token JWT de OpenID. 

Para obter mais informações, consulte [Usando Json Web chaves de OpenID conectar a especificação de descoberta de trabalhar com autenticação de token de JWT nos serviços de mídia do Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Lançamento de maio de 2015

Anunciando novos recursos a seguir:

- [Uma visualização do Live codificação com os serviços de mídia](media-services-manage-live-encoder-enabled-channels.md)
- [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
- [Uma visualização do processador de mídia Hyperlapse de mídia do Azure](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Lançamento de abril de 2015

 ###<a name="general-media-services-updates"></a>Atualizações de serviços de mídia de geral

- [Anunciando o Player de mídia do Microsoft Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Começando com mídia serviços REST 2.10, canais que estão configurados para inclusão de um protocolo RTMP, são criados com o principal e secundário inclusão URLs. Para obter mais informações, consulte [configurações de inclusão de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Atualizações de mídia indexador Azure
- Suporte para o idioma espanhol
- Novo formato de xml de configuração

Para obter mais informações, consulte [Este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>.NET SDK atualizações de serviços de mídia

Azure Media Services .NET SDK agora é versão 3.2.0.0.

Estes são alguns do cliente opostas atualizações:

- **Alteração significativa**: alterado **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** para ser de um tipo de cadeia de caracteres.
- Políticas de repetição de atualizações relacionadas à criação de personalizado.
- Correções de bugs relacionadas a upload/download de arquivos.
- A classe de **MediaServicesCredentials** agora aceita ponto de extremidade de controle de acesso primário e secundário para autenticar.



##<a id="march_changes_15"></a>Lançamento de março de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de mídia de geral

- Serviços de mídia agora fornece integração de CDN do Azure. Para dar suporte a integração, a propriedade **CdnEnabled** foi adicionada ao **StreamingEndpoint**.  **CdnEnabled** pode ser usado com APIs REST começando com a versão 2,9 (para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** pode ser usado com .NET SDK começando com a versão 3.1.0.2 (para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Comunicado de **fluxo de trabalho do Media codificador Premium**. Para obter mais informações, consulte [Introdução ao Premium codificação nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Lançamento de fevereiro de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de mídia de geral

API de REST de serviços de mídia agora é versão 2,9. Começando com esta versão, você pode habilitar a integração do Azure CDN com streaming pontos de extremidade. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Lançamento de janeiro de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de mídia de geral

Anúncio da disponibilidade geral (GA) de proteção de conteúdo com criptografia dinâmico. Para obter mais informações, consulte [Serviços de mídia do Azure aprimora a segurança streaming com a tecnologia de disponibilidade geral de DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>.NET SDK atualizações de serviços de mídia

Azure Media Services .NET SDK agora é versão 3.1.0.1.

Esta versão marcado o construtor de Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate padrão como obsoleto. O construtor new leva TokenType como um argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Lançamento de dezembro de 2014

###<a name="general-media-services-updates"></a>Atualizações de serviços de mídia de geral

- Algumas atualizações e novos recursos foram adicionados ao processador de mídia do Azure indexador. Para obter mais informações, consulte [Notas de versão de indexador de mídia do Azure 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Adicionado a uma nova API REST que permite que você atualize a codificação reservado unidades: [EncodingReservedUnitType com restante](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS adicionados suporte para o serviço de entrega de chave.
- Melhorias de desempenho de opções de diretivas de autorização de consulta foram feitas.
- No Centro de dados da China, a [URL de entrega de chave](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) é agora por cliente (assim como em outros centros de dados).
- Duração de destino de automático HLS adicionada. Ao fazer streaming ao vivo, HLS é sempre empacotado dinamicamente. Por padrão, os serviços de mídia calcula automaticamente HLS segmento embalagem proporção (FragmentsPerSegment) com base no intervalo de quadro-chave (KeyFrameInterval), também conhecido como grupo de imagens – GOP, que é recebido do codificador ao vivo. Para obter mais informações, consulte [Trabalhando com Live de Streaming do Azure Media Services].
 
###<a name="media-services-net-sdk-updates"></a>.NET SDK atualizações de serviços de mídia

- [Azure Media Services.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) agora é versão 3.1.0.0.
- Atualizado a dependência .net SDK para .NET Framework de 4,5.
- Adicionar uma nova API que permite que você atualize codificação unidades reservadas. Para obter mais informações, consulte [Atualizando tipo de unidade reservadas e crescentes RUs codificação usando .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Adicionado JWT (JSON Web Token) suporte para autenticação de token. Para obter mais informações, consulte [autenticação de token de JWT nos serviços de mídia do Azure e criptografia dinâmico](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Adicionado relativas compensações para BeginDate e ExpirationDate no modelo de licença do PlayReady.


##<a id="november_changes_14"></a>Lançamento de novembro de 2014

- Serviços de mídia agora permite a inclusão de um conteúdo ao vivo suave Streaming (FMP4) sobre uma conexão SSL. Para inclusão sobre SSL, certifique-se de atualizar a URL de recebimento para HTTPS.  Para obter mais informações sobre o live streaming, consulte [Trabalhando com Live de Streaming do Azure Media Services].
- Observe que no momento, você não pode incluir um fluxo ao vivo RTMP ao longo de uma conexão SSL.
- Você também pode transmitir seu conteúdo em uma conexão SSL. Para fazer isso, certifique-se de que URLs streaming iniciar com HTTPS.
- Observe que você só pode transmitir sobre SSL se o ponto de extremidade streaming do qual você distribuir o conteúdo foi criado após 10 de setembro de 2014. Se suas URLs streaming se baseiam os pontos de extremidade streaming criados após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). Streaming URLs que contêm "origin.mediaservices.windows.net" (o antigo formato) não suportam SSL. Se a URL for no formato antigo e você deseja ser capaz de transmitir em SSL, [criar uma nova empresa streaming](media-services-portal-manage-streaming-endpoints.md). Use URLs criados com base no ponto de extremidade streaming novo para transmitir seu conteúdo em SSL.

##<a id="october_changes_14"></a>Lançamento de outubro de 2014

### <a id="new_encoder_release"></a>Lançamento de codificador de serviços de mídia

Anunciando a nova versão do codificador de mídia do Media Services Azure. Com as últimas codificador de mídia do Azure somente cobrado para GB de saída, mas caso contrário, o codificador novo é recurso compatível com o codificador anterior. Para saber mais [Detalhes de preços de serviços de mídia]).

### <a id="oct_sdk"></a>Serviços de mídia SDK .NET 

SDK de serviços de mídia para extensões .NET agora é versão 2.0.0.3.

SDK de serviços de mídia para .NET agora é versão 3.0.0.8.

As seguintes alterações foram feitas:

- Refatoração em classes de política de repetição.
- Adicionando cadeia de caracteres de agente de usuário para cabeçalhos de solicitação de http.
- Adicionando nuget etapa de compilação de restauração.
- Corrigindo testes de cenário para usar x509 certificado do repositório.
- Validando configurações ao atualizar o canal e streaming final.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório GitHub para exemplos de serviços de mídia do host

Exemplos estão localizados no [repositório de GitHub de amostras de serviços de mídia do Azure](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Lançamento de setembro de 2014

Metadados do resto de serviços de mídia agora são versão 2.7. Para obter mais informações sobre as últimas atualizações do resto, consulte [Referência API do restante dos serviços de mídia do Azure].

SDK de serviços de mídia para .NET é agora versão 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Alterações significativas

* **Origem** foi renomeado para [StreamingEndpoint].
* Uma alteração no comportamento padrão ao usar o **Portal de clássico do Azure** para codificar e publicar arquivos MP4.

Anteriormente, quando usando o Portal de clássico do Azure para publicar um ativo de vídeo de MP4 uma URL de SAS de arquivo único seria criado (SAS URLs permitem que você baixe o vídeo de um armazenamento de blob). Atualmente, quando você usa o Portal de clássico do Azure para codificar e publicar um ativo de vídeo MP4 arquivo único, a URL gerada aponta para um serviços de mídia do Azure streaming ponto de extremidade.  Essa alteração não afeta MP4 vídeos carregados para serviços de mídia diretamente e publicados sem sendo codificados por serviços de mídia do Azure.

Atualmente, você tem duas opções a seguir para resolver o problema.

* Habilitar o streaming de unidades e usar embalagem dinâmica para transmitir ativo. mp4 como uma apresentação de streaming suave.

* Crie uma url SAS para baixar (ou reproduzir cada vez) o. mp4. Para obter mais informações sobre como criar um localizador SAS, consulte [Oferecer conteúdo].


### <a id="sept_14_GA_changes"></a>Novos recursos/cenários que fazem parte da versão de lançamento

* **Processador de mídia do indexador**. Para obter mais informações, consulte [a indexação de arquivos de mídia com indexador de mídia do Azure].

* A entidade de [StreamingEndpoint] agora permite adicionar nomes de domínio personalizado (host).

    Um nome de domínio personalizado a ser usado como o nome de ponto de extremidade de fluxo de serviços de mídia, você precisa adicionar nomes de host personalizado ao seu ponto de extremidade de streaming. Use o APIs de REST de serviços de mídia ou .NET SDK para adicionar nomes de host personalizado.
    
    Aplica considerando o seguinte:
    
    * Você deve ter a propriedade do nome de domínio personalizado.
    
    * A propriedade do nome de domínio deve ser validada pelos serviços de mídia do Azure. Para validar o domínio, crie um CName que mapeia <MediaServicesAccountId>.<parent domain> para verifydns. < zona de dns serviços de mídia >. 
    
    * Você deve criar outro CName que mapeia o nome de host personalizado (por exemplo, sports.contoso.com) para o nome de host de seu StreamingEndpont serviços de mídia (por exemplo, amstest.streaming.mediaservices.windows.net).


    Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Novos recursos/cenários que fazem parte da versão de visualização pública

* Visualização dinâmica de Streaming. Para obter mais informações, consulte [Trabalhando com Live de Streaming do Azure Media Services].

* Serviço de entrega de chave. Para obter mais informações, consulte [usando criptografia dinâmico AES de 128 e serviço de entrega de chave].

* Criptografia AES de dinâmico. Para obter mais informações, consulte [usando criptografia dinâmico AES de 128 e serviço de entrega de chave].

* Serviço de entrega de licença PlayReady. Para obter mais informações, consulte [usando criptografia dinâmico do PlayReady e serviço de entrega de licença].

* Criptografia de PlayReady dinâmico. Para obter mais informações, consulte [usando criptografia dinâmico do PlayReady e serviço de entrega de licença].

* Modelo de licença PlayReady de serviços de mídia. Para obter mais informações, consulte [Visão geral do modelo de licença de PlayReady de serviços de mídia].

* Streaming armazenamento criptografado ativos. Para obter mais informações, consulte [Criptografados conteúdo de armazenamento de Streaming].

##<a id="august_changes_14"></a>Lançamento de agosto de 2014

Quando você codificar um ativo, um ativo de saída é gerado após a conclusão do trabalho de codificação. Até nesta versão, o codificador de serviços de mídia do Azure produzidas metadados sobre ativos de saída. Começando com esta versão do codificador também produz metadados sobre ativos de entrada. Para obter mais informações, consulte os tópicos de [Metadados de entrada] e [Saída] .


##<a id="july_changes_14"></a>Lançamento de julho de 2014

As seguintes correções de bugs foram feitas para o Gerenciador de serviços de mídia do Azure e Criptografador:

* Somente áudio é reproduzido quando transmuxing um ativo de arquivamento dinâmico para Live Streaming HTTP – isso foi resolvido e agora o áudio e vídeo são executados.

* Quando empacotamento um ativo Live Streaming HTTP e a criptografia de envelope AES de 128 bits, os fluxos de empacotado não reproduzir em dispositivos Android – esse bug foi corrigido e o fluxo de pacote reproduz em dispositivos Android que oferecem suporte ao vivo Streaming HTTP.

##<a id="may_changes_14"></a>Lançamento de maio de 2014

### <a id="may_14_changes"></a>Atualizações de serviços de mídia de geral

Agora você pode usar [Embalagem dinâmico] para fluxo Streaming Live HTTP (HLS) v3. Para transmitir HLS v3, adicione o seguinte formato para o caminho de localizador de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte [Blog de Nick Drouin].

Embalagem dinâmica agora também suporta oferecer HLS (v3 e v4) criptografados com PlayReady com base em Streaming suave estática criptografados com PlayReady. Para obter informações sobre como criptografar Streaming suave com PlayReady, consulte [proteção fluxo suave com PlayReady].

### <a name="may_14_donnet_changes"></a>.NET SDK atualizações de serviços de mídia

As seguintes melhorias estão incluídas na versão Media Services .NET SDK 3.0.0.5:

* Melhor velocidade e resiliência para upload/download ativos de mídia.

* Melhorias na manipulação de exceção de lógica e transitório de repetição: 

    * Lógica de detecção e repetir erro temporárias foram melhorada para exceções que são causadas por consultando, salvar as alterações, upload ou download de arquivos. 
    
    * Ao obter exceções da Web (por exemplo, durante uma solicitação de token de ACS), você observará que erros fatais estão falhando com mais rapidez agora.

Para obter mais informações, consulte [Repetir lógica no SDK do serviços de mídia para .NET].

##<a id="april_changes_14"></a>Lançamento de codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Atualizações de codificador de serviços de mídia

* Adicionado suporte para a inclusão de arquivos AVI criados usando o editor de grama vale EDIUS não linear, onde o vídeo está levemente compactado usando codec de grama vale matriz/HQX. Para obter mais informações, consulte [Grama vale anuncia EDIUS 7 Streaming por meio de nuvem].

* Adicionado suporte para especificar a convenção de nomenclatura para os arquivos produzidos pelo codificador de mídia. Para obter mais informações, consulte [Controlar nomes de saída codificador de serviço de mídia].

* Adicionado suporte para sobreposições de vídeo e/ou áudio. Para obter mais informações, consulte [Criando sobreposições].

* Adicionado suporte para unir vários segmentos de vídeo. Para obter mais informações, consulte [Imagens segmentos de vídeo].

* Corrigido um bug relacionado ao transcodificação de MP4s onde o áudio tenha sido codificado com áudio MPEG-1 layer 3 (também conhecidos como MP3).


##<a id="jan_feb_changes_14"></a>Janeiro/fevereiro de 2014 versões

### <a name="jan_fab_14_donnet_changes"></a>.NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3 de serviços de mídia do Azure

As alterações na 3.0.0.1 e 3.0.0.2 incluem:

* Fixos problemas relacionados ao uso de consultas LINQ com instruções OrderBy.

* Soluções de teste de divisão no [GitHub] em testes de unidade e baseados em cenários.

Para obter mais detalhes sobre as alterações, consulte: [lançamentos de Azure Media Services .NET SDK 3.0.0.1 e 3.0.0.2].

As seguintes alterações foram feitas no 3.0.0.3:

* Dependências de armazenamento do Azure atualizado para usar a versão 3.0.3.0. 

* Corrigi o problema de compatibilidade com versões anteriores para 3.0. *.* versões. 


##<a id="december_changes_13"></a>Lançamento de dezembro de 2013

### <a name="dec_13_donnet_changes"></a>.NET SDK 3.0.0.0 de serviços de mídia do Azure

>[AZURE.NOTE] versões de 3.0.x.x não são compatíveis com versões de 2.4.x.x.

A versão mais recente do Media Services SDK agora é 3.0.0.0. Você pode baixar o pacote mais recente do Nuget ou obter os bits de [GitHub].

Começando com o SDK de serviços de mídia versão 3.0.0.0, você pode reutilizar os tokens do [Azure Active Directory Access Control Service (ACS)] . Para obter mais informações, consulte a seção "Reutilizando Tokens de serviço de controle de acesso" no tópico [Conectando-se aos serviços de mídia com o SDK de serviços de mídia para .NET] .

### <a name="dec_13_donnet_ext_changes"></a>.NET SDK extensões 2.0.0.0 de serviços de mídia do Azure

As extensões de SDK do Azure mídia serviços .NET é um conjunto de funções de auxiliar que irá simplificar seu código e facilitar a desenvolver com os serviços de mídia do Azure e métodos de extensão. Você pode obter os bits mais recentes do [Azure mídia serviços .NET SDK extensões].

##<a id="november_changes_13"></a>Lançamento de novembro de 2013

### <a name="nov_13_donnet_changes"></a>.NET SDK alterações de serviços de mídia do Azure

Começando com esta versão, o SDK de serviços de mídia para .NET manipula erros de falhas temporárias que podem ocorrer ao fazer chamadas para a camada de API de REST de serviços de mídia.

##<a id="august_changes_13"></a>Lançamento de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell de serviços de mídia incluído nas ferramentas de Sdk do Azure

Os seguintes cmdlets do PowerShell de serviços de mídia agora estão incluídos em [Ferramentas de sdk do azure].

* Get-AzureMediaServices 

    Por exemplo, `Get-AzureMediaServicesAccount`.

* Novo AzureMediaServicesAccount 

    Por exemplo, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Novo AzureMediaServicesKey 

    Por exemplo, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remover AzureMediaServicesAccount 

    Por exemplo, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Lançamento de junho de 2013

### <a name="june_13_general_changes"></a>Alterações de serviços de mídia do Azure

As alterações mencionadas nesta seção são atualizações incluídas nas edições de serviços de mídia de junho de 2013.

* Capacidade de link várias contas de armazenamento para uma conta de serviço de mídia. 

    StorageAccount
    
    Asset.StorageAccountName e Asset.StorageAccount

* Capacidade de atualizar Job.Priority. 

* Notificação relacionados entidades e propriedades: 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Trabalho

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do Azure SDK do .NET de serviços de mídia

As seguintes alterações serão incluídas em junho de 2013 Media Services SDK versões. O SDK mais recente de serviços de mídia está disponível no GitHub.

* Começando com a versão 2.3.0.0, o SDK de serviços de mídia oferece suporte à vinculação armazenamento várias contas para uma conta de serviços de mídia. As seguintes APIs suportam este recurso:
    
    O tipo de IStorageAccount.
    
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    A propriedade StorageAccount.
    
    A propriedade StorageAccountName.
    
    Para obter mais informações, consulte [Gerenciar ativos de serviços de mídia em várias contas de armazenamento].

* Notificação de APIs relacionadas. Começando com a versão 2.2.0.0 a capacidade de ouvir notificações de armazenamento de fila do Azure. Para mais informações, consulte [Manipulação de notificações de trabalho de serviços de mídia].
    
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependência no cliente armazenamento do Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependência de OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Lançamento de dezembro de 2012

### <a name="dec_12_dotnet_changes"></a>Alterações do Azure SDK do .NET de serviços de mídia

* IntelliSense: Adicionado ausente documentação do Intellisense para muitos tipos.

* Microsoft.Practices.TransientFaultHandling.Core: Corrigido um problema onde o SDK ainda tinha uma dependência a uma versão antiga desse assembly. O SDK agora referencia versão 5.1.1209.1 deste assembly.

Correções de problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count: Essa contagem é agora corretamente relatada em novas interfaces IAsset depois que todos os localizadores forem excluídos.

* IAssetFile.ContentFileSize: Este valor agora está corretamente definido após um carregamento por IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: Esta propriedade agora pode ser definida ao criar um arquivo de ativo. Era anteriormente somente leitura.

* IAssetFile.Upload(filepath): Corrigido um problema onde esse método de carregamento síncrono estava gerando o seguinte erro ao carregar vários arquivos ao ativo. Erro: "Falha ao autenticar a solicitação de servidor. Verifique se que o valor de cabeçalho de autorização é formado corretamente, incluindo a assinatura."

* IAssetFile.UploadAsync: Corrigido um problema onde não mais do que 5 arquivos podem ser carregados simultaneamente.

* IAssetFile.UploadProgressChanged: Este evento agora é fornecido pelo SDK.

* IAssetFile.DownloadAsync (cadeia, BlobTransferClient, ILocator, CancellationToken): essa sobrecarga de método agora é fornecida.

* IAssetFile.DownloadAsync: Corrigido um problema onde não mais do que 5 arquivos podem ser baixados simultaneamente.

* IAssetFile.Delete(): Corrigido um problema onde excluir chamada pode lançar uma exceção se nenhum arquivo foi carregado para o IAssetFile.

* Tarefas: Corrigido um problema onde encadeamento um "MP4 a tarefa fluxos suave" com uma "PlayReady proteção tarefa" usando um modelo de trabalho não criaria quaisquer tarefas todo.

* EncryptionUtils.GetCertificateFromStore(): Este método já não gera uma exceção de referência nula devido a um falhas está encontrando o certificado com base em problemas de configuração de certificado.

##<a id="november_changes_12"></a>Lançamento de novembro de 2012

As alterações mencionadas nesta seção foram atualizações incluídas na (versão 2.0.0.0) de novembro de 2012 SDK. Essas alterações podem exigir qualquer código escrito para a visualização de junho de 2012 SDK liberar para ser modificada ou reescrito.

* Ativos
    
    IAsset.Create(assetName) é a função de criação de ativo somente. IAsset.Create já não carrega os arquivos como parte da chamada do método. Use IAssetFile para carregamento.
    
    O método de IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK dos serviços. Qualquer código que depende este valor deve ser escrito novamente.

* FileInfo

    Essa classe foi removida e substituída pelo IAssetFile.

    IAssetFiles

    IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-lo, uma instância do objeto IAssetFiles, seguido por um carregamento de arquivo usando o SDK dos serviços de mídia ou o SDK de armazenamento do Azure. A seguinte sobrecarga de IAssetFile.Upload pode ser usada:

    * IAssetFile.Upload(filePath): Um método síncrono que bloqueia o segmento e é recomendado somente quando o upload de um único arquivo.
    
    * IAssetFile.UploadAsync (caminho do arquivo, blobTransferClient, locator, cancellationToken): um método assíncrono. Este é o mecanismo de carregamento preferido. 

    Bug conhecido: usar o cancellationToken realmente cancelará o carregamento; No entanto, o estado de cancelamento das tarefas pode ser qualquer um dos vários estados. Corretamente, você deve capturar e manipular exceções.

* Locators
    
    As versões específicas de origem foram removidas. O contexto de SAS específicas. Locators.CreateSasLocator (ativo, accessPolicy) será marcado preteridos ou removida por GA. Consulte a seção Locators em nova funcionalidade para o comportamento atualizado.


##<a id="june_changes_12"></a>Versão de visualização de junho de 2012

A seguinte funcionalidade era nova na versão de novembro do SDK.

* Excluindo entidades

    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objetos agora são excluídos no nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão na coleção, que é cloudMediaContext.ObjCollection.Delete(objInstance).

* Locators

    Locators agora devem ser criados usando o método CreateLocator e usam os valores de enumeração LocatorType.SAS ou LocatorType.OnDemandOrigin como um argumento para o tipo de localizador específico que você deseja criar.

    Novas propriedades foram adicionadas ao Locators para tornar mais fácil obter utilizáveis URIs para o seu conteúdo. Essa reformulação da Locators foi criada para fornecer mais flexibilidade para futura extensibilidade de terceiros e aumentar a facilidade de uso para aplicativos de cliente de mídia.

* Suporte ao método assíncrona

    Suporte assíncrono foi adicionado a todos os métodos.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum do MSDN de serviços de mídia do Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência da API REST de serviços de mídia do Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Detalhes de preços de serviços de mídia]: http://azure.microsoft.com/pricing/details/media-services/
[Entrada de metadados]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Fornecimento de conteúdo]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[A indexação de arquivos de mídia com indexador de mídia do Microsoft Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabalhar com os serviços de mídia do Microsoft Azure Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Usando criptografia dinâmico AES de 128 e o serviço de entrega de chave]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Usando criptografia PlayReady dinâmico e o serviço de entrega de licença]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Visão geral do modelo de licença PlayReady de serviços de mídia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Armazenamento de streaming criptografados conteúdo]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Embalagem dinâmica]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protegendo fluxo suave com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Repetir lógica nos SDK dos serviços de mídia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grama vale anuncia EDIUS 7 Streaming através da nuvem]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Nomes de arquivo de saída de codificador de serviço de controle de mídia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Criação de sobreposições]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Alinhando segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versões do Azure Media Services .NET SDK 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Serviço de controle de acesso do Active Directory do Azure (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Conectar-se aos serviços de mídia com os serviços de mídia SDK para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[.NET SDK extensões de serviços de mídia do Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[ferramentas de sdk do Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gerenciando mídia serviços ativos em várias contas de armazenamento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Notificações de trabalho de serviços de manipulação de mídia]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
