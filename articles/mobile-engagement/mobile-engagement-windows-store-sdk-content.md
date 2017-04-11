<properties 
    pageTitle="Conteúdo do Windows SDK Universal de aplicativos" 
    description="Saiba mais sobre o conteúdo do SDK Universal de aplicativos do Windows Azure Mobile contrato"                    
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Conteúdo do Windows SDK Universal de aplicativos

Este documento lista e descreve o conteúdo implantado pelo SDK em seu aplicativo.

##<a name="the-resources-folder"></a>O `/Resources` pasta

Esta pasta contém todos os recursos que precisa ser Mobile contrato. Você também pode personalizá-los para ajustar seu aplicativo.

- `EngagementConfiguration.xml`: Arquivo de configuração do contrato a Mobile, isso é onde você pode personalizar as configurações de contrato Mobile (cadeia de conexão de celular contrato, falha de relatório...).

### <a name="html-folder"></a>pasta de /HTML mais externas

- `EngagementNotification.html`: O `Notification` exibir html design de faixas do aplicativo da web.

- `EngagementAnnouncement.html`: O `Announcement` modo de exibição design de html para exibições intersticiais do aplicativo da web.

### <a name="images-folder"></a>pasta de /Images

- `EngagementIconNotification.png`: O ícone de marca exibido à esquerda de uma notificação, substituí-lo por seu ícone de marca.

- `EngagementIconOk.png`: O `Ok` ícone as alcance de páginas de conteúdo para o botão de ação ou validação.

- `EngagementIconNOK.png`: O `NOK` ícone usado quando o botão de validação das páginas de conteúdo de alcance está desativado.
 
- `EngagementIconClose.png`: O `Close` ícone das notificações de alcance e conteúdo para o botão Dispensar.

### <a name="overlay-folder"></a>pasta de /overlay

- `EngagementPageOverlay.cs`: A página de sobreposição responsável pela adição do contrato atinja a interface de usuário do aplicativo para seu filho.
  
