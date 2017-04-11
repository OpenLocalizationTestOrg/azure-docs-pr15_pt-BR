<properties 
    pageTitle="Windows Phone visão geral do SDK do Silverlight" 
    description="Visão geral do SDK do Windows Phone Silverlight para Azure contrato móvel"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone visão geral do SDK do Silverlight para Azure contrato móvel

Comece aqui para obter os detalhes sobre como integrar o contrato de celular do Azure em um Windows Phone Silverlight App. Se você gostaria de Experimente primeiro, certifique-se que você conclua nosso [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procedimentos de integração

1. Comece aqui: [como integrar contrato Mobile em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Para notificações: [como integrar alcance (notificações) em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Marcar plano de implementação: [como usar o contrato Mobile avançada API em seu aplicativo do Windows Phone Silverlight de marcação](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Notas de versão

###<a name="330-04192016"></a>3.3.0 (04/19/2016)
Parte do nuget *MicrosoftAzure.MobileEngagement* pacote **v3.4.0**

-   Logs de console adicionado "TestLogLevel" API para ativar/desativar/filtro emitidas pelo SDK.

Versão anterior, consulte as [notas de versão completa](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimentos de atualização

Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você pode precisar acompanhar vários procedimentos se você perdeu várias versões do SDK. Consulte os [Procedimentos de atualização](mobile-engagement-windows-phone-upgrade-procedure.md)de completa. Por exemplo, se você migrar do 0.10.1 para 0.11.0, que você precisa primeiro, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

###<a name="from-200-to-330"></a>De 2.0.0 para 3.3.0

####<a name="test-logs"></a>Logs de teste

Logs de console produzidos pelo SDK agora podem ser habilitado/desabilitado/filtrada. Para personalizar isso, atualizar a propriedade `EngagementAgent.Instance.TestLogEnabled` para uma do valor disponível a `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Atualização de versões mais antigas

Consulte [atualizar procedimentos](mobile-engagement-windows-phone-upgrade-procedure.md)
 
