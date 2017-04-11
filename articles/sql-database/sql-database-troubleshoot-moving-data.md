<properties
    pageTitle="Mova bancos de dados entre servidores, entre assinaturas e e sair do Azure."
    description="Etapas rápidas para copiar, mover e migrar dados e bancos de dados no Azure SQL Database."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Mover bancos de dados entre servidores, entre assinaturas e e sair do Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover um banco de dados para um servidor diferente na mesma assinatura
- No [Portal do Azure](https://portal.azure.com), clique em **bancos de dados SQL**, selecione um banco de dados na lista e clique em **Copiar**. Para obter mais detalhes, consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover um banco de dados entre assinaturas
- No [Portal do Azure](https://portal.azure.com), clique em **SQL servers** e selecione o servidor que hospeda seu banco de dados da lista. Clique em **Mover**e, em seguida, escolha os recursos para mover e a inscrição para mover para.

## <a name="to-migrate-a-sql-database-into-azure"></a>Migrar um banco de dados SQL para Azure
- Determine a compatibilidade de banco de dados e escolha o método de migração certo com base nas suas necessidades. Siga as diretrizes e opções migrar [um banco de dados do SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Para criar uma cópia de um banco de dados para uso fora do Azure
- [Exporte um arquivo BACPAC.](sql-database-export.md)
