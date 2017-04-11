<properties
    pageTitle="Mover os recursos de aplicativo da Web para outro grupo de recursos"
    description="Descreve os cenários onde você pode mover Web Apps e os serviços de aplicativo de um grupo de recursos para outro."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configurações de mover com suporte

Você pode mover os recursos de Azure Web App usando a [Api do BRAÇO mover recursos](../resource-group-move-resources.md).

Azure Web Apps atualmente oferece suporte a cenários de mover a seguir:

* Movendo todo o conteúdo de um grupo de recursos (aplicativos web, planos de serviço de aplicativo e certificados) para outro grupo de recursos 
    * Observação: O grupo de recursos de destino não pode conter qualquer recursos Microsoft.Web neste cenário
* Mover aplicativos web individuais a um grupo de recursos diferentes, enquanto ainda hospedando-las em seu plano de serviço de aplicativo atual (o plano de serviço de aplicativo fica no grupo de recursos antigo)
