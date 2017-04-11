<properties 
    pageTitle="Iniciar um failover planejado ou para o banco de dados do SQL Azure com o portal Azure | Microsoft Azure" 
    description="Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o portal do Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Iniciar um failover planejado ou para o banco de dados do SQL Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra como iniciar failover para um banco de dados do SQL secundário com o [portal do Azure](http://portal.azure.com). Para configurar replicação geográfica, consulte [Configurar geográfica-replicação de banco de dados do SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar o principal.  

1. Em Procurar o [portal do Azure](http://portal.azure.com) para o banco de dados principal a parceria de replicação geográfica.
2. Na lâmina banco de dados SQL, selecione **todas as configurações** > **Replicação geográfica**.
3. Na lista **SECUNDÁRIOS** , selecione o banco de dados que você deseja se tornar o novo primário e clique em **Failover**.

    ![failover][2]

4. Clique em **Sim** para iniciar o failover.

O comando trocarem imediatamente o banco de dados secundário para a função principal. 

Não há um curto período durante o qual os dois bancos de dados estão indisponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Se o banco de dados principal tiver vários bancos de dados secundários, o comando reconfigurar automaticamente as outras secundárias para se conectar ao novo principal. Toda a operação deve levar menos de um minuto para ser concluída em circunstâncias normais. 

>[AZURE.NOTE] Se o principal está online e confirmar transações quando o comando é emitido perda de dados pode ocorrer.


## <a name="next-steps"></a>Próximas etapas   

- Após o failover, verifique os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo principal. Para obter detalhes, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md).
- Para saber a recuperação após um desastre usando replicação geográfica ativo, incluindo pré e postar etapas de recuperação e executar uma análise de recuperação de desastres, consulte [Exercícios de recuperação de desastres](sql-database-disaster-recovery.md)
- Para uma postagem de blog de Sasha Nosov sobre Active geográfica replicação, consulte [Destaque sobre novos recursos de replicação de localização geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações sobre a criação de aplicativos de nuvem para usar replicação de localização geográfica ativa, consulte [criação de aplicativos de nuvem para continuidade de negócios usando replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como usar replicação geográfica ativo com pools de banco de dados elástica, consulte [Pool elástica estratégias de recuperação de desastres](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma visão geral de negócios continurity, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
