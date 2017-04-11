<properties 
    pageTitle="Criando filtros com os serviços de mídia do Microsoft Azure SDK .NET" 
    description="Este tópico descreve como criar filtros para que seu cliente possa usá-los para seções específicas do fluxo de um fluxo. Serviços de mídia cria manifestos dinâmicos para obter este streaming seletiva." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Criando filtros com os serviços de mídia do Microsoft Azure SDK .NET

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [RESTANTE](media-services-rest-dynamic-manifest.md)

Começando com versão 2.11, serviços de mídia permite definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirá que seus clientes optar por fazer coisas como: reprodução apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro), ou especificar apenas um subconjunto de representações de áudio e vídeo que dispositivo do cliente pode manipular (em vez de todas as representações que estão associadas a ativo). A filtragem de seus ativos é obtida por meio de s **Manifesto dinâmicos**que são criados por solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Para obter informações detalhadas relacionadas a filtros e manifesto dinâmico, consulte [Visão geral de manifestos dinâmico](media-services-dynamic-manifest-overview.md).

Este tópico mostra como usar o SDK do .NET de serviços de mídia para criar, atualizar e excluir filtros. 


Observação Se você atualizar um filtro, pode levar até 2 minutos para fluxo de extremidade para atualizar as regras. Se o conteúdo foi served usando esse filtro (e armazenados em cache proxies e CDN caches), atualizar esse filtro pode resultar em falhas de player. É recomendável para limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente. 

##<a name="types-used-to-create-filters"></a>Tipos usados para criar filtros

Os seguintes tipos são usados ao criar filtros: 

- **IStreamingFilter**.  Esse tipo está baseado no seguinte API REST [filtro](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Esse tipo se baseia a seguir da API REST [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Esse tipo se baseia a seguir da API REST [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** e **IFilterTrackPropertyCondition**. Esses tipos são baseados nas seguintes APIs REST [FilterTrackSelect e FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Criar/Atualizar/leitura/excluir os filtros globais

O código a seguir mostra como usar o .NET para criar, atualizar, leia e excluir filtros de ativos.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Filtros de criar/atualizar/leitura/excluir ativo

O código a seguir mostra como usar o .NET para criar, atualizar, leia e excluir filtros de ativos.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Construir streaming URLs que usam filtros

Para obter informações sobre como publicar e fornecer os ativos, consulte [Oferecer conteúdo visão geral de clientes](media-services-deliver-content-overview.md).


Os exemplos a seguir mostram como adicionar filtros a URLs streaming.

**MPEG TRAÇO** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming V4 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming V3 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Streaming suave**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também 

[Visão geral de manifestos dinâmico](media-services-dynamic-manifest-overview.md)
 

