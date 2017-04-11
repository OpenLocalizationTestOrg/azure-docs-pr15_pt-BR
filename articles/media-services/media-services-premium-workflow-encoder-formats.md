<properties 
    pageTitle="Codecs e formatos de mídia codificador Premium fluxo de trabalho | Microsoft Azure" 
    description="Este tópico fornece uma visão geral dos formatos de formatos de fluxo de trabalho de Premium de codificador de mídia e codecs" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Codecs e formatos de mídia codificador Premium fluxo de trabalho


>[AZURE.NOTE]Para perguntas de codificador premium, email mepd no Microsoft.com.
>
>Processador de mídia de fluxo de trabalho do Media codificador Premium abordado neste tópico não está disponível na China. 

Este documento contém uma lista dos codecs que são suportados pela versão de visualização pública do **Fluxo de trabalho do Media codificador Premium** codificador e formatos de arquivo de saída e entrada.

[Formatos de entrada de Worflow de Premium de codificador de mídia e Codecs](#input_formats)

[Formatos de saída de Worflow de Premium de codificador de mídia e Codecs](#output_formats)

**Fluxo de trabalho do Media codificador Premium** oferece suporte a legenda codificada descritas [nesta](#closed_captioning) seção. 


##<a id="input_formats"></a>Fluxo de trabalho do Media codificador Premium Codecs e formatos de entrada

A seção a seguir lista os formatos de arquivo e de codecs compatível com este processador de mídia como entrada.

###<a name="input-containerfile-formats"></a>Formatos de arquivo/contêiner de entrada

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Fluxos de transporte MPEG-2
- Fluxos de programa de MPEG-2
- MPEG-4/MP4
- / ASF do Windows Media
- AVI (descompactado 8 bits/10 bits)

###<a name="input-video-codecs"></a>Codecs de vídeo de entrada

- AVC 8 bits/10 bits, até 4:2:2, incluindo AVCIntra
- DNxHD ávido (em MXF)
- DVCPro/DVCProHD (em MXF)
- JPEG2000
- MPEG-2 (até 422 perfil e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Codecs de áudio de entrada

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, HE-AAC e AAC-HEv2; até 5.1)
- MPEG camada 2
- MP3 (MPEG-1 áudio camada 3)
- Áudio do Windows Media
- WAV/PCM
 
##<a id="output_format"></a>Codecs e formatos de saída de fluxo de trabalho do Media codificador Premium

A seção a seguir lista os formatos de arquivo e de codecs suportados como saída desse processador de mídia.

###<a name="output-containerfile-formats"></a>Formatos de arquivo/contêiner de saída

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM e AS02)
- DPP (incluindo AS11)
- GXF
- MPEG-4/MP4
- / ASF do Windows Media
- AVI (descompactado 8 bits/10 bits)
- Formato de arquivo Streaming (PIFF 1.3) suave
- MPEG-TS 


###<a name="output-video-codecs"></a>Codecs de vídeo de saída

- AVC (h. 264; 8 bits; até perfil de alta, nível 5.2; 4 K Ultra HD; AVC UE)
- DNxHD ávido (em MXF)
- DVCPro/DVCProHD (em MXF)
- MPEG-2 (até 422 perfil e de alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1
- Criação de miniatura JPEG

###<a name="output-audio-codecs"></a>Codecs de áudio de saída

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) até 7.1
- AAC (AAC-LC, HE-AAC e AAC-HEv2; até 5.1)
- MPEG camada 2
- MP3 (MPEG-1 áudio camada 3)
- Áudio do Windows Media

##<a id="closed_captioning"></a>Suporte a legenda codificada

Em inclusão, **Mídia codificador Premium Workflow** dá suporte a:

1. Arquivos SCC
1. Arquivos de SMPTE-TT
1. CEA-608/CEA-708 – realizados como dados de usuário (SEI mensagens de fluxos de ensino fundamental de h. 264, ATSC/53, SCTE20) ou executada como dados complementares em arquivos MXF/GXF
1. Arquivos de subtítulo STL

Na saída, as seguintes opções estão disponíveis:

1. CEA-608 à conversão de CEA-708
1. CEA-608/CEA-708 passam (incorporado em mensagens SEI de fluxos de ensino fundamental h. 264 ou realizados como subordinados dados em arquivos MXF)
1. SCC
1. SMPTE sincronizou texto (da origem CEA 608 por SMPTE RP2052; incluindo criação de arquivo DFXP)
1. Arquivo de SRT subtítulo
1. Fluxos de subtítulo DVB

Observação: nem todos os formatos de saída acima são suportados para entrega por meio de streaming nos serviços de mídia do Azure.

##<a name="known-issues"></a>Problemas conhecidos

Se o vídeo de entrada não contiver legendas codificadas, a saída ativo ainda terá um arquivo TTML vazio. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
