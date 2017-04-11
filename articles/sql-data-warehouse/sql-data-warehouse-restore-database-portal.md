<properties
   pageTitle="Restaurar um depósito de dados do SQL Azure (Portal) | Microsoft Azure"
   description="Tarefas de portal Azure para restaurar um depósito de dados do SQL Azure."
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

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Restaurar um depósito de dados do SQL Azure (Portal)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [RESTANTE][]

Neste artigo, você aprenderá como restaurar um depósito de dados do SQL Azure usando o Portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada depósito de dados do SQL é hospedado por um SQL server (por exemplo, myserver.database.windows.net) que possui uma cota DTU padrão.  Antes de você pode restaurar um depósito de dados do SQL, verifique se o SQL server tem cota suficiente DTU restante do banco de dados que está sendo restaurado. Para saber como calcular DTU necessário ou para solicitar mais DTU, consulte [solicitar uma alteração de cota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados:

1. Faça logon [portal do Azure][]
2. No lado esquerdo da tela Selecione **Procurar** e selecione **servidores SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Navegue até seu servidor e selecioná-lo
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Localizar o depósito de dados do SQL que você deseja restaurar a partir de e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior da lâmina Data Warehouse, clique em **Restaurar**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Especificar um novo **nome de banco de dados**
7. Selecione o último **Ponto de restauração**
    1. Verifique se que você escolher o ponto de restauração mais recente.  Como pontos de restauração sejam mostrados no UTC, às vezes, a opção de padrão mostrada não é o ponto de restauração mais recente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Clique em **Okey**
9. O processo de restauração do banco de dados será iniciado e possível monitorar usando **notificações**

>[AZURE.NOTE] Concluída a restauração, você pode configurar o seu banco de dados recuperado pela seguinte [configurar seu banco de dados após a recuperação][].


## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído:

1. Faça logon [portal do Azure][]
2. No lado esquerdo da tela Selecione **Procurar** e selecione **servidores SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Navegue até seu servidor e selecioná-lo
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Role para baixo até a seção de operações na lâmina do seu servidor
5. Clique no bloco de **Bancos de dados excluídos**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Selecione o banco de dados excluído que você deseja restaurar
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Especificar um novo **nome de banco de dados**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Clique em **Okey**
9. O processo de restauração do banco de dados será iniciado e possível monitorar usando **notificações**

>[AZURE.NOTE] Para configurar seu banco de dados após a conclusão da restauração, consulte [Configurar o seu banco de dados após a recuperação][]. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Azure SQL Database, leia a [Visão geral de continuidade de negócios do Azure SQL Database][].

<!--Image references-->

<!--Article references-->
[Visão geral sobre o continuidade de negócios banco de dados do SQL Azure]: ./sql-database-business-continuity.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTANTE]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar seu banco de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Solicitar uma alteração de cota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/
