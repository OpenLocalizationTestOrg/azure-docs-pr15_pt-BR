<properties
    pageTitle="Gerenciar Azure Web App usando a automação do Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar o Azure Web App."
    services="app-service\web, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-web-app-using-azure-automation"></a>Gerenciando Azure Web App usando a automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Azure Web App.

## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](../automation/automation-intro.md) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos. Usando o Azure automação, manual, repetido, demorada e propensa tarefas podem ser automatizadas para aumentar a confiabilidade, a eficiência e tempo de valor para sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT e DevOps funcionários se concentrar no trabalho que adiciona o valor de negócios, movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.


## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Como o Azure automação pode ajudar gerenciar Azure Web App?

Aplicativo da Web pode ser gerenciado no Azure automação usando cmdlets do PowerShell que estão disponível nos [módulos do PowerShell do Azure](../powershell-install-configure.md). Você pode [instalar esses cmdlets do PowerShell do aplicativo da Web no Azure automação](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/), para que você possa realizar todas as suas tarefas de gerenciamento de aplicativo Web dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços Azure automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Aqui estão alguns exemplos de gerenciamento de serviços de aplicativo com a automação:

* [Scripts para gerenciar aplicativos Web](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar o Azure Web App, siga estes links para saber mais sobre a automação do Azure.

* Consulte a automação Azure [Introdução Tutorial](../automation/automation-first-runbook-graphical.md)
