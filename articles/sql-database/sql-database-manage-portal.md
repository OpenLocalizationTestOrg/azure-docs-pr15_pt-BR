<properties
    pageTitle="Gerenciar o banco de dados do SQL Azure usando o Portal do Azure | Microsoft Azure"
    description="Saiba como usar o Portal do Azure para gerenciar um banco de dados relacional na nuvem usando o Portal do Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Gerenciar bancos de dados do SQL Azure usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

O [portal do Azure](https://portal.azure.com/) permite que você criar, monitorar e gerenciar servidores e bancos de dados do SQL Azure. Este artigo fornece uma descrição rápida e links para os detalhes das tarefas mais comuns.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Exibir seus bancos de dados SQL Azure, servidores e pools

Para exibir os serviços disponíveis do banco de dados SQL, clique em **mais serviços**e digite **SQL** na caixa de pesquisa:

![Banco de dados SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Como criar ou exibir bancos de dados do SQL Azure?

Para abrir a lâmina de **bancos de dados SQL** , clique em **bancos de dados SQL**e clique o banco de dados que você quer trabalhar com ou clique em **+ Add** para criar um banco de dados do SQL. Para obter detalhes, consulte [criar um banco de dados SQL em minutos usando o portal do Azure](sql-database-get-started.md).


![Bancos de dados SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Como criar ou exibir servidores do SQL Azure?

Para abrir a lâmina de **servidores SQL** , clique em **servidores SQL**e, em seguida, clique no servidor que você quer trabalhar com ou clique **+ Adicionar** para criar um SQL server. Para obter detalhes, consulte [criar um banco de dados SQL em minutos usando o portal do Azure](sql-database-get-started.md).

![Servidores SQL](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Como criar ou exibir pools Elástico SQL?

Para abrir a lâmina **pools Elástico SQL** , clique **pools Elástico SQL**e, em seguida, clique no pool que você quer trabalhar com ou clique em **+ Add** para criar um pool. Para obter detalhes, consulte [criar um pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-create-portal.md).

![Pools Elástico SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Como atualizar ou exibir configurações de banco de dados do SQL?

Para exibir ou atualizar as configurações do seu banco de dados, clique na configuração desejada na lâmina banco de dados SQL:


![Configurações de banco de dados SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Como posso encontrar um nome de servidor totalmente qualificado de bancos de dados do SQL?

Para exibir o nome do seu servidor de bancos de dados, clique em **Visão geral** na lâmina **banco de dados SQL** e observe o nome do servidor:


![Configurações de banco de dados SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Como gerencio as regras de firewall para controlar o acesso ao meu SQL server e o banco de dados?

Para exibir, criar ou atualizar regras de firewall, clique em **Definir firewall server** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [Configurar uma regra de firewall de nível de servidor de banco de dados do SQL Azure usando o portal do Azure](sql-database-configure-firewall-settings.md).


![regras de firewall](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Como posso alterar meu nível de desempenho ou nível de serviço do banco de dados SQL?


Para atualizar o nível de desempenho ou nível de serviço de um banco de dados do SQL, clique em **nível de preços (escala DTUs)** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL](sql-database-scale-up.md).


![níveis de preços](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Como configurar a auditoria e ameaças detecção para um banco de dados do SQL?

Para configurar a detecção de ameaças e auditoria para um banco de dados do SQL, clique **detecção de auditoria e ameaças** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [Introdução ao auditoria de banco de dados do SQL](sql-database-auditing-get-started.md)e [começar a usar a detecção de ameaças de banco de dados do SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Como posso configurar dados dinâmicos masking para um banco de dados do SQL?

Para configurar dados dinâmicos masking para um banco de dados do SQL, clique em **dados dinâmicos masking** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [Introdução ao SQL banco de dados dinâmicos dados Masking](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Como posso configurar a criptografia de dados transparente (TDE) para um banco de dados do SQL?

Para configurar a criptografia de dados transparente para um banco de dados do SQL, clique em **criptografia de dados transparente** na lâmina de **banco de dados SQL** . Para obter detalhes, consulte [Habilitar TDE em um banco de dados usando o portal](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Como exibir ou alterar o tamanho máximo de um banco de dados do SQL?

Para exibir ou alterar o tamanho de um banco de dados do SQL, clique em **tamanho do banco de dados** no **banco de dados SQL** blade. Atualize o tamanho máximo de um banco de dados, alterando o nível de desempenho ou nível de serviço. Para obter detalhes, consulte [alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Como monitorar e melhorar o desempenho de um banco de dados do SQL?

Para monitorar e melhorar as características de desempenho de um banco de dados do SQL, clique em **Visão geral sobre desempenho** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [Visão de desempenho de banco de dados do SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Como posso configurar replicação geográfica?

Para configurar replicação geográfica para um banco de dados do SQL, clique em **Replicação geográfica** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [Configurar geográfica-replicação de banco de dados SQL Azure com o portal do Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Como fazer failover para um banco de dados do SQL replicado geográfica?

Failover para um secundário replicado geográfica, **Localização geográfica replicação** no **banco de dados SQL** blade clique **Failover**. Para obter detalhes, consulte [Iniciar um failover planejado ou para o banco de dados SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Como copiar um banco de dados do SQL?

Para copiar um banco de dados do SQL, clique em **Copiar** no **banco de dados SQL** blade. Para obter detalhes, consulte [Copiar um banco de dados do SQL Azure usando o portal do Azure](sql-database-copy-portal.md).


![Configurações de banco de dados SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Como posso arquivar um banco de dados do SQL Azure para um arquivo BACPAC?

Para criar um BACPAC de um banco de dados do SQL, clique em **Exportar** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [arquivamento um banco de dados do SQL Azure para um arquivo BACPAC usando o portal do Azure](sql-database-export.md).


![Exportação de banco de dados SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Como restaurar um banco de dados SQL para um ponto anterior no tempo?

Para restaurar um banco de dados do SQL, clique em **Restaurar** na lâmina **banco de dados SQL** . Para obter detalhes, consulte [restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o portal do Azure](sql-database-point-in-time-restore-portal.md).


![Configurações de banco de dados SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Como criar um banco de dados do SQL Azure de um arquivo BACPAC?

Para criar um banco de dados SQL de um arquivo BACPAC, clique em **importar banco de dados** na lâmina **do SQL server** . Para obter detalhes, consulte [Importar um arquivo BACPAC para criar um banco de dados do SQL Azure](sql-database-import.md).


![Do SQL server](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Como restaurar um banco de dados do SQL excluído?

Para restaurar um banco de dados do SQL excluído, clique em **excluídos bancos de dados** na lâmina **do SQL server** (SQL server que continha o banco de dados que foi excluído). Para obter detalhes, consulte [restaurar um banco de dados excluído do SQL Azure usando o portal do Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Como posso excluir um banco de dados do SQL?

Para excluir um banco de dados do SQL, clique em **Excluir** na lâmina **banco de dados SQL** . 

![Configurações de banco de dados SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Recursos adicionais

- [Banco de dados SQL](sql-database-technical-overview.md)
- [Monitorar e gerenciar um pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-manage-portal.md)
