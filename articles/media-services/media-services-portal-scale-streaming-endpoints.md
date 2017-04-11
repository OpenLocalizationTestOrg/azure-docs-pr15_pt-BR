<properties
    pageTitle=" Escala de streaming pontos de extremidade com o portal do Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de dimensionamento streaming pontos de extremidade com o portal do Azure."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Escala de streaming pontos de extremidade com o portal do Azure

##<a name="overview"></a>Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando vídeo por meio de taxa de bits adaptativa streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia fornece pacote dinâmico que permite que você fornecer sua taxa de bits adaptativa MP4 codificado conteúdo streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) just-in-time, sem precisar armazenar predefinidas versões de cada um desses streaming formatos.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de arquivos de taxa de bits adaptativa MP4 (as etapas de codificação são demonstrou posteriormente neste tutorial).  
- Crie pelo menos uma unidade streaming para o *fluxo de extremidade* do qual você planeja entrega seu conteúdo. As etapas a seguir mostram como alterar o número de unidades de streaming.

Com embalagem dinâmica você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia será construir e servir a resposta apropriada com base em solicitações de um cliente.

Além disso, você pode controlar a capacidade do serviço de Streaming ponto de extremidade de lidar com crescentes necessidades de largura de banda ajustando streaming unidades. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. Unidades de streaming fornecem capacidade egresso dedicado que pode ser comprados em incrementos de 200 Mbps e funcionalidade adicional que funcionalidades que inclui: [dinâmica embalagem](media-services-dynamic-packaging-overview.md), CDN integração e configuração avançada. Para obter mais informações, consulte [pontos de extremidade de streaming de gerenciar com o portal do Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Escala de pontos de extremidade de streaming

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2. Na janela **configurações** , selecione **pontos de extremidade de Streaming**.
3. Clique no ponto de extremidade de streaming que você deseja dimensionar. 
4. Mova o controle deslizante para especificar o número de unidades de streaming
 
![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Aplica considerando o seguinte:

- A alocação de qualquer novas unidades streaming pode levar cerca de 20 minutos para ser concluída. 
- Atualmente, indo de qualquer valor positivo de streaming unidades de volta para nenhum, pode desabilitar o streaming de sob demanda para até uma hora.
- O maior número de unidades especificadas para o período de 24 horas é usado no cálculo do custo. Para obter informações sobre preços detalhes, consulte [Detalhes de preços de serviços de mídia](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


