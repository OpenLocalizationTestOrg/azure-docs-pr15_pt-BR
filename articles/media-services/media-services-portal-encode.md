<properties
    pageTitle="Codificar um ativo usando mídia codificador padrão com o portal Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de codificação de um ativo usando o padrão de codificador de mídia com o portal do Azure."
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


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codificar um ativo usando o padrão de codificador de mídia com o portal do Azure

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando adaptativa taxa de bits streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso). Para preparar vídeos para taxa de bits adaptativa streaming, você precisa codificar o vídeo de origem em arquivos de multi-taxa de bits. Você deve usar o codificador de **Mídia codificador padrão** para codificar vídeos.  

Serviços de mídia também fornece embalagem dinâmica que permite que você fornecer sua MP4s multi-taxa de bits nos seguintes formatos de streaming: MPEG traço, HLS, Streaming suave ou HDS, sem precisar empacotar novamente em esses formatos de fluxo contínuo. Com embalagem dinâmica você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia será construir e servir a resposta apropriada com base em solicitações de um cliente.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo de origem em um conjunto de arquivos de MP4 multi-taxa de bits (as etapas de codificação são demonstrou posteriormente nesta seção).
- Obtenha pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [Configurando pontos de extremidade de streaming](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Para dimensionar o processamento de mídia, consulte [Este](media-services-portal-scale-media-processing.md) tópico.

## <a name="encode-with-the-azure-portal"></a>Codificar com o portal do Azure

Esta seção descreve as etapas que você pode tomar para codificar seu conteúdo com o codificador de mídia padrão.

1.  No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2.  Na janela **configurações** , selecione **ativos**.  
2.  Na janela de **ativos** , selecione o ativo que você gostaria de codificar.
3.  Pressione o botão de **codificar** .
4.  Na janela **codificar um ativo** , selecione o processador "Padrão de codificador de mídia" e um valor predefinido. Por exemplo, se você souber o vídeo de entrada tem uma resolução de pixels de 1920x1080, você pode usar o "H264 taxa de vários bits 1080p" predefinido. Para obter mais informações sobre as predefinições, consulte [Este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar o valor predefinido que é mais apropriado para o vídeo de entrada. Se você tem um vídeo de baixa resolução (640 x 360), então você não deve estar usando o padrão "H264 taxa de vários bits 1080p" predefinido.
    
    Para facilitar o gerenciamento, você tem uma opção de edição no nome do ativo saída e o nome do trabalho.
        
    ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pressione **criar**.


##<a name="next-step"></a>Próxima etapa

Você pode monitorar o andamento codificação de trabalho com o portal Azure, conforme descrito [neste](media-services-portal-check-job-progress.md) artigo.  

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


