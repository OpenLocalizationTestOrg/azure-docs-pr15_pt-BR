<properties 
    pageTitle="Criar fluxos de trabalho de codificação avançados com o Designer de fluxo de trabalho | Microsoft Azure" 
    description="Saiba mais sobre como criar fluxos de trabalho de codificação avançados com o Designer de fluxo de trabalho." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar fluxos de trabalho de codificação avançados com o Designer de fluxo de trabalho

##<a name="overview"></a>Visão geral

O **Designer de fluxo de trabalho** é uma ferramenta de área de trabalho do Windows que é usada para projetar e criar fluxos de trabalho personalizados para codificar com **Fluxo de trabalho do Media codificador Premium**.
Usando o poder da ferramenta designer de fluxo de trabalho, você pode projetar e criar fluxos de trabalho complexos que serão executado no **Premium de codificador de mídia**.  

Fluxos de trabalho podem incluir lógica de decisão do cliente e ramificação com base em Propriedades do arquivo de origem da entrada. Você pode criar fluxos de trabalho com propriedades substituíveis e valores dinâmicos para facilitar a repetir e personalizar na nuvem do mesmo as tarefas de codificação mais complexas.

Fluxos de trabalho de exemplo que você pode criar incluem:

- Decisão com base em fluxos de trabalho que inspecionar o conteúdo de origem para resolução e codificar somente as faixas de saída desejado.  Isso é helfpul eliminando as faixas desperdício seriam geradas pelo colocação em escala maior a inadvertidamente de conteúdo de origem.
- Vários arquivos de entrada podem ser usados para dar suporte a legendas, sobreposições e alinhando conteúdo junto. 

Esta ferramenta também pode ser usada para modificar qualquer um dos nossos [publicado fluxos de trabalho](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Para obter sua cópia da ferramenta Designer de fluxo de trabalho, entre em contato com mepd@microsoft.com.


Depois que um arquivo de fluxo de trabalho é criado, ele pode ser carregado como um ativo e, em seguida, ser usado para codificar arquivos de mídia. Para obter informações sobre como codificar com o **Fluxo de trabalho do Media codificador Premium** usando **.NET**, consulte [avançados a codificação com fluxo de trabalho do Media codificador Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificar fluxos de trabalho existentes

A versão padrão [publicado fluxos de trabalho](media-services-workflow-designer.md#existing_workflows) pode ser modificada usando a ferramenta de designer. Você pode obter o padrão de arquivos de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Os vídeos a seguintes demonstram como usar o designer.

###<a name="day-1--getting-started"></a>1º dia – Introdução

Vídeo do dia 1 abrange:

- Visão geral do Designer
- Fluxos de trabalho básicos – "Olá"
- Criar vários arquivos MP4 para uso com serviços de mídia do Azure streaming de saída

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Dia 2

Vídeo de dia 2 aborda:

- Cenários de arquivo de origem de variável – manipulação de áudio
- Fluxos de trabalho com lógica avançada
- Estágios de gráfico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Dia 3

Vídeo de dia 3 aborda:

- Script dentro/plantas de fluxos de trabalho
- Restrições com o codificador atual
- P & r
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Se você precisa de suporte ou tiver dúvidas sobre a criação de fluxos de trabalho personalizados na ferramenta designer de fluxo de trabalho, envie e-mail para mepd@microsoft.com.

##<a name="see-also"></a>Consulte também

[Vídeos de treinamento de Designer de fluxo de trabalho de codificador do Azure Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
