<properties 
    pageTitle="Usando parceiros para fornecer Widevine licenças aos serviços de mídia do Azure | Microsoft Azure" 
    description="Este artigo descreve como você pode usar serviços de mídia do Azure (AMS) para distribuir um fluxo dinamicamente criptografada por AMS com PlayReady e Widevine DRMs. A licença PlayReady proveniente de servidor de licenças do PlayReady de serviços de mídia e Widevine licença é fornecida pelo servidor de licenças de castLabs." 
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
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando parceiros para fornecer Widevine licenças aos serviços de mídia do Azure

##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure permitem que você forneça MPEG-traço protegido com Widevine DRM, que é criptografado pela especificação de criptografia comuns (CENC).

Começando com a mídia serviços .NET SDK versão 3.5.2, serviços de mídia permite que você configure o modelo de licença de Widevine e obter Widevine licenças. Você também pode usar os seguintes parceiros AMS para ajudá-lo a oferecer Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Você pode usar [castLabs](http://castlabs.com/company/partners/azure/) para entregar Widevine licenças. Para obter mais informações, consulte [usando castLabs para fornecer licenças DRM aos serviços de mídia do Azure](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Você pode usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para entregar Widevine licenças. Para obter mais informações, consulte [Usando Axinom para fornecer licenças DRM aos serviços de mídia do Azure](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Usando o PlayReady e/ou Widevine criptografia comuns dinâmico](media-services-protect-with-drm.md)

[Blog do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

