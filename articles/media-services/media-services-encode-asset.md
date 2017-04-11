<properties 
    pageTitle="Visão geral e a comparação do Azure em codificadores de mídia de demanda | Microsoft Azure" 
    description="Este tópico fornece uma visão geral e a comparação do Azure em codificadores de mídia de demanda." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Visão geral e a comparação do Azure em codificadores de mídia de propostas

##<a name="encoding-overview"></a>Visão geral de codificação

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

Este artigo fornece uma visão geral de sob demanda codificadores de mídia e fornece links para artigos que fornecem informações mais detalhadas. O tópico também fornece comparação dos codificadores.

Observe que, por padrão cada conta de serviços de mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem que você tenha várias tarefas de codificação executando simultaneamente, um para cada unidade reservada codificação que comprar. Para obter informações, consulte [unidades de codificação de escala](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Padrão de codificador de mídia

###<a name="how-to-use"></a>Como usar

[Como codificar com o codificador de mídia padrão](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formatos

[Codecs e formatos](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Predefinições

Mídia codificador padrão é configurada usando um do codificador predefinições descrito [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadados de entrada e saído

Os metadados de entrada codificadores é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Os metadados de saída codificadores é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Gerar miniaturas

Para obter informações, consulte [como gerar miniaturas utilizando mídia codificador padrão](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Cortar vídeos (recorte)

Para obter informações, consulte [como cortar vídeos usando mídia codificador padrão](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Criar sobreposições

Para obter informações, consulte [como criar sobreposições usando mídia codificador padrão](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Consulte também

[O blog de serviços de mídia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Fluxo de trabalho do Media codificador Premium

###<a name="overview"></a>Visão geral

[Apresentando o Premium codificação no Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Como usar

Fluxo de trabalho do Media codificador Premium é configurado usando fluxos de trabalho complexos. Arquivos de fluxo de trabalho podem ser criados e atualizado usando a ferramenta [Designer de fluxo de trabalho](media-services-workflow-designer.md) .

[Como usar Premium codificação no Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problemas conhecidos

Se o vídeo de entrada não contiver legendas codificadas, a saída ativo ainda terá um arquivo TTML vazio. 


##<a id="compare_encoders"></a>Comparar codificadores

###<a id="billing"></a>Medidor de cobrança usado por cada codificador

Nome do processador de mídia|Preços aplicável|Anotações
---|---|---
**Padrão de codificador de mídia** |CODIFICADOR|Codificação de tarefas será cobrado acordo com o tamanho da saída ativo, em GB, na taxa especificada [aqui][1], na coluna de CODIFICADOR.
**Fluxo de trabalho do Media codificador Premium** |CODIFICADOR PREMIUM|Codificação de tarefas será cobrado acordo com o tamanho da saída ativo, em GB, na taxa especificada [aqui][1], na coluna de CODIFICADOR PREMIUM.


Esta seção compara os recursos de codificação de **Mídia codificador padrão** e de **Fluxo de trabalho do Media codificador Premium**.


###<a name="input-containerfile-formats"></a>Formatos de arquivo/contêiner de entrada

Formatos de arquivo/contêiner de entrada|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
Adobe® Flash® F4V           |Sim|Sim
MXF/SMPTE 377M              |Sim|Sim
GXF                         |Sim|Sim
Fluxos de transporte MPEG-2    |Sim|Sim
Fluxos de programa de MPEG-2      |Sim|Sim
MPEG-4/MP4                  |Sim|Sim
/ ASF do Windows Media           |Sim|Sim
AVI (descompactado 8 bits/10 bits)|Sim|Sim
3GPP/3GPP2                  |Sim|Não
Formato de arquivo Streaming (PIFF 1.3) suave|Sim|Não
[Recording(DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sim|Não
Matroska/WebM               |Sim|Não
QuickTime (. mov) |Sim|Não

###<a name="input-video-codecs"></a>Codecs de vídeo de entrada

Codecs de vídeo de entrada|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
AVC 8 bits/10 bits, até 4:2:2, incluindo AVCIntra   |4 de 8 bits: 0:2 e 4:2:2|Sim
DNxHD ávido (em MXF)                                 |Sim|Sim
DVCPro/DVCProHD (em MXF)                            |Sim|Sim
JPEG2000                                            |Sim|Sim
MPEG-2 (até 422 perfil e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Perfil até 422|Sim
MPEG-1                                              |Sim|Sim
Windows Media Video/VC-1                            |Sim|Sim
Matriz de Canopus/HQX                                      |Não|Não
MPEG-4 parte 2                                       |Sim|Não
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sim|Não
Apple ProRes 422    |Sim|Não
Apple ProRes 422 LT |Sim|Não
Apple ProRes 422 matriz |Sim|Não
Apple ProRes Proxy|Sim|Não
Apple ProRes 4444 |Sim|Não
Apple ProRes 4444 XQ |Sim|Não

###<a name="input-audio-codecs"></a>Codecs de áudio de entrada

Codecs de áudio de entrada|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)        |Não|Sim
Dolby® E                                    |Não|Sim
Dolby® Digital (AC3)                        |Não|Sim
Dolby® Digital Plus (E-AC3)                 |Não|Sim
AAC (AAC-LC, HE-AAC e AAC-HEv2; até 5.1)|Sim|Sim
MPEG camada 2|Sim|Sim
MP3 (MPEG-1 áudio camada 3)|Sim|Sim
Áudio do Windows Media|Sim|Sim
WAV/PCM|Sim|Sim
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim|Não
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sim|Não
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim|Não


###<a name="output-containerfile-formats"></a>Formatos de arquivo/contêiner de saída

Formatos de arquivo/contêiner de saída|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
Adobe® Flash® F4V|Não|Sim
MXF (OP1a, XDCAM e AS02)|Não|Sim
DPP (incluindo AS11)|Não|Sim
GXF|Não|Sim
MPEG-4/MP4|Sim|Sim
MPEG-TS|Sim|Sim
/ ASF do Windows Media|Não|Sim
AVI (descompactado 8 bits/10 bits)|Não|Sim
Formato de arquivo Streaming (PIFF 1.3) suave|Não|Sim

###<a name="output-video-codecs"></a>Codecs de vídeo de saída

Codecs de vídeo de saída|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
AVC (h. 264; 8 bits; até perfil de alta, nível 5.2; 4 K Ultra HD; AVC UE)|Somente 8 bits 4:2:0|Sim
DNxHD ávido (em MXF)|Não|Sim
MPEG-2 (até 422 perfil e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Não|Sim
MPEG-1|Não|Sim
Windows Media Video/VC-1|Não|Sim
Criação de miniatura JPEG|Não|Sim

###<a name="output-audio-codecs"></a>Codecs de áudio de saída

Codecs de áudio de saída|Padrão de codificador de mídia|Fluxo de trabalho do Media codificador Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)|Não|Sim
Dolby® Digital (AC3)|Não|Sim
Dolby® Digital Plus (E-AC3) até 7.1|Não|Sim
AAC (AAC-LC, HE-AAC e AAC-HEv2; até 5.1)|Sim|Sim
MPEG camada 2|Não|Sim
MP3 (MPEG-1 áudio camada 3)|Não|Sim
Áudio do Windows Media|Não|Sim


##<a name="error-codes"></a>Códigos de erro  

A tabela a seguir lista os códigos de erro que podem ser retornados em caso de erro durante a execução de tarefas de codificação.  Para obter detalhes do erro no seu código .NET, use a classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Para obter detalhes do erro em seu código restante, use a API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

ErrorDetail.Code|Causas possíveis para erro
-----|-----------------------
Desconhecido| Erro desconhecido ao executar a tarefa
ErrorDownloadingInputAssetMalformedContent|Categoria de erros que abrange erros de download ativo entrado como nomes de arquivo incorreto, zero arquivos de comprimento, incorretos formatos e assim por diante.
ErrorDownloadingInputAssetServiceFailure|Categoria de erros que aborda problemas no lado do serviço - erros de rede ou armazenamento de exemplo durante o download.
ErrorParsingConfiguration|Categoria de erros de tarefas onde <see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo a configuração não é um sistema válido predefinidos ou contém XML inválido.
ErrorExecutingTaskMalformedContent|Categoria de erros durante a execução da tarefa onde problemas dentro os arquivos de mídia de entrada causam a falha.
ErrorExecutingTaskUnsupportedFormat|Categoria de erros onde o processador de mídia não é possível processar os arquivos fornecidos - formato de mídia não suportado ou não coincidir com a configuração. Por exemplo, tentando gerar uma saída somente de áudio de um ativo com vídeo somente
ErrorProcessingTask|Categoria de outros erros que encontra o processador de mídia durante o processamento da tarefa que não estão relacionadas ao conteúdo.
ErrorUploadingOutputAsset|Categoria de erros ao carregar o ativo de saída
ErrorCancelingTask|Categoria de erros para cobrir falhas ao tentar cancelar a tarefa
TransientError|Categoria de erros para cobrir temporárias problemas (ex. problemas de rede temporários com o armazenamento do Azure)


Para obter ajuda com a equipe de **Serviços de mídia** do, abra um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Artigos relacionados

- [Executar tarefas de codificação avançadas personalizando predefinições padrão de codificador de mídia](media-services-custom-mes-presets-with-dotnet.md)
- [Cotas e limitações](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
