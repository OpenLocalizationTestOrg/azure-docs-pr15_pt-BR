<properties
   pageTitle="Restaurar o SQL Data Warehouse | Microsoft Azure"
   description="Visão geral das opções de restauração de banco de dados para recuperar um banco de dados no depósito de dados do SQL Azure."
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
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Restaurar o SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [RESTANTE][]

SQL Data Warehouse oferece restaura locais e geográficas como parte de sua ocorrência de depósito de dados recursos de recuperação. Usar backups de depósito de dados para restaurar seu data warehouse para um ponto de restauração na região primária ou use backups geográfica redundantes restaurar para uma região geográfica diferente. Este artigo explica as especificações de restaurando um depósito de dados.

## <a name="what-is-a-data-warehouse-restore"></a>O que é a restauração de um depósito de dados?

A restauração de um depósito de dados é uma nova data warehouse que é criado a partir de um backup de um existente ou um depósito de dados excluídos. O depósito de dados restaurados recria o depósito de dados de backup em uma hora específica. Como o SQL Data Warehouse é um sistema distribuído, a restauração de um depósito de dados é criada a partir de muitos arquivos de backup que estão armazenados em bolhas Azure. 

Restauração do banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade de negócios porque ele cria novamente seus dados após danos acidentais ou exclusão.

Para obter mais informações, consulte:

-  [Backups de depósito de dados do SQL](sql-data-warehouse-backups.md)
-  [Visão geral de continuidade de negócios](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Pontos de restauração de depósito de dados

Como um benefício de usar o armazenamento do Azure Premium, SQL Data Warehouse usa instantâneos Blob de armazenamento do Azure para o depósito de dados principal de backup. Cada instantâneo tem um ponto de restauração que representa a hora em que o instantâneo iniciado. Para restaurar um depósito de dados, escolha um ponto de restauração e emitir um comando de restauração.  

SQL Data Warehouse sempre restaura o backup para um novo depósito de dados. Você pode manter o depósito de dados restaurados e atual, ou excluir um deles. Se você quiser substituir o depósito de dados atual com o depósito de dados restaurados, você pode renomeá-la.

Se você precisar restaurar um depósito de dados excluídos ou pausado, você pode [criar um tíquete](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restaurar geográfica redundantes

Se você estiver usando o armazenamento de localização geográfica redundantes, você pode restaurar o data warehouse para seu [Centro de dados pares](../best-practices-availability-paired-regions.md) em uma região geográfica diferentes. O data warehouse é restaurado desde o último backup diariamente. 

## <a name="restore-timeline"></a>Restaurar a linha do tempo

Você pode restaurar um banco de dados para qualquer ponto de restauração disponíveis nos últimos sete dias. Instantâneos iniciar cada quatro a oito horas e estão disponíveis para sete dias. Quando um instantâneo é mais de sete dias, ela expire e seu ponto de restauração não está mais disponível.

## <a name="restore-costs"></a>Restaurar os custos

A carga de armazenamento para o depósito de dados restaurados é cobrada na taxa de armazenamento do Azure Premium. 

Se você pausar um depósito de dados restaurados, cobrado para armazenamento a taxa de armazenamento do Azure Premium. A vantagem de pausa é cobrado não para os recursos de computação DWU.

Para obter mais informações sobre os preços de depósito de dados do SQL, consulte [Preços de depósito de dados do SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Usos para restaurar

O uso principal para restauração de depósito de dados é recuperar dados após perda de dados acidental ou corrupção.

Você também pode usar a restauração de depósito de dados para reter um backup por mais de sete dias. Depois que o backup é restaurado, você tem o data warehouse online e pode pausar indefinidamente para economizar custos de computação. O banco de dados pausado provoca encargos de armazenamento com a taxa de armazenamento do Azure Premium. 

## <a name="related-topics"></a>Tópicos relacionados

### <a name="scenarios"></a>Cenários

- Para obter uma visão de continuidade de negócios, consulte [Visão geral de continuidade de negócios](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Para executar a restauração de um depósito de dados, restaure usando:

- Portal do Azure, consulte [restaurar um depósito de dados usando o portal do Azure](sql-data-warehouse-restore-database-portal.md)
- Cmdlets do PowerShell, consulte [restaurar um depósito de dados usando cmdlets do PowerShell](sql-data-warehouse-restore-database-powershell.md)
- Coloque APIs, consulte [restaurar um depósito de dados usando as APIs REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTANTE]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
