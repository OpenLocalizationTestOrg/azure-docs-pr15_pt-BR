<properties
   pageTitle="Restaurar um depósito de dados do SQL Azure (API REST) | Microsoft Azure"
   description="Tarefas de API REST para restaurar um depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restaurar um depósito de dados do SQL Azure (API REST)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [RESTANTE][]

Neste artigo, você aprenderá como restaurar um depósito de dados do SQL Azure usando a API REST.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada depósito de dados do SQL é hospedado por um SQL server (por exemplo, myserver.database.windows.net) que possui uma cota DTU padrão.  Antes de você pode restaurar um depósito de dados do SQL, verifique se o SQL server tem cota suficiente DTU restante do banco de dados que está sendo restaurado. Para saber como calcular DTU necessário ou para solicitar mais DTU, consulte [solicitar uma alteração de cota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados:

1. Obter a lista dos pontos de restauração do banco de dados usando a operação de obter pontos de restauração de banco de dados.
2. Começar a restauração usando a operação de [solicitação de restauração do banco de dados de criar][] .
3. Acompanhe o status de sua restauração usando a operação de [status da operação de banco de dados][] .

>[AZURE.NOTE] Concluída a restauração, você pode configurar o seu banco de dados recuperado pela seguinte [configurar seu banco de dados após a recuperação][].

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído:

1.  Liste todos os bancos de dados excluídos restauráveis usando a operação de [lista restauráveis ignorados bancos de dados do][] .
2.  Obter os detalhes do banco de dados excluídos que você deseja restaurar usando a operação [obter restaurável banco de dados ignorado][] .
3.  Começar a restauração usando a operação de [solicitação de restauração do banco de dados de criar][] .
4.  Acompanhe o status de sua restauração usando a operação de [status da operação de banco de dados][] .

>[AZURE.NOTE] Para configurar seu banco de dados após a conclusão da restauração, consulte [Configurar o seu banco de dados após a recuperação][]. 


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Azure SQL Database, leia a [Visão geral de continuidade de negócios do Azure SQL Database][].

<!--Image references-->

<!--Article references-->
[Visão geral sobre o continuidade de negócios banco de dados do SQL Azure]: ./sql-database-business-continuity.md
[Solicitar uma alteração de cota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar seu banco de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTANTE]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Criar solicitação de restauração do banco de dados]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Status da operação de banco de dados]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter o banco de dados ignorado restaurável]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista restaurável descartados bancos de dados]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
