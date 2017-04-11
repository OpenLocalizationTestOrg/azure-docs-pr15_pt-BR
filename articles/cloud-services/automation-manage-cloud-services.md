<properties
    pageTitle="Gerenciar serviços de nuvem do Azure usando automação Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar serviços de nuvem Azure em escala."
    services="cloud-services, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/20/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-cloud-services-using-azure-automation"></a>Gerenciando serviços de nuvem do Azure usando automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento dos seus serviços de nuvem Azure.

## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos. Com a automação do Azure, tarefas de execução demorada, manuais, propensa e frequentemente repetidas podem ser automatizadas para aumentar a confiabilidade, a eficiência e tempo de valor para sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades à medida que cresce de sua organização. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT / DevOps equipe se concentrar no trabalho que adiciona business valor movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.


## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como o Azure automação pode ajudar gerenciar serviços de nuvem Azure?

Serviços de nuvem Azure podem ser gerenciados na automação do Azure usando cmdlets do PowerShell que estão disponível nas [Ferramentas do PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automação Azure tem esses cmdlets de PowerShell de serviço de nuvem disponíveis prontos para uso, para que você possa realizar todas as suas tarefas de gerenciamento de serviço de nuvem dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Alguns usos de exemplo de automação do Azure para gerenciar serviços de nuvem do Azure incluem:

- [Implantação de contínua de um serviço de nuvem sempre cscfg ou cspkg é atualizado no armazenamento de Blob do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
- [Reinicializar instâncias de serviço de nuvem em paralelo, um domínio de atualização por vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar serviços de nuvem Azure, siga estes links para saber mais sobre a automação do Azure.

- [Visão geral de automação Azure](../automation/automation-intro.md)
- [Minha primeira runbook](../automation/automation-first-runbook-graphical.md)
- [Mapa de aprendizagem de automação Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
 
