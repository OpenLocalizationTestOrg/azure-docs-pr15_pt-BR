<properties 
    pageTitle="Como criar um processador de mídia | Microsoft Azure" 
    description="Aprenda a criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar o conteúdo de mídia para serviços de mídia do Azure. Exemplos de código escritos em c# e usam o SDK de serviços de mídia para .NET." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância de processador de mídia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTANTE](media-services-rest-get-media-processor.md)


##<a name="overview"></a>Visão geral

Nos serviços de mídia que um processador de mídia é um componente que lida com uma tarefa de processamento específico, como codificação, formate conversão, criptografar ou descriptografar conteúdo de mídia. Você normalmente cria um processador de mídia quando você estiver criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

Nome do processador de mídia|Descrição|Mais informações
---|---|---
Padrão de codificador de mídia|Fornece recursos padrão de codificação de sob demanda. |[Visão geral e a comparação do Azure em demanda codificadores de mídia](media-services-encode-asset.md)
Fluxo de trabalho do Media codificador Premium|Permite executar tarefas de codificação usando fluxo de trabalho do Media codificador Premium.|[Visão geral e a comparação do Azure em demanda codificadores de mídia](media-services-encode-asset.md)
Indexador de mídia do Microsoft Azure| Permite que você verifique os arquivos de mídia e conteúdo pesquisável, bem como gerar faixas de legendas fechadas e palavras-chave.|[Indexador de mídia do Microsoft Azure](media-services-index-content.md)
Hyperlapse de mídia Azure (visualização)|Permite que você suaves check-out "impactos" em seu vídeo com vídeo estabilização. Também permite acelerar o seu conteúdo em um clipe de consumo.|[Hyperlapse de mídia do Microsoft Azure](media-services-hyperlapse-content.md)
Codificador de mídia do Microsoft Azure|Depreciado
Descriptografia de armazenamento| Depreciado|
Gerenciador de mídia do Microsoft Azure|Depreciado|
Criptografador de mídia do Microsoft Azure|Depreciado|

##<a name="get-media-processor"></a>Obter processador de mídia

O método a seguir mostra como obter uma instância de processador de mídia. O exemplo de código pressupõe o uso de uma variável de nível de módulo chamada **Context** para fazer referência o contexto do servidor, conforme descrito na seção [como: conectar a mídia serviços programaticamente](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>Próximas etapas

Agora que você sabe como obter uma instância de processador de mídia, vá para o tópico [como codificar um ativo](media-services-dotnet-encode-with-media-encoder-standard.md) que mostrará como usar o padrão de codificador de mídia para codificar um ativo.


