<properties
    pageTitle="Integração do Windows SDK Universal"
    description="Integração Universal Windows SDK para Azure contrato móvel"                                     
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integração de Universal SDK do Windows Azure contrato móvel

Este documento descreve todas as integração e configuração opções disponíveis para o SDK Universal do Azure Mobile contrato Windows.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve primeiro concluir nosso [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Recursos avançados

### <a name="reporting-features"></a>Recursos de relatórios
Você pode adicionar esses recursos:

1. [Opções de relatórios avançadas](mobile-engagement-windows-store-advanced-reporting.md)

2. [Opções de configuração avançada](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificações

[Como integrar alcance (notificações) em seu aplicativo Universal do Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementação de plano de marca:

[Como usar o contrato Mobile avançada API de marcação em seu aplicativo Universal do Windows](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Notas de versão

###<a name="340-04192016"></a>3.4.0 (04/19/2016)

-   Alcançar melhorias de sobreposição.
-   Logs de console adicionado "TestLogLevel" API para ativar/desativar/filtro emitidas pelo SDK.
-   Inicie o fixas notificações na atividade direcionamento a primeira atividade não exibida no aplicativo.

Para versões anteriores, consulte as [notas de versão completa](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimentos de atualização

Se você já tiver integrado uma versão antiga do contrato em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Se você perdeu várias versões do SDK, você pode precisar acompanhar vários procedimentos. Consulte os [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md)de completa. Por exemplo, se você migrar do 0.10.1 para 0.11.0, que você precisa primeiro, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

###<a name="from-330-to-340"></a>De 3.3.0 para 3.4.0

####<a name="test-logs"></a>Logs de teste

Logs de console produzidos pelo SDK agora podem ser habilitado/desabilitado/filtrada. Para personalizar, atualizar a propriedade `EngagementAgent.Instance.TestLogEnabled` a um dos valores disponíveis da `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Recursos

Sobreposição de alcance foi aprimorada. Ele é parte dos recursos de pacote SDK NuGet.

Durante a atualização para a nova versão do SDK, você pode escolher se deseja manter os seus arquivos da pasta de sobreposição de seus recursos ou não:

* Se a sobreposição anterior está trabalhando para você, ou você é integrar o `WebView` elementos manualmente, em seguida, você pode decidir manter seu saindo arquivos, ele ainda funcionarão.
* Para atualizar para a nova sobreposição, substitua todo `overlay` pasta de seus recursos com o novo nome do pacote SDK (aplicativos UWP: após a atualização, você pode obter a nova pasta de sobreposição da % USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Usando a nova sobreposição substitui todas as personalizações feitas na versão anterior.

### <a name="upgrade-from-older-versions"></a>Atualização de versões mais antigas

Consulte [atualizar procedimentos](mobile-engagement-windows-store-upgrade-procedure.md)
