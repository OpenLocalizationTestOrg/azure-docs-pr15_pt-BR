<properties 
    pageTitle="Formatos padrão de codificador de mídia e codecs" 
    description="Este tópico fornece uma visão geral dos formatos padrão de codificador de mídia e codecs." 
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
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Formatos padrão de codificador de mídia e de codecs


Este documento contém uma lista de importação mais comuns e formatos de arquivo de exportação que você pode usar com o codificador de mídia padrão.


##<a name="input-containerfile-formats"></a>Formatos de arquivo/contêiner de entrada

Formatos de arquivo (extensões de arquivo)|Com suporte
---|---|---|---
FLV (com codecs h. 264 e AAC) (. FLV)          |Sim 
MXF (.mxf)                  |Sim 
GXF (.gxf)                  |Sim 
MPEG2-PS, MPEG2-TS, 3GP (.ts,. PS, .3gp, .3gpp,. mpg)   |Sim 
Vídeo do Windows Media (WMV) / ASF (. wmv,. asf) |Sim 
AVI (descompactado 8 bits/10 bits) (. avi)|Sim 
MP4 (. mp4, .m4a, .m4v) / ISMV (.isma, .ismv)|Sim 
[Recording(DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr-ms) |Sim 
Matroska/WebM (.mkv)        |Sim 
ONDA/WAV (. wav) |Sim 
QuickTime (. mov) |Sim

>[AZURE.NOTE] Acima é uma lista das extensões de arquivo mais comumente encontrados. Padrão de codificador de mídia oferece suporte a muitos outros (por exemplo: .m2ts, .mpeg2video,. qt). Se você tentar codificar um arquivo e você recebe uma mensagem de erro sobre o formato não suportado, forneça comentários [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formatos de áudio em contêineres de entrada 

Padrão de codificador de mídia oferece suporte para os seguintes formatos de áudio em contêineres de entrada de manutenção:

- MXF, GXF e QuickTime arquivos que tiver faixas de áudio com Estéreo Intercalado ou 5.1 amostras

ou

- Arquivos MXF, GXF e QuickTime onde o áudio é executado como faixas PCM separadas, mas o mapeamento de canal (para estéreo ou 5.1) pode ser deduzido a partir os metadados do arquivo

Observe que o suporte para mapeamento de canal explícita/fornecido pelo usuário será fornecido no futuro próximo.


##<a name="input-video-codecs"></a>Codecs de vídeo de entrada

Codecs de vídeo de entrada|Com suporte
---|---|---|---
AVC 8 bits/10 bits, até 4:2:2, incluindo AVCIntra   |4 de 8 bits: 0:2 e 4:2:2 
DNxHD ávido (em MXF)                                 |Sim 
DVCPro/DVCProHD (em MXF)                            |Sim 
Vídeo digital (DV) (em arquivos AVI)                   |Sim
JPEG 2000                                           |Sim 
MPEG-2 (até 422 perfil e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Perfil até 422 
MPEG-1                                              |Sim 
VC-1/WMV9                                           |Sim 
Matriz de Canopus/HQX                                      |Não 
MPEG-4 parte 2                                       |Sim 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sim 
YUV420 descompactados ou mezzanine                   |Sim
Apple ProRes 422                                    |Sim
Apple ProRes 422 LT |Sim
Apple ProRes 422 matriz |Sim
Apple ProRes Proxy|Sim
Apple ProRes 4444 |Sim
Apple ProRes 4444 XQ |Sim



##<a name="input-audio-codecs"></a>Codecs de áudio de entrada

Codecs de áudio de entrada|Com suporte
---|---|---|---
AAC (AAC-LC, HE-AAC e AAC-HEv2; até 5.1)|Sim 
MPEG camada 2|Sim 
MP3 (MPEG-1 áudio camada 3)|Sim 
Áudio do Windows Media|Sim 
WAV/PCM|Sim 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sim 
[Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Sim 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sim 
AMR (taxa de vários adaptativa)|Sim
AES (SMPTE 331 M e 302 M, AES3-2003)        |Não 
Dolby® E                                    |Não 
Dolby® Digital (AC3)                        |Não 
Dolby® Digital Plus (E-AC3)                 |Não 


##<a name="output-formats-and-codecs"></a>Codecs e formatos de saída

A tabela a seguir lista os formatos de arquivo e de codecs suportados para exportação.


Formato de arquivo|Codec de vídeo|Codec de áudio
---|---|---
MP4 <br/><br/>(incluindo contêineres de MP4 multi-taxa de bits) |H. 264 (alta, principal e perfis de linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2 TS |H. 264 (alta, principal e perfis de linha de base)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Codificação de conteúdo sob demanda com os serviços de mídia do Microsoft Azure](media-services-encode-asset.md)

[Como codificar com o codificador de mídia padrão](media-services-dotnet-encode-with-media-encoder-standard.md)
