<properties 
    pageTitle="Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL | Microsoft Azure" 
    description="Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="CarlRabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra como iniciar failover para um banco de dados do SQL secundário usando Transact-SQL. Para configurar replicação geográfica, consulte [Configurar geográfica-replicação de banco de dados do SQL Azure](sql-database-geo-replication-transact-sql.md).



Para iniciar o failover, você precisa do seguinte:

- Um logon que é DBManager no sistema principal, tiver db_ownership de banco de dados local que você irá geográfica replicar, e ser DBManager nos servidores parceiro para o qual você configurará replicação geográfica.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Iniciar um failover planejado promover um banco de dados secundário para se tornar o novo principal

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados principal de maneira planejada, rebaixar o principal existente para se tornar um secundário. Esta política é executada no banco de dados mestre no servidor lógico Azure SQL Database em que reside o localização geográfica-secundário banco de dados replicado que está sendo promovido. Esta funcionalidade foi projetada para failover planejado, como durante drills DR e requer que o banco de dados principal estejam disponíveis.

O comando executa o fluxo de trabalho a seguir:

1. Alterna temporariamente replicação para o modo síncrono, fazendo com que todas as transações pendentes ser transferidas para o secundário e bloqueando a todas as novas transações;

2. Alterna as funções dos dois bancos de dados a parceria de replicação geográfica.  

Esta sequência garante que os dois bancos de dados são sincronizados antes das funções mudar e, portanto, sem perda de dados. Não há um curto período durante o qual os dois bancos de dados estão indisponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Se o banco de dados principal tiver vários bancos de dados secundários, o comando reconfigurar automaticamente as outras secundárias para se conectar ao novo principal.  Toda a operação deve levar menos de um minuto para ser concluída em circunstâncias normais. Para obter mais informações, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Níveis de serviço](sql-database-service-tiers.md).


Use as etapas a seguir para iniciar um failover planejado.

1. No Management Studio, conecte ao servidor lógico Azure SQL Database em que reside um banco de dados de secundário replicado localização geográfica.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para mudar o banco de dados secundário para a função principal.

        ALTER DATABASE <MyDB> FAILOVER;

4. Clique em **Executar** para executar a consulta.

>[AZURE.NOTE] Em casos raros, é possível que a operação não consegue concluir e pode aparecer presa. Nesse caso, o usuário pode executar o comando de failover força e aceitar perda de dados.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar um failover planejado do banco de dados principal ao banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados principal de maneira planejada, forçar o rebaixamento do primário existente se tornar um secundário uma vez quando o banco de dados principal não está mais disponível. Esta política é executada no banco de dados mestre no servidor lógico Azure SQL Database em que reside o localização geográfica-secundário banco de dados replicado que está sendo promovido.

Esta funcionalidade foi projetada para recuperação de dados quando a restauração de disponibilidade do banco de dados é fundamental e perda de dados é aceitável. Quando forçada failover é invocado, o banco de dados secundário especificado imediatamente se torna o banco de dados principal e começa a aceitar transações de gravação. Assim que o banco de dados principal original é capaz de reconectar-se com o novo banco de dados principal, um backup incremental é levado para o banco de dados principal original e o banco de dados principal antigo é feito em um banco de dados secundário para o novo banco de dados principal; posteriormente, é simplesmente uma sincronização réplica do novo principal.

No entanto, como ponto no tempo restaurar não é suportado nos bancos de dados secundários, se o usuário deseja recuperar dados comprometidos com o banco de dados principal antigo que não tinha sido replicado para o novo banco de dados principal antes do failover forçado ocorreu, o usuário precisará entre em contato com o suporte para recuperar esse dados perdidos.

Se o banco de dados principal tiver vários bancos de dados secundários, o comando reconfigurar automaticamente as outras secundárias para se conectar ao novo principal.

Use as etapas a seguir para iniciar um failover não planejado.

1. No Management Studio, conecte ao servidor lógico Azure SQL Database em que reside um banco de dados de secundário replicado localização geográfica.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para mudar o banco de dados secundário para a função principal.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Clique em **Executar** para executar a consulta.

>[AZURE.NOTE] Se o comando é emitido quando principal e secundário estão on-line principal antigo tornará o novo secundário imediatamente sem sincronização de dados. Se o principal é comprometimento transações quando o comando é emitido perda de dados podem ocorrer.



## <a name="next-steps"></a>Próximas etapas   

- Após o failover, verifique os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo principal. Para obter detalhes, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md).
- Para saber a recuperação após um desastre usando replicação geográfica ativo, incluindo pré e postar etapas de recuperação e executar uma análise de recuperação de desastres, consulte [Recuperação de dados](sql-database-disaster-recovery.md)
- Para uma postagem de blog de Sasha Nosov sobre Active geográfica replicação, consulte [Destaque sobre novos recursos de replicação de localização geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações sobre a criação de aplicativos de nuvem para usar replicação de localização geográfica ativa, consulte [criação de aplicativos de nuvem para continuidade de negócios usando replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como usar replicação geográfica ativo com pools de banco de dados elástica, consulte [Pool elástica estratégias de recuperação de desastres](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma visão geral de negócios continurity, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
