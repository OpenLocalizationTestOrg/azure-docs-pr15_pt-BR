<properties 
    pageTitle="Como adicionar unidades de codificação" 
    description="Saiba como adicionar unidades de codificação com .NET"  
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
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Como escalar a codificação com .NET SDK

> [AZURE.SELECTOR]
- [Portal](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTANTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Visão geral

>[AZURE.IMPORTANT] Certifique-se de revisar o tópico de [Visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre dimensionamento tópico de processamento de mídia.
 
Para alterar o tipo de unidade reservadas e o número de codificação reservadas unidades usando .NET SDK, faça o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Abrindo um tíquete

Por padrão todas as contas de serviços de mídia podem chegar a até 25 codificação e 5 sob demanda Streaming reservadas unidades. Você pode solicitar um limite superior abrindo um tíquete.

###<a name="open-a-support-ticket"></a>Abrir um tíquete

Para abrir um suporte tíquete, faça o seguinte:

1. Clique em [obter suporte](https://manage.windowsazure.com/?getsupport=true). Se você não estiver conectado, você será solicitado a inserir suas credenciais.

1. Selecione sua assinatura.

1. Em tipo de suporte, selecione "Técnicos".

1. Clique em "Criar ingressos".

1. Selecione "Azure Media Services" na lista de produtos apresentadas na próxima página.

1. Selecione um tipo de problema"" que é apropriado para o seu problema.

1. Clique em continuar.

1. Siga as instruções na próxima página e, em seguida, insira os detalhes sobre o problema.

1. Clique em Enviar para abrir o tíquete.



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
