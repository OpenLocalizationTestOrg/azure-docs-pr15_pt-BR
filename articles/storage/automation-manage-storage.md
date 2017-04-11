<properties
    pageTitle="Gerenciar o armazenamento do Azure usando automação do Azure"
    description="Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar o armazenamento do Azure em escala."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Gerenciar o armazenamento do Azure usando automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento de armazenamento do Azure blobs, tabelas e filas.


## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos. Com a automação do Azure, demorada, manuais, propensa e frequentemente repetidas tarefas podem ser automatizadas para aumentar a eficiência e confiabilidade e reduzem o tempo de valor para a sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades à medida que cresce de sua organização. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT / DevOps equipe se concentrar no trabalho que adiciona business valor movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Como automação Azure ajudar a gerenciar o armazenamento do Azure?

Armazenamento do Azure pode ser gerenciado na automação do Azure usando cmdlets do PowerShell que estão disponível no [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automação Azure tem esses cmdlets do PowerShell de armazenamento disponíveis prontos para uso, para que você possa realizar todas as suas blob, tabela e tarefas de gerenciamento de fila dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

A [Galeria de runbook de automação do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de comunidade e equipe de produto para começar a automação do gerenciamento de armazenamento do Azure, outros serviços do Azure e 3º sistemas de terceiros. Galeria runbooks incluem:

 * [Remover bolhas de armazenamento do Azure que são determinados dias antigo usando o serviço de automação](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Baixe um Blob do armazenamento do Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Faça o backup de todos os discos para uma única VM Azure ou para todas as VMs em um serviço de nuvem](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar o armazenamento do Azure blobs, tabelas e filas, siga estes links para saber mais sobre a automação do Azure.

Consulte o tutorial de automação do Azure [Criando ou importando um runbook no Azure automação](../automation/automation-creating-importing-runbook.md).
 
