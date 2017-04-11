<properties
    pageTitle="Solucionar problemas de backup e restauração com o banco de dados do SQL Azure"
    description="Saiba como recuperar um banco de dados de nuvem de erros e interrupções usando backups e réplicas no Azure SQL Database."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Restaurar um banco de dados para um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar uma interrupção do Centro de dados

Banco de dados SQL mantém réplicas do banco de dados para que você pode recuperar de interrupções e erros de usuário. Opções disponíveis dependem o nível de serviço de banco de dados e as opções que você escolher. Consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md) para obter detalhes e considerações de design.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Para restaurar um banco de dados a um ponto anterior no tempo
1.  No [Portal do Azure](https://azure.microsoft.com/), clique em **bancos de dados SQL**.
2.  Selecione o banco de dados na lista e clique em **Restaurar**.
3.  Digite um novo nome para o banco de dados, escolha a data e tempo para restaurar a partir de e, em seguida, clique em **criar.**
4.  Fazer ajustes de aplicativo conforme necessário para referenciar o novo banco de dados. Consulte [recuperar um banco de dados para um ponto no tempo](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Para restaurar um banco de dados excluído acidentalmente
1.  No [Portal do Azure](https://azure.microsoft.com/), clique em **servidores SQL**.
2.  Selecione o servidor que hospedado o banco de dados da lista.
3.  No blade Server, role para baixo e clique em **bancos de dados excluídos**.
4.  Selecione o banco de dados para restaurar e clique em **criar**.
5.  Fazer ajustes de aplicativo conforme necessário para referenciar o novo banco de dados. Consulte [recuperar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Recuperar uma interrupção comerciais
Com bancos de dados Standard e Premium, se você configurar replicado geográfica secundários, você pode recuperar usando estas secundários. Isso lhe dá a capacidade de restaurar um banco de dados com menos potencial de perda de dados. Para obter detalhes, consulte [recuperar um banco de dados do SQL Azure usando backups automatizados do banco de dados](sql-database-disaster-recovery.md) .

Azure também fornece backups de cada banco de dados em uma região diferente (um backup geográfica redundantes). Você pode criar um novo banco de dados desses backups, que é chamado restauração geográfica, mas é provável que você vai enfrentar perda de dados se você confiar neste método sozinho.

**Para recuperar um banco de dados usando a restauração de localização geográfica:**

- No [Portal do Azure](https://azure.microsoft.com/), clique em **novo**, clique em **dados e armazenamento**, clique em **Banco de dados SQL**e selecione **Backup** como fonte de banco de dados. Para obter detalhes, consulte [recuperar um banco de dados do SQL Azure de uma interrupção](sql-database-disaster-recovery.md) .