<properties
    pageTitle="Visão geral: ferramentas de gerenciamento para banco de dados SQL | Microsoft Azure"
    description="Compara ferramentas e opções de gerenciamento de banco de dados do SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Visão geral: ferramentas de gerenciamento para banco de dados SQL

Este tópico explora e compara ferramentas e opções para gerenciar bancos de dados do SQL Azure, por isso você pode escolher a ferramenta certa para o trabalho, seu negócio e você. Escolhendo a ferramenta certa depende de quantos bancos de dados que você gerenciar, a tarefa e quantas vezes uma tarefa é executada.

## <a name="azure-portal"></a>Portal do Azure

O [portal do Azure](https://portal.azure.com) é um aplicativo baseado na web, onde você pode criar, atualizar, excluir bancos de dados e servidores lógicos e monitorar a atividade de banco de dados. Esta ferramenta é ótima se você estiver começando a usar o Azure, gerenciar alguns bancos de dados, ou precisa fazer algo rapidamente.

Para obter mais informações sobre como usar o portal do, consulte [Gerenciar bancos de dados SQL usando o portal do Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e ferramentas de dados do SQL Server no Visual Studio

SQL Server Management Studio (SSMS) e SQL Server dados ferramentas (SSDT) são ferramentas de cliente que são executadas em seu computador para gerenciar e ao desenvolvimento de seu banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [tente usar SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muitos administradores de banco de dados estão familiarizados com SSMS, que pode ser usado com bancos de dados do SQL Azure. [Baixar a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e sempre usar a versão mais recente ao trabalhar com o Azure SQL Database. Para obter mais informações sobre como gerenciar seus bancos de dados do SQL Azure com SSMS, consulte [Gerenciar bancos de dados SQL usando o SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Sempre use a versão mais recente do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL.


## <a name="powershell"></a>PowerShell

Você pode usar o PowerShell para gerenciar bancos de dados e pools de banco de dados elástica e automatizar implantações de recurso Azure. A Microsoft recomenda esta ferramenta para gerenciar um grande número de bancos de dados e automatizar a implantação e alterações de recursos em um ambiente de produção.

Para obter mais informações, consulte [Gerenciar de banco de dados SQL com o PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Elástica ferramentas de banco de dados
Use as ferramentas de banco de dados elástica para executar ações como 

* Executar um script T-SQL em relação a um conjunto de bancos de dados usando um [trabalho Elástico](sql-database-elastic-jobs-overview.md)
* Movendo bancos de dados de vários locatários modelo a um modelo de locatário do único com a [ferramenta de mesclagem de divisão](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerenciar bancos de dados em um modelo de locatário do único ou um modelo de vários locatário usando a [biblioteca de cliente de dimensionamento Elástico](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciador de recursos do Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automação Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Agendador do Azure](https://azure.microsoft.com/documentation/services/scheduler/)