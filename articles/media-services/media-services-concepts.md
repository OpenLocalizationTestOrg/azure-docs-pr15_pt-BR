<properties 
    pageTitle="Conceitos de serviços de mídia Azure | Microsoft Azure" 
    description="Este tópico fornece uma visão geral dos conceitos de serviços de mídia do Azure" 
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

#<a name="azure-media-services-concepts"></a>Azure conceitos de serviços de mídia 

Este tópico fornece uma visão geral dos conceitos de serviços de mídia mais importantes.

##<a id="assets"></a>Ativos e armazenamento

###<a name="assets"></a>Ativos

Um [ativo](https://msdn.microsoft.com/library/azure/hh974277.aspx) contém arquivos digitais (incluindo vídeo, áudio, imagens, conjuntos de miniaturas, controles de texto e arquivos de legenda codificada) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos serviços de mídia codificação e streaming fluxos de trabalho.

Um ativo é mapeado para um contêiner de blob na conta de armazenamento do Azure e os arquivos em ativo são armazenados como blobs no contêiner.

Ao decidir qual conteúdo de mídia para carregar e armazenar em um ativo, as considerações a seguir se aplicam:

- Um ativo deve conter apenas uma instância única e exclusiva do conteúdo de mídia. Por exemplo, uma única edição de um episódio TV, filme ou anúncio.
- Um ativo não deve conter várias representações ou edições de um arquivo audiovisuais. Um exemplo de um uso incorreto de um ativo é tentar armazenar mais de um episódio de TV, anúncio ou vários ângulos de câmera de um único de produção dentro de um ativo. Armazenar várias representações ou edições de um arquivo audiovisuais em um ativo pode resultar em dificuldades enviando trabalhos de codificação, streaming e protegendo a entrega do ativo posteriormente no fluxo de trabalho.  

###<a name="asset-file"></a>Arquivo de ativos 
Um [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa um arquivo de áudio ou vídeo real que está armazenado em um contêiner de blob. Um arquivo de ativo sempre está associado a um ativo e um ativo pode conter um ou muitos arquivos. A tarefa de codificador de serviços de mídia falhará se um objeto de arquivo ativo não está associado um arquivo digital em um contêiner de blob.

A instância de **AssetFile** e o arquivo de mídia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Você não deve tentar alterar o conteúdo de contêineres de blob geradas pelos serviços de mídia sem usar APIs de serviço de mídia.

###<a name="asset-encryption-options"></a>Opções de criptografia de ativos

Dependendo do tipo de conteúdo que você deseja carregar, armazenar e fornecer, serviços de mídia oferece várias opções de criptografia que você pode escolher.

**Nenhum** Nenhuma criptografia é usada. Este é o valor padrão. Observe que ao usar essa opção seu conteúdo não está protegido em trânsito ou inativos em armazenamento.

Se você planeja fornecer um MP4 usando o download progressivo, use essa opção para carregar seu conteúdo.

**StorageEncrypted** – Use esta opção para criptografar o conteúdo limpar localmente usando criptografia AES 256 e carregue-o para o armazenamento do Azure onde ele está armazenado criptografado no restante. Ativos protegidos com criptografia de armazenamento são automaticamente não criptografados e colocados em um sistema de arquivos criptografados antes de codificação e, opcionalmente, criptografados novamente antes de carregar volta como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte inativos no disco. 

Para entregar um ativo criptografada do armazenamento, você deve configurar política de entrega do ativo para que serviços de mídia Saiba como deseja distribuir o conteúdo. Antes de seu ativo pode ser transmitido, o servidor de streaming remove a criptografia de armazenamento e fluxos seu conteúdo usando a política de entrega especificado (por exemplo, AES, PlayReady ou sem criptografia). 

**CommonEncryptionProtected** - Use essa opção se desejar criptografar (ou carregar já criptografados) conteúdo com criptografia comum ou PlayReady DRM (por exemplo, suave Streaming protegido com PlayReady DRM).

**EnvelopeEncryptionProtected** – Use essa opção se você deseja proteger (ou carregar já protegido) HTTP Live Streaming (HLS) criptografados com avançadas criptografia AES (padrão). Observe que se você estiver carregando HLS já criptografados com AES, ele deve ter sido criptografado pelo Gerenciador de transformação.

###<a name="access-policy"></a>Política de acesso 

Um [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define as permissões (como ler, gravar e lista) e a duração do acesso a um ativo. Você normalmente faria passar um objeto AccessPolicy para um localizador que deve ser usado para acessar os arquivos contidos em um ativo.


###<a name="blob-container"></a>Contêiner de blob

Um contêiner de blob fornece um agrupamento de um conjunto de blobs. Contêineres de blob são usados nos serviços de mídia como ponto de limite para controle de acesso e locators de assinatura de acesso compartilhado (SAS) nos ativos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contêineres de blob. Um contêiner pode armazenar um número ilimitado de blobs.

>[AZURE.NOTE]Você não deve tentar alterar o conteúdo de contêineres de blob geradas pelos serviços de mídia sem usar APIs de serviço de mídia.

###<a id="locators"></a>Locators

[Localizador](https://msdn.microsoft.com/library/azure/hh974308.aspx)s fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Uma política de acesso é usada para definir as permissões e a duração que um cliente tem acesso a um determinado ativo. Locators podem ter de muitas para uma relação com uma política de acesso, por exemplo, que locators diferentes podem fornecer tempos de início diferentes e tipos de conexão para clientes diferentes durante o uso de todas as mesmas permissões e configurações de duração; No entanto, devido a uma restrição de política de acesso compartilhado definida pelos serviços de armazenamento do Azure, você não pode ter mais de cinco locators exclusivos associados a um determinado ativo ao mesmo tempo. 

Serviços de mídia compatível com dois tipos de localizadores: locators OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG traço, HLS ou Streaming suave) ou download progressivo locators SAS URL e a mídia usada para carregar ou baixar arquivos de mídia to\from armazenamento do Azure. 

Observe que a permissão de lista (AccessPermissions.List) não deve ser usada ao criar um localizador de OrDemandOrigin. 

###<a name="storage-account"></a>Conta de armazenamento

Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Uma conta de serviço de mídia pode associar uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total seja em 500TB por conta de armazenamento.  Serviços de mídia fornece as ferramentas de nível de SDK para permitir que você gerencie várias contas de armazenamento e a distribuição dos seus ativos durante o carregamento a essas contas com base em métricas ou distribuição aleatória de balanceamento de carga. Para obter mais informações, consulte Trabalhando com o [Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Trabalhos e tarefas

Um [trabalho](https://msdn.microsoft.com/library/azure/hh974289.aspx) normalmente é usado para processo (por exemplo, índice ou codificar) uma apresentação de áudio/vídeo. Se você estiver processando vários vídeos, crie um trabalho para cada vídeo ser codificado.

Um trabalho contém metadados sobre o processamento para ser executado. Cada trabalho contém um ou mais s de [tarefa](https://msdn.microsoft.com/library/azure/hh974286.aspx)que especificam uma tarefa de processamento atômica, seus ativos de entrada, saída ativos, um processador de mídia e suas configurações associadas. Tarefas em um trabalho podem ser encadeadas, onde o ativo de saída de uma tarefa é dado como o ativo de entrada para a próxima tarefa. Dessa forma um trabalho pode conter todo o processamento necessário para uma apresentação de mídia.

##<a id="encoding"></a>Codificação

Serviços de mídia do Azure fornece várias opções para a codificação de mídia na nuvem.

Quando começando com os serviços de mídia, é importante compreender a diferença entre os formatos de arquivo e de codecs.
Codecs são o software que implementa os algoritmos de compactação/descompactação enquanto os formatos de arquivo são contêineres que mantenha o vídeo compactado.

Serviços de mídia fornece pacote dinâmico que permite que você distribuir o conteúdo de taxa de bits adaptativa MP4 ou Streaming suave codificado de streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) sem precisar empacotar novamente em esses formatos de fluxo contínuo.

Para tirar proveito da [embalagem dinâmica](media-services-dynamic-packaging-overview.md), você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de taxa de bits adaptativa MP4 arquivos ou taxa de bits adaptativa Streaming suave (as etapas de codificação são demonstrou posteriormente neste tutorial).
- Obtenha pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [como sob demanda Streaming reservadas unidades da escala](media-services-portal-manage-streaming-endpoints.md).

Serviços de mídia suporta o seguinte nas codificadores demanda descritos neste artigo:

- [Padrão de codificador de mídia](media-services-encode-asset.md#media-encoder-standard)
- [Fluxo de trabalho do Media codificador Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores com suporte, consulte [codificadores](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live Streaming

Serviços de mídia do Azure, um canal representa um pipeline de processamento de conteúdo de streaming ao vivo. Um canal recebe fluxos de entrada ao vivo em uma de duas maneiras:

- Um codificador live local envia RTMP multi-taxa de bits ou suave Streaming (fragmentado MP4) para o canal. Você pode usar os seguintes codificadores ao vivo que multi-taxa de bits suave Streaming de saída: primários, Envivio, Cisco. Os seguintes codificadores live saída RTMP: transcodificadores Adobe Flash Live Telestream Wirecast e Tricaster. Os fluxos de ingeridos passam pelos canais sem qualquer processamento posterior. Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.

- Um fluxo de taxa de bits única (em um dos seguintes formatos: RTP (MPEG-TS)), RTMP ou suave Streaming (fragmentado MP4)) é enviada para o canal que estiver ativado para executar a codificação ao vivo com os serviços de mídia. O canal, em seguida, executa a codificação ao vivo de entrada fluxo de taxa de bits única para um fluxo de vídeo multi-taxa de bits (adaptativa). Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.

###<a name="channel"></a>Canal

Em serviços de mídia, s de [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)é responsável pelo processamento de conteúdo de streaming ao vivo. Um canal fornece um ponto de extremidade de entrada (inclusão URL) que você fornece para transcodificador ao vivo. O canal recebe fluxos de entrada ao vivo do transcodificador ao vivo e disponibiliza para streaming por meio de um ou mais StreamingEndpoints. Canais também fornecem um ponto de extremidade preview (visualização URL) que você usa para visualizar e validar seu fluxo antes de processamento posterior e entrega.

Você pode obter a URL de recebimento e a URL de visualização quando você criar o canal. Para obter essas URLs, o canal não tem que estar em estado de iniciado. Quando você estiver pronto para começar a enviar dados de transcodificador ao vivo no canal, o canal deve ser iniciado. Depois que o live transcodificador começarem a inclusão de dados, você pode visualizar seu fluxo.

Cada conta de serviços de mídia pode conter vários canais, vários programas e StreamingEndpoints vários. Dependendo das necessidades de segurança e de largura de banda, serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode retirar de qualquer canal.


###<a name="program"></a>Programa

Um [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) permite controlar a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciar programas. A relação de canal e programa é muito semelhante à mídia tradicional onde um canal tem um fluxo constante de conteúdo e um programa é delimitado para alguns eventos agendados naquele canal.
Você pode especificar o número de horas que você deseja manter o conteúdo registrado para o programa, definindo a propriedade **ArchiveWindowLength** . Esse valor pode ser definido entre o mínimo de 5 minutos a um máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode buscar Voltar no tempo da posição atual do live. Programas podem ser executados sobre o período de tempo especificado, mas o conteúdo que estiver atrás o comprimento da janela continuamente é descartado. Este valor desta propriedade também determina quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo. Para publicar o programa que você deve criar um localizador para o ativo associado. Ter esse localizador permitirá que você criar uma URL de streaming que você pode fornecer aos seus clientes.

Um canal suporta até três programas em execução simultaneamente para poder criar vários arquivos mortos do mesmo fluxo de entrada. Isso permite que você publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas a transmissão apenas últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultaneamente. Um programa está definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa está definido como arquivamento de 10 minutos e este programa é publicado.


Para obter mais informações, consulte:

- [Trabalhando com canais que estão ativados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)
- [Trabalhando com canais que recebem fluxo ao vivo Multi-taxa de bits de codificadores de local](media-services-live-streaming-with-onprem-encoders.md)
- [Cotas e limitações](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Proteger o conteúdo

###<a name="dynamic-encryption"></a>Criptografia dinâmico

Serviços de mídia do Azure permite que você proteja sua mídia do horário deixa seu computador por meio de armazenamento, processamento e entrega. Serviços de mídia permite que você distribuir o conteúdo criptografado dinamicamente com avançadas criptografia AES (padrão) (usando chaves de criptografia de 128 bits) e criptografia comuns (CENC) usando PlayReady e/ou Widevine DRM. Serviços de mídia também fornece um serviço para oferecer chaves AES e PlayReady licenças aos clientes autorizados.

Atualmente, você pode criptografar os seguintes formatos de streaming: HLS, MPEG traço e Streaming suave. Você não pode criptografar HDS streaming format ou progressivo downloads.

Se você quiser para serviços de mídia criptografar um ativo, você precisa associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) a seus ativos e também configurar diretivas de autorização para a chave.

Se você quiser um ativo criptografada do armazenamento de fluxo, você deve configurar política de entrega do ativo para especificar como você deseja entregar seu ativo.

Quando um fluxo é solicitado por um player, serviços de mídia usa a chave especificada para criptografar dinamicamente seu conteúdo usando um envelope (com AES) ou a criptografia comum (com PlayReady ou Widevine). Para descriptografar o fluxo, o player solicitará a chave do serviço de entrega de chave. Para decidir se ou não o usuário está autorizado obter a chave, o serviço avalia as diretivas de autorização que você especificou para a chave.


###<a name="token-restriction"></a>Restrição token

A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir, símbolo restrição ou restrição de IP. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço). Serviços de mídia suporta tokens em simples Web Tokens SWT () e o formato de JSON Web Token (JWT). Serviços de mídia não fornece serviços de Token de segurança. Você pode criar um STS personalizado ou aproveitar Microsoft Azure ACS para tokens de problema. O STS deve ser configurado para criar um token assinado com as declarações especificadas de chave e o problema que você especificou na configuração do token de restrição. O serviço de entrega de chave de serviços de mídia retornará a chave solicitada (ou licença) para o cliente se o token for válido e as declarações na correspondência de token aqueles configurados para a chave (ou a licença).

Ao configurar o token restrito política, especifique a chave primária de verificação, emissor e parâmetros de audiência. A chave primária de verificação contém a chave que o token foi assinado com, emissor é o serviço de símbolo seguro que emite o token. A audiência (às vezes chamada de escopo) descreve o propósito do token ou o recurso o token autoriza acesso a. O serviço de entrega de chave de serviços de mídia valida que esses valores no token coincidir com os valores no modelo.

Para obter mais informações, consulte os seguintes artigos:

[Proteger visão geral do conteúdo](media-services-content-protection-overview.md)
[proteger com AES-128](media-services-protect-with-aes128.md)
[proteger com DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Entregar

###<a id="dynamic_packaging"></a>Embalagem dinâmica

Ao trabalhar com serviços de mídia, é recomendável codificar seus arquivos mezzanine em uma taxa de bits adaptativa MP4 defina e depois converter o conjunto para o formato desejado usando na [Embalagem dinâmico](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Ponto de extremidade de streaming

Um StreamingEndpoint representa um serviço de streaming que pode fornecer conteúdo diretamente um aplicativo de player de cliente, ou para uma rede de entrega de conteúdo (CDN) para distribuição adicional (serviços de mídia do Azure agora fornece a integração do Azure CDN). O fluxo de saída de um serviço de StreamingEndpoint pode ser um fluxo ao vivo ou um vídeo sob demanda ativo na sua conta de serviços de mídia. Além disso, você pode controlar a capacidade do serviço StreamingEndpoint tratar crescentes necessidades de largura de banda ajustando as unidades de escala (também conhecido como streaming unidades). É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. Unidades de escala fornecem capacidade de egresso dedicado que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que inclui atualmente uso dinâmico embalagem.

É recomendável usar a criptografia dinâmico do embalagem dinâmico e/ou. Para usar esses recursos, você deve ter pelo menos uma unidade de streaming para o ponto de extremidade do qual você planeja transmitir. Para obter mais informações, consulte [escala streaming unidades](media-services-portal-manage-streaming-endpoints.md).

Por padrão, você pode ter até 2 streaming pontos de extremidade em sua conta de serviços de mídia. Para solicitar um limite superior, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

Você só será cobrado quando seu StreamingEndpoint estiver em estado de execução.

###<a name="asset-delivery-policy"></a>Política de entrega de ativos

Uma das etapas do fluxo de trabalho de fornecimento de conteúdo de serviços de mídia está configurando [políticas de entrega para ativos ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que você deseja ser transmitido. A política de entrega de ativos informa serviços de mídia como deseja para seu ativo ser entregue: em qual streaming protocolo deve seu ativo ser dinamicamente empacotado (por exemplo, MPEG traço, HLS, Streaming suave ou todos), estando ou não desejar criptografar dinamicamente seu ativo e como (envelope ou criptografia comuns).

Se você tiver um ativo criptografada de armazenamento, antes de seu ativo pode ser transmitido, o servidor de streaming remove a criptografia de armazenamento e fluxos seu conteúdo usando a política de entrega especificado. Por exemplo, para entregar seu ativo criptografado com a chave de criptografia avançada criptografia AES (padrão), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a criptografia de armazenamento e fluxo ativo em Limpar, defina o tipo de política NoDynamicEncryption.

###<a name="progressive-download"></a>Download progressivo

Download progressivo permite que você inicie a reprodução de mídia antes de todo o arquivo foi baixado. Somente cada vez que você pode baixar um arquivo MP4.

Observe que você deve descriptografar ativos criptografados se você quer que eles estejam disponíveis para download progressivo.

Para fornecer aos usuários URLs de download progressivo, você deve primeiro criar um localizador de OnDemandOrigin. Criar o localizador, fornece o caminho base para o ativo. Em seguida, você precisa acrescentar o nome do arquivo MP4. Por exemplo:

http://amstest1.streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>URLs de streaming

Streaming seu conteúdo para clientes. Para fornecer aos usuários streaming URLs, você deve primeiro criar um localizador de OnDemandOrigin. Criar o localizador, lhe o caminho base para o ativo que contém o conteúdo que deseja transmitir. No entanto, para poder transmitir esse conteúdo é necessário modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto streaming, você deve concatenar o valor do caminho do localizador e o manifesto (filename.ism) nome do arquivo. Em seguida, acrescente /Manifest e um formato apropriado (se necessário) para o caminho de localizador.

Você também pode transmitir seu conteúdo em uma conexão SSL. Para fazer isso, certifique-se de que URLs streaming iniciar com HTTPS.

Observe que você só pode transmitir sobre SSL se o ponto de extremidade streaming do qual você distribuir o conteúdo foi criado após 10 de setembro de 2014. Se suas URLs streaming se baseiam os pontos de extremidade streaming criados após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). Streaming URLs que contêm "origin.mediaservices.windows.net" (o antigo formato) não suportam SSL. Se a URL for no formato antigo e você quer ser capaz de transmitir em SSL, crie um novo ponto de extremidade de streaming. Use URLs criados com base no ponto de extremidade streaming novo para transmitir seu conteúdo em SSL.

A lista a seguir descreve diferentes formatos de streaming e fornece exemplos:

- Streaming suave

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest


- MPEG TRAÇO

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=mpd-time-CSF)



- Apple HTTP Live Streaming V4 (HLS)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl)



- Apple HTTP Live Streaming V3 (HLS)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3)

- HDS (para licenciados do Adobe faltava/acesso somente)

{streaming ponto de extremidade dos serviços de mídia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
