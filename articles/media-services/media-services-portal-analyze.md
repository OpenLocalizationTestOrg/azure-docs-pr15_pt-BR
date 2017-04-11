<properties
    pageTitle="Analisar sua mídia usando o portal do Azure | Microsoft Azure"
    description="Este tópico aborda como processar suas mídias com processadores de mídia de análise de mídia (MPs) usando o portal do Azure."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analisar sua mídia usando o portal do Azure

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Visão geral

Análise de serviços de mídia Azure é uma coleção de componentes de fala e visão (em escala empresarial, conformidade, segurança e alcance global) que tornam mais fácil para as organizações e empresas para originar acionáveis percepções do seus arquivos de vídeo. Para visão detalhada da análise de serviços de mídia do Azure consulte [neste](media-services-analytics-overview.md) tópico. 

Este tópico aborda como processar suas mídias com processadores de mídia de análise de mídia (MPs) usando o portal do Azure. Mídia Analytics MPs produzir arquivos MP4 ou JSON. Se um processador de mídia produzidos um arquivo MP4, cada vez que você pode baixar o arquivo. Se um processador de mídia produzidos um arquivo JSON, você pode baixar o arquivo do armazenamento de blob do Microsoft Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que você deseja analisar 
 
1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2. Na janela **configurações** , selecione **ativos**.  
.
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Selecione o ativo que você gostaria de analisar e pressione o botão de **análise** .
        
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Na janela de **ativos de mídia de processo com a análise de mídia** , selecione o processador. 

    O restante do artigo explica por que e como usar cada processador. 
   
4. Pressione **criar** para iniciar um trabalho.

## <a name="azure-media-indexer"></a>Indexador de mídia do Microsoft Azure

O processador de mídia do **Azure mídia indexador** permite que você verifique os arquivos de mídia e conteúdo pesquisável, bem como gerar faixas de legendas fechadas. Esta seção fornece alguns detalhes sobre as opções que você pode especificar para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma

O idioma natural a ser reconhecida no arquivo multimídia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas

Você pode escolher um formato de legenda que será gerado com seu conteúdo. Um trabalho de indexação pode gerar arquivos de legenda codificada nos seguintes formatos:  

- **Sami**
- **TTML**
- **WebVTT**

Fechada legenda (CC) arquivos nestes formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

### <a name="aib-file"></a>Arquivo AIB

Selecione esta opção se você gostaria de gerar o arquivo de áudio índice Blob para uso com o IFilter de servidor SQL personalizadas. Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Palavras-chave

Selecione esta opção se você gostaria de gerar um arquivo XML de palavras-chave. Este arquivo contém palavras-chave extraídas do conteúdo fala, com frequência e informações de deslocamento.

### <a name="job-name"></a>Nome do trabalho

Um nome amigável que permite que você identifique o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o andamento de uma tarefa. 

### <a name="output-file"></a>Arquivo de saída

Um nome amigável que permite que você identifique o conteúdo de saída. 

## <a name="azure-media-hyperlapse"></a>Hyperlapse de mídia do Microsoft Azure

Azure Hyperlapse de mídia é um MP que cria suaves vídeos de tempo decorrido do conteúdo da primeira pessoa ou câmera de ação.  Para obter mais informações, consulte [Este](media-services-hyperlapse-content.md) tópico. Esta seção fornece alguns detalhes sobre as opções que você pode especificar para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocidade 

Especifique a velocidade com a qual acelerar o vídeo de entrada. O resultado é uma representação estabilizada e tempo decorrido o vídeo de entrada.

### <a name="job-name"></a>Nome do trabalho

Um nome amigável que permite que você identifique o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o andamento de uma tarefa. 

### <a name="output-file"></a>Arquivo de saída

Um nome amigável que permite que você identifique o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Detector de Face de mídia do Microsoft Azure

O processador de mídia do **Azure mídia Face Detector** (MP) permite que você contar, acompanhar os movimentos e até mesmo medir participação de audiência e resposta por meio de expressões de faciais. Este serviço contém dois recursos: 

- **Detecção de face**

    Detecção de face localiza e rastreia faces humanos dentro de um vídeo. Várias faces podem ser detectadas e subsequentemente ser rastreados conforme eles movimentam, com os metadados de hora e local retornado em um arquivo JSON. Durante o controle, ele tentará atribuir uma ID consistente para a mesma face enquanto a pessoa é mover-se na tela, mesmo se eles são obstruídos ou deixe resumidamente o quadro.

    >[AZURE.NOTE]Este serviços não executará reconhecimento de face. Um indivíduo que deixa o quadro ou fica obstruído muito longo receberá uma nova ID quando eles retornarem.

- **Detecção de emoções**
    
    Detecção de emoções é um componente opcional do processador Face detecção de mídia que retorna análise em vários atributos emocionais de faces detectadas, incluindo felicidade, tristeza, medo, irritação e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de detecção

Um dos seguintes modos pode ser usado pelo processador:

- detecção de face
- por detecção de emoções face
- detecção de emoções agregada

### <a name="job-name"></a>Nome do trabalho

Um nome amigável que permite que você identifique o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o andamento de uma tarefa. 

### <a name="output-file"></a>Arquivo de saída

Um nome amigável que permite que você identifique o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Detector de movimento de mídia do Microsoft Azure

O **Detector de movimento de mídia do Azure** mídia processador () permite que você identifique com eficiência seções de interesse dentro de um vídeo caso contrário normal e por extenso. Detecção de movimento pode ser usada em filmagem em câmera estático para identificar seções do vídeo onde o movimento ocorre. Ele gera um arquivo JSON que contém um metadados com carimbos de hora e a região delimitadora onde o evento ocorreu.

Dirigido feeds de vídeo de segurança, essa tecnologia é capaz de categorizar movimento em eventos relevantes e falsos positivos como sombras e alterações de iluminação. Isso permite gerar alertas de segurança de feeds de câmera sem recebam com eventos sem importância infinitas, e ser capaz de extrair momentos de interesse de vídeos de vigilância muito longa.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniaturas de vídeo de mídia do Microsoft Azure

Este processador pode ajudá-lo a criar resumos dos vídeos longos selecionando automaticamente trechos de interessantes no vídeo de origem. Isso é útil quando você quiser fornecer uma descrição rápida do que esperar em um vídeo longo. Para obter informações detalhadas e exemplos, consulte [Usar Azure Media Video miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome do trabalho

Um nome amigável que permite que você identifique o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o andamento de uma tarefa. 

### <a name="output-file"></a>Arquivo de saída

Um nome amigável que permite que você identifique o conteúdo de saída. 


##<a name="next-steps"></a>Próximas etapas

Serviços de mídia do modo de exibição caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


