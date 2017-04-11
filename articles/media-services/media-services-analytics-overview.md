<properties
    pageTitle="Visão geral de análise de serviços de mídia do Azure | Microsoft Azure"
    description="Serviços de mídia do Azure oferece a visualização pública da análise de mídia do Azure, um conjunto de serviços de visão de fala e computador em escala empresarial, conformidade, segurança e alcance global. Serviços de análise de mídia Azure são criados usando os componentes de plataforma de serviços de mídia do Azure principais e, portanto, estão prontos para lidar com mídia processamento em escala no primeiro dia. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Visão geral de análise de serviços de mídia do Azure

##<a name="overview"></a>Visão geral

Mais organizações e empresas estão adotando vídeo como o meio preferido para treinar seus funcionários, entre em contato com seus clientes e funções de negócios do documento. Nuvem computação torna mais eficaz armazenar, transmitir e acessar esses arquivos de mídia grandes, mas como empresas crescem sua biblioteca de conteúdo de vídeo, eles devem ter um meio igualmente eficaz para extração novas percepções do vídeo para criar mais significativo, personalizada interações com seus audiências e levar seus negócios para o próximo nível.

Para resolver essa crescente necessidade no mercado, serviços de mídia do Azure oferece análise de mídia, uma coleção de componentes de fala e visão (em escala empresarial, conformidade, segurança e alcance global) que tornam mais fácil para as organizações e empresas para originar acionáveis percepções do seus arquivos de vídeo. Serviços de análise de mídia Azure são criados usando os componentes de plataforma de serviços de mídia do Azure principais e, portanto, estão prontos para lidar com mídia processamento em escala no primeiro dia.

Análise de mídia Azure permitem que desenvolvedores começar a usar os recursos de visão para vídeo em escala limitada e colocá-la a funcionalidade avançada em aplicativos rapidamente. Análise de mídia Azure baseia-se para ser usada por ambientes corporativos com escala completa, conformidade, segurança e alcance global necessários para grandes organizações.

O diagrama a seguir mostra **A análise de mídia** e outras partes principais da plataforma de serviços de mídia. 

![Fluxo de trabalho VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Processadores de mídia de análise de mídia produzir arquivos MP4 ou JSON. Se um processador de mídia produzidos um arquivo MP4, cada vez que você pode baixar o arquivo. Se um processador de mídia produzidos um arquivo JSON, você pode baixar o arquivo do armazenamento de blob do Microsoft Azure. 

## <a name="azure-media-analytics-services"></a>Serviços de análise de mídia Azure

- **Indexador** – indexador de mídia do Azure permite tornar o conteúdo pesquisável, bem como gerar faixas de legendas fechadas. Serviços de mídia do Azure lançado **Azure mídia indexador 2 Preview** com rapidez indexação e mais amplo suporte a idiomas. Idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês, português e árabe. Para obter informações detalhadas e exemplos, consulte [vídeos de processo com 2 de indexador de mídia do Azure](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse é um resultado de mais de 20 anos de pesquisa de visão do computador no Microsoft Research (MSR), combinando estabilização vídeo e tempo perder a validade para criar rápidos, consumo, belas vídeos do seu formulário longo conteúdo. Além de criar falhas de tempo, você também pode usar Hyperlapse para criar vídeos estáveis de vídeos shaky capturados por meio de telefones celulares e câmeras de vídeo. Para obter informações detalhadas e exemplos, consulte [Arquivos de mídia de Hyperlapse com Hyperlapse de mídia do Azure](media-services-hyperlapse-content.md)
 
- **Detecção de movimento** – você pode usar esse serviço para detectar movimento em um vídeo com planos de fundo de papel de carta. Isso é ideal para clientes que deseja verificar falsos positivos em eventos de movimento detectados pelo câmeras de vigilância nos feeds de vídeo de vigilância. Para obter informações detalhadas e exemplos, consulte [Detecção de movimento para análise de mídia do Azure](media-services-motion-detection.md).
 
- **Detecção de face e emoções Face** – usando esse serviço, você pode detectar faces de pessoas e suas emoções, incluindo felicidade, tristeza, surpresa, irritação, contempt, medo, desista e indiferença/neutro. Isso tem várias setor útil aplicativos, descritos abaixo, incluindo agregar e analisar ações de pessoas participando de um evento. Para obter informações detalhadas e exemplos, consulte [nominal e detecção de emoções para análise de mídia do Azure](media-services-face-and-emotion-detection.md).
 
- **Resumo do vídeo** – resumo de vídeo pode ajudá-lo a criar resumos dos vídeos longos selecionando automaticamente trechos de interessantes no vídeo de origem. Isso é útil quando você quiser fornecer uma descrição rápida do que esperar em um vídeo longo. Para obter informações detalhadas e exemplos, consulte [Usar Azure Media Video miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)

- **Reconhecimento óptico de caracteres** - Azure mídia Analytics OCR (reconhecimento óptico de caracteres) permite que você converter o conteúdo de texto em arquivos de vídeo em texto digital pesquisável editável. Isso permite automatizar a extração de metadados significativo do sinal de vídeo de sua mídia.
 
- **Redação face Scalable** - **Redactor de mídia do Azure** é um MP de análise de mídia do Azure que oferece redação face scalable na nuvem. Face redação permite que você modifique o vídeo para desfoque faces de pessoas selecionadas. Talvez você queira usar o serviço de redação de face em cenários de segurança e mídia de notícias públicos. Alguns minutos de filmagem em que contém várias faces podem levar horas para redigir manualmente, mas com esse serviço, o processo de redação face exigirão apenas algumas etapas simples. Para obter mais informações, consulte [Este](media-services-face-redaction.md) artigo.

 
## <a name="common-scenarios"></a>Cenários comuns

A seguir estão alguns dos cenários onde pode ajudar as organizações a análise de mídia do Azure e empresas setores obter novas percepções do vídeo para criar mais personalizada audiência e contratos de funcionário, bem como gerenciar com mais eficiência grande volume de conteúdo de vídeo:

- **Centros de chamada** – mesmo com o aparecimento das mídias sociais, chamada de cliente centros facilitam ainda uma grande porcentagem de transações de serviço de atendimento ao cliente. Codificado nesses dados de áudio é uma grande variedade de informações sobre clientes que podem ser analisados para melhorar a planos de produtos e também treinar funcionários de call center para atingir maior satisfação do cliente. Usando o indexador de mídia do Azure, os clientes são capazes de extrair texto e criar um índice de pesquisa e painéis para extrair intelligence em torno de mais comum reclamar, reclama de origem de e outros tais dados relevantes.

- **Moderação de conteúdo gerado pelo usuário** – de tomadas de mídia para departamentos de boletins, muitas organizações têm portais opostas públicos onde eles aceitarem mídia UGC, como vídeos e imagens. O volume de conteúdo pode ter picos devido a eventos inesperados. Nesses cenários, é quase impossível conduzir uma revisão eficaz manual do conteúdo é adequado. Clientes podem depender do serviço de moderação de conteúdo para focalizar o conteúdo apropriado.

- **Vigilância** - com o crescimento de câmeras IP, há uma explosão de vídeos de vigilância. Revisar manualmente o vídeo de vigilância é hora intenso e propensa a erros humanos. Análise de mídia Azure fornece vários componentes como detecção de movimento, detecção de face e Hyperlapse para tornar o processo de revisão, gerenciar e criação de derivados.

## <a name="media-services-analytics-media-processors"></a>Processadores de mídia de análise de serviços de mídia 

Esta seção lista todas as mídia serviços Analytics mídia processadores (MP) e mostra como usar .NET ou REST para obter um objeto de MP.

### <a name="mp-names"></a>Nomes de MP


- Visualização de indexador 2 de mídia do Microsoft Azure
- Indexador de mídia do Microsoft Azure
- Hyperlapse de mídia do Microsoft Azure
- Detector de Face de mídia do Microsoft Azure
- Detector de movimento de mídia do Microsoft Azure
- Miniaturas de vídeo de mídia do Microsoft Azure
- OCR de mídia do Microsoft Azure

### <a name="net"></a>.NET

A função a seguir leva um dos nomes de MP especificados e retornar um objeto de MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>RESTANTE

Solicitação:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Resposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Demonstrações

[Azure demonstrações de análise de mídia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Artigos relacionados

[Comunicado de análise de serviços de mídia](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
