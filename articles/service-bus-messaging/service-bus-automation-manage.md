<properties
    pageTitle="Gerenciar barramento de serviço do Azure usando automação Azure | Microsoft Azure"
    description="Saiba como usar o serviço de automação do Azure para gerenciar barramento de serviço do Azure."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Gerenciando barramento de serviço do Azure usando automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento de barramento de serviço do Azure.

## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](../automation/automation-intro.md) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos e configuração de estado desejado. Usando o Azure automação, manual, repetido, demorada e propensa tarefas podem ser automatizadas para aumentar a confiabilidade, a eficiência e tempo de valor para sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT e DevOps funcionários se concentrar no trabalho que adiciona o valor de negócios, movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Como o Azure automação pode ajudar gerenciar barramento de serviço do Azure?

Você pode gerenciar barramento de serviço com a automação do Azure usando a [API do serviço barramento restante](https://msdn.microsoft.com/library/azure/mt639375.aspx). Dentro de automação do Azure, é possível executar scripts do PowerShell para realizar muitas das tarefas barramento de serviço usando a API REST. Você também pode emparelhar essas chamadas API REST na automação do Azure com os cmdlets para outros serviços do Azure, automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Aqui estão alguns exemplos de como usar o PowerShell para gerenciar barramento de serviço do Azure:

* [Cmdlets personalizados do PowerShell para gerenciar filas de barramento de serviço do Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Como criar filas barramento de serviço, tópicos e assinaturas usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Criar Namespaces de barramento de serviço do Azure usando o PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

O módulo do PowerShell para trabalhar com barramento de serviço Azure em automação runbooks pode ser baixado da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar o barramento de serviço do Azure, siga estes links para saber mais sobre a automação do Azure.

* Consulte a automação Azure [Introdução Tutorial](../automation/automation-first-runbook-graphical.md)
* Consulte como [Gerenciar barramento de serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
