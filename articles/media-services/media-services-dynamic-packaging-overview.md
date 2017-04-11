<properties
    pageTitle="Visão geral de embalagem dinâmico | Microsoft Azure"
    description="O tópico apresenta e visão geral da embalagem dinâmica."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Embalagem dinâmica

##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure pode ser usados para produzir muitos mídia fonte formatos de arquivo, formatos, de fluxo de mídia e formatos de proteção de conteúdo para uma variedade de tecnologias do cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Esses clientes compreendam protocolos diferentes, por exemplo iOS requer um formato de Streaming Live HTTP (HLS) V4 e Silverlight e Xbox exigem Streaming suave. Se você tiver um conjunto de taxa de bits adaptativa (multi-taxa de bits) MP4 arquivos (ISO Base Media 14496-12) ou um conjunto de taxa de bits adaptativa Streaming suave arquivos que você deseja atender aos clientes que entendam traço de MPEG, HLS ou Streaming suave, você deve aproveitar embalagem dinâmica de serviços de mídia.

Com a embalagem dinâmica tudo o que você precisa é criar um ativo que contém um conjunto de taxa de bits adaptativa MP4 arquivos ou taxa de bits adaptativa Streaming suave. Em seguida, com base no formato especificado na solicitação de manifesto ou fragmento, o Streaming de On Demand servidor garantirá que você recebe o fluxo no protocolo que você optou. Como resultado, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia serão construir e servir a resposta apropriada com base em solicitações de um cliente.

O diagrama a seguir mostra a codificação tradicional e o fluxo de trabalho de embalagem estático.

![Codificação de estático](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho de embalagem dinâmico.

![Codificação de dinâmico](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para tirar proveito da embalagem dinâmico, você deve primeiro obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [como serviços de mídia de escala](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Cenário comum

1. Carregar um arquivo de entrada (chamado de um arquivo de mezzanine). Por exemplo, h. 264, MP4 ou WMV (para a lista de formatos suportados consulte [Formatos compatíveis com o padrão de codificador de mídia](media-services-media-encoder-standard-formats.md).

1. Codifica seu arquivo mezzanine aos conjuntos de taxa de bits adaptativa h. 264 MP4.

1. Publica o ativo que contém a taxa de bits adaptativa MP4 definido por criar o localizador On Demand.

1. Construa as URLs streaming para acessar e transmitir seu conteúdo.


##<a name="preparing-assets-for-dynamic-streaming"></a>Preparação de ativos para streaming dinâmico

Para preparar seu ativo streaming dinâmico, você tem duas opções:

1. [Carregar um arquivo mestre](media-services-dotnet-upload-files.md).
2. [Usar o codificador mídia codificador padrão para produzir conjuntos de taxa de bits adaptativa h. 264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Fluxo de seu conteúdo](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formatos que não são suportados pelo embalagem dinâmica

Os seguintes formatos de arquivo de origem não são suportados pelo embalagem dinâmica.

- Arquivos do Dolby digital mp4.
- Arquivos de suavização de Dolby digital.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
