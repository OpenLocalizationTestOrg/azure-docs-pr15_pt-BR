<properties
    pageTitle="Visão geral do processamento de mídia de dimensionamento | Microsoft Azure"
    description="Este tópico é uma visão geral do processamento de mídia dimensionamento com os serviços de mídia do Azure."
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
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Dimensionamento de visão geral do processamento de mídia

Esta página fornece uma visão geral de como e por que dimensionar processamento de mídia. 

## <a name="overview"></a>Visão geral

Uma conta de serviços de mídia está associada um tipo de unidade reservadas, o que determina a velocidade com que sua mídia processando tarefas é processadas. Você pode escolher entre os seguintes tipos de unidade reservadas: **S1**, **S2**ou **S3**. Por exemplo, o mesmo trabalho codificação é executado mais rápido quando você usa a comparação de tipo de unidade **S2** reservada para o tipo de **S1** . Para obter mais informações, consulte os [Tipos de unidade reservadas](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Além de especificar o tipo de unidade reservadas, você pode especificar para provisionar sua conta com unidades reservadas. O número de unidades reservadas provisionados determina o número de tarefas de mídia que podem ser processados simultaneamente em uma determinada conta. Por exemplo, se sua conta tem cinco unidades reservadas, e tarefas de cinco mídia serão executado simultaneamente tanto como existem tarefas a serem processadas. As tarefas restantes esperará na fila e irá estão selecionadas para processamento sequencialmente quando concluir uma tarefa em execução. Se uma conta não tem quaisquer unidades reservadas provisionadas, em seguida, tarefas serão separadas para cima sequencialmente. Nesse caso, o tempo de espera entre terminando de uma tarefa e a próxima um começando dependerá a disponibilidade dos recursos do sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolhendo entre tipos diferentes de unidade reservadas

A tabela a seguir ajuda você a tomar a decisão ao escolher entre diferentes velocidades codificação. Ele também fornece alguns casos de referência e fornece SAS URLs que você pode usar para fazer o download de vídeos em que você pode executar seus próprios testes:

Cenários|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Caso de uso pretendido| Taxa de bits única codificação. <br/>Arquivos SD ou abaixo resoluções, tempo não confidenciais e de baixo custo.|Taxa de bits única e vários codificação de taxa de bits.<br/>Uso normal para codificação SD e HD. |Taxa de bits única e vários codificação de taxa de bits.<br/>Vídeos de resolução de HD e 4K completos. Tempo de codificação de conclusão confidencial e mais rápido. 
Padrão de referência|[Arquivo de entrada: 5 minutos longos 640x360p em 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codificação a uma taxa de bits única arquivo MP4, com a mesma resolução, leva aproximadamente 11 minutos.|[Arquivo de entrada: 5 minutos longos 1280x720p em 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codificação com "H264 única taxa de bits 720p" predefinidas leva aproximadamente 5 minutos.<br/><br/>Codificação com "H264 taxa de vários bits 720p" predefinir leva aproximadamente 11,5 minutos.|[Arquivo de entrada: 5 minutos longos 1920x1080p em 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codificação com "H264 única taxa de bits 1080p" predefinidas leva aproximadamente 2.7 minutos.<br/><br/>Codificação com "H264 taxa de vários bits 1080p" predefinir leva aproximadamente 5,7 minutos.

##<a name="considerations"></a>Considerações

>[AZURE.IMPORTANT] Examine as considerações descritas nesta seção.  

- Unidades de reservadas funcionam para paralelizar todo o processamento de mídia, incluindo a indexação trabalhos usando indexador de mídia do Azure.  No entanto, ao contrário de codificação, trabalhos de indexação não seja processados mais rápido com mais rapidez reservadas unidades.

- Se usando o pool compartilhado, ou seja, sem qualquer unidades reservadas, em seguida, suas tarefas codificar tem o mesmo desempenho conforme com S1 RUs. No entanto, há nenhum limite superior para a hora de suas tarefas podem gastar em estado na fila e a qualquer momento, no máximo uma tarefa é estar em execução.

- Os centros de dados a seguir não oferecem o tipo de unidade **S2** reservado: Sul Brasil, Índia Oeste, Índia Central e do Sul Índia.

- Os centros de dados a seguir não oferecem o tipo de unidade **S3** reservado: Sul Brasil, Índia Oeste, Índia Central.

- O maior número de unidades especificadas para o período de 24 horas é usado no cálculo do custo.


##<a name="quotas-and-limitations"></a>Cotas e limitações

Para obter informações sobre cotas e limitações e como abrir um tíquete, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Próxima etapa

Alcançar a tarefa de processamento de mídia dimensionamento com uma dessas tecnologias: 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-scale-media-processing.md)
- [RESTANTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
