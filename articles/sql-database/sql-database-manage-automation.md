<properties
    pageTitle="Gerenciar bancos de dados do SQL Azure usando automação Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar bancos de dados do SQL Azure em escala."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Gerenciar bancos de dados do SQL Azure usando automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento dos seus bancos de dados do SQL Azure.


## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos. Com a automação do Azure, tarefas de execução demorada, manuais, propensa e frequentemente repetidas podem ser automatizadas para aumentar a confiabilidade, a eficiência e tempo de valor para sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades à medida que cresce de sua organização. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT / DevOps equipe se concentrar no trabalho que adiciona business valor movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como o Azure automação pode ajudar gerenciar bancos de dados do SQL Azure?

Banco de dados do SQL Azure podem ser gerenciado na automação do Azure usando os [cmdlets do PowerShell de banco de dados do SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) que estão disponíveis nas [Ferramentas do PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automação Azure tem esses cmdlets do PowerShell de banco de dados do SQL Azure disponíveis prontos para uso, para que você possa realizar todas as suas tarefas de gerenciamento de banco de dados do SQL dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Automação Azure também tem a capacidade de se comunicar com servidores SQL diretamente, emitir comandos SQL usando o PowerShell.

A [Galeria de runbook de automação do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de comunidade e equipe de produto para começar a automação do gerenciamento de bancos de dados do SQL Azure, outros serviços do Azure e 3º sistemas de terceiros. Galeria runbooks incluem:

 * [Executar consultas SQL em um banco de dados do SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Dimensionar verticalmente (para cima ou para baixo) um banco de dados do SQL Azure em um cronograma](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Truncar uma tabela do SQL se seu banco de dados abordagens seu tamanho máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Indexar as tabelas em um banco de dados do SQL Azure se eles são altamente fragmentados](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar bancos de dados do SQL Azure, siga estes links para saber mais sobre a automação do Azure.

- [Visão geral de automação Azure](../automation/automation-intro.md)
- [Minha primeira runbook](../automation/automation-first-runbook-graphical.md)
- [Mapa de aprendizagem de automação Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Automação Azure: Seu agente SQL na nuvem](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
