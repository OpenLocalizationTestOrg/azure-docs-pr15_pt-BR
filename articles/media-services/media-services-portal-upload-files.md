<properties
    pageTitle=" Carregar arquivos para uma conta de serviços de mídia usando o portal de Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de carregar arquivos para uma conta de serviços de mídia usando o portal do Azure"
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
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="juliako"/>


# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Carregar arquivos para uma conta de serviços de mídia usando o portal do Azure 

> [AZURE.SELECTOR]
- [Portal](media-services-portal-upload-files.md)
- [.NET](media-services-dotnet-upload-files.md)
- [RESTANTE](media-services-rest-upload-files.md)

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Em serviços de mídia, você pode carregar arquivos digitais em um ativo. O ativo pode conter vídeo, áudio, imagens, conjuntos de miniaturas, texto tracks e legenda codificada arquivos (e os metadados sobre esses arquivos.) Depois que os arquivos são carregados, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming.
 
1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.

2. Na lâmina **configurações** , clique em **ativos**.

    ![Carregar arquivos](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Clique no botão **carregar** .

    A janela **carregar um vídeo ativo** aparece.

    >[AZURE.NOTE] Não há nenhuma limitação de tamanho de arquivo.
    
4. Navegue até o vídeo desejado em seu computador, selecione-o e acertar Okey.  

    Inicia o carregamento e você pode ver o andamento sob o nome de arquivo.  

Quando o carregamento for concluída, você verá o novo ativo listado na janela de **ativos** . 


## <a name="next-steps"></a>Próximas etapas

Agora você pode codificar seus ativos carregados. Para obter mais informações, consulte [codificar ativos](media-services-portal-encode.md).

## <a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


