<properties 
    pageTitle="Visão geral do Live fluxo usando os serviços de mídia do Azure | Microsoft Azure" 
    description="Este tópico fornece uma visão geral do fluxo ao vivo usando os serviços de mídia do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Visão geral do Live fluxo usando os serviços de mídia do Azure

##<a name="overview"></a>Visão geral

Ao exibir eventos de streaming ao vivo com os serviços de mídia do Azure os seguintes componentes comumente estão envolvidos:

- Uma câmera que é usada para transmitir um evento.
- Um codificador de vídeo ao vivo que converte sinais da câmera para fluxos que são enviados para um serviço de streaming ao vivo.

    Opcionalmente, vários tempo ao vivo sincronizados codificadores. Crítico para determinados eventos ao vivo que disponibilidade muito alta demanda e qualidade da experiência, recomenda-se para adotar codificadores redundantes ativa com a sincronização de tempo para atingir failover perfeito sem perda de dados.
- Um serviço de streaming live que permite que você faça o seguinte:
    
    - inclusão de conteúdo ao vivo usando vários protocolos de streaming live (por exemplo RTMP ou Streaming suave)
    - (opcionalmente) codificar seu fluxo no fluxo de taxa de bits adaptativa
    - visualizar seu fluxo ao vivo,
    - gravar e armazenar o conteúdo ingerido para ser transmitido posterior (vídeo sob demanda)
    - entregar o conteúdo por meio de protocolos comuns de streaming (por exemplo, MPEG traço, suave, HLS, HDS) diretamente aos seus clientes ou para uma rede de entrega de conteúdo (CDN) para distribuição adicional.


**Serviços de mídia do Microsoft Azure** (AMS) fornece a capacidade de inclusão, codificar, visualizar, armazenar e distribuir o conteúdo de streaming ao vivo.

Ao fornecer o seu conteúdo para clientes seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para fazer isso, use codificadores ao vivo para codificar seu fluxo para um fluxo de vídeo multi-taxa de bits (taxa de bits adaptativa).  Para cuidam de streaming em dispositivos diferentes, use serviços de mídia [embalagem dinâmica](media-services-dynamic-packaging-overview.md) dinamicamente empacotar novamente seu fluxo para protocolos diferentes. Serviços de mídia dá suporte a entrega da taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia do Azure, **canais**, **programas**e **StreamingEndpoints** tratar todas as funcionalidades de streaming live incluindo recebimento, formatação, DVR, segurança, escalabilidade e redundância.

Um **canal** representa um pipeline de processamento de conteúdo de streaming ao vivo. Um canal pode receber um live fluxos de entrada das seguintes maneiras:

- Um local ao vivo codificador envia multi-taxa de bits **RTMP** ou **Streaming suave** (fragmentados MP4) para o canal que está configurado para entrega de **passagem** . A entrega de **passagem** é quando os fluxos ingeridos passam por **canal**s sem qualquer processamento posterior. Você pode usar os seguintes codificadores ao vivo que multi-taxa de bits suave Streaming de saída: primários, Envivio, Cisco.  Os seguintes codificadores live saída RTMP: transcodificadores de certificação Adobe Flash Media Live codificador (FMLE), Telestream Wirecast e Tricaster.  Um codificador ao vivo também pode enviar um fluxo de taxa de bits única em um canal que não está habilitado para codificação ao vivo, mas que não é recomendado. Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.

    >[AZURE.NOTE] Usando um método passagem é a maneira mais econômica Live streaming quando você estiver fazendo vários eventos por um longo período de tempo, e você já tiver investido no codificadores local. Consulte detalhes de [preços](/pricing/details/media-services/) .
    
    
- Um codificador live local envia um fluxo de taxa de bits única no canal que estiver ativado para executar a codificação ao vivo com serviços de mídia em um dos seguintes formatos: RTMP ou suave Streaming (fragmentados MP4). RTP (MPEG-TS) também é suportado, desde que você tenha uma conexão dedicada ao Azure data center. Os seguintes codificadores ao vivo com a saída RTMP são conhecidos para trabalhar com canais desse tipo: Telestream Wirecast, FMLE. O canal, em seguida, executa a codificação ao vivo de entrada fluxo de taxa de bits única para um fluxo de vídeo multi-taxa de bits (adaptativa). Quando solicitado, os serviços de mídia oferece o fluxo aos clientes.


Começando com a versão 2.10 de serviços de mídia, quando você cria um canal, você pode especificar qual forma desejada do canal receber o fluxo de entrada e se deseja ou não para o canal executar a codificação ao vivo do seu fluxo. Você tem duas opções:

- **Nenhum** (passagem) – Especifique esse valor, se você planeja usar um codificador ao vivo do local que resultará fluxo de multi-taxa de bits (um fluxo de passagem). Nesse caso, o fluxo de entrada passou a saída sem qualquer codificação. Este é o comportamento de um canal antes da versão 2.10.  

- **Padrão** – escolha que esse valor, se você planeja usar os serviços de mídia para codificar seu fluxo ao vivo de taxa de bits única fluxo de multi-taxa de bits. Este método é mais econômico para dimensionar rapidamente para eventos raros. Lembre-se de que há um impacto de cobrança para codificação ao vivo e você deve lembrar que sair de um canal de codificação ao vivo no estado "Executando" provocará cobranças.  É recomendável que parar seus canais em execução imediatamente após o evento de streaming ao vivo conclusão para evitar encargos de hora extra. 

##<a name="comparison-of-channel-types"></a>Comparação de tipos de canal

Tabela a seguir fornece um guia para comparar os dois tipos de canal com suporte nos serviços de mídia

Recurso|Canal de passagem|Canal padrão
---|---|---
Taxa de bits única entrada é codificada em vários bitrates na nuvem|Não|Sim
Resolução máxima, número de camadas|1080p, 8 camadas, 60 + q/s|720p, 6 camadas, 30 quadros/s
Protocolos de entrada|RTMP, suave Streaming|RTMP, Streaming suave e RTP
Preço|Consulte a [página de preços](/pricing/details/media-services/) e clique na guia "Live vídeo"|Consulte a [página de preços](/pricing/details/media-services/) 
Tempo máximo de execução|24 x 7|8 horas
Suporte para inserir slates|Não|Sim
Suporte para sinalização do ad|Não|Sim
Legendas de CEA 608/708 passagem|Sim|Sim
Capacidade de recuperação de vagas breves no feed de contribuição|Sim|Não (canal começará slating após 6 + segundos sem dados de entrada)
Suporte para GOPs de entrada não-uniforme|Sim|Não – entrada deve ser corrigida 2 s GOPs
Suporte para entrada de taxas de quadros variáveis|Sim|Não – entrada deve ser corrigida taxa de quadro.<br/>Pequenas variações serão toleradas, por exemplo, durante cenas de movimento alta. Mas codificador não é possível cancelar a 10 quadros/s.
Autodesligamento de canais quando entrada feed é perdida|Não|Depois de 12 horas, se há um programa em execução 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhando com canais que recebem fluxo ao vivo multi-taxa de bits de codificadores local (passagem)

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho **passagem** .

![Fluxo de trabalho ao vivo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhando com canais que vários recebimento-fluxo ao vivo de taxa de bits de codificadores locais](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhando com canais que estão habilitados para executar a codificação ao vivo com serviços de mídia do Azure

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho Live Streaming onde um canal estiver ativado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhando com canais que estão habilitados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Descrição de um canal e seus componentes relacionados

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


##<a name="billing-implications"></a>Implicações de cobrança

Um canal começa assim que ele é transições de estado para "Executando" por meio da API de cobrança.  

A tabela a seguir mostra como os estados de canal mapeiam para cobranças estados no portal do API e o Azure. Observe que os estados são ligeiramente diferentes entre a API e UX Portal. Assim que um canal está no estado "Executar" por meio da API ou no estado "Pronto" ou "Streaming" no portal do Azure, cobrança ficará ativa.

Para interromper o canal de cobrança você ainda mais, você precisa parar o canal por meio da API ou no portal do Azure.
Você é responsável pelo interrompendo seus canais quando terminar com o canal. Falha ao parar o canal resultará em cobrança contínua.

>[AZURE.NOTE]Ao trabalhar com canais padrão, AMS gerará automaticamente desligamento da qualquer canal que ainda está em estado "Executando" 12 horas após o feed de entrada é perdido e não existem programas em execução. No entanto, você ainda será cobrado pela vez que o canal estava em estado "Executando".

###<a id="states"></a>Estados de canal e como eles são mapeados para o modo de cobrança 

O estado atual de um canal. Valores possíveis incluem:

- **Interrompido**. Este é o estado inicial do canal após sua criação (a menos que iniciar automaticamente foi selecionado no portal.) Não há cobrança ocorre nesse estado. Nesse estado, as propriedades do canal podem ser atualizadas mas streaming não é permitido.
- **Iniciando**. O canal está sendo iniciado. Não há cobrança ocorre nesse estado. Não há atualizações ou streaming é permitida durante esse estado. Se ocorrer um erro, o canal retornará para o estado de parado.
- **Executando**. O canal é capaz de processar fluxos ao vivo. Agora, ele é cobrança uso. Você deve parar o canal para impedir a cobrança adicional. 
- **Interrompendo**. O canal está sendo interrompido. Não há cobrança ocorre nesse estado temporárias. Não há atualizações ou streaming é permitida durante esse estado.
- **Excluindo**. O canal está sendo excluído. Não há cobrança ocorre nesse estado temporárias. Não há atualizações ou streaming é permitida durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança. 
 
Estado de canal|Indicadores de portal de interface do usuário|Trata-se de cobrança?
---|---|---
Iniciando|Iniciando|Nenhum (estado temporário)
Em execução|Pronto (sem programas em execução)<br/>ou<br/>Streaming (pelo menos um programa em execução)|SIM
Interrompendo|Interrompendo|Nenhum (estado temporário)
Interrompido|Interrompido|Não


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Tópicos relacionados

[Especificação de inclusão de serviços de mídia do Microsoft Azure MP4 fragmentados Live](media-services-fmp4-live-ingest-overview.md)

[Trabalhando com canais que estão ativados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhando com canais que recebem fluxo ao vivo Multi-taxa de bits de codificadores de local](media-services-live-streaming-with-onprem-encoders.md)

[Cotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos de serviços de mídia](media-services-concepts.md)
