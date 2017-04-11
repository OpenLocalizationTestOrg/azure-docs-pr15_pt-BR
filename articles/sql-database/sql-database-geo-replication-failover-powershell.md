<properties 
    pageTitle="Iniciar um failover planejado ou para o banco de dados do SQL Azure com o PowerShell | Microsoft Azure" 
    description="Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Iniciar um failover planejado ou para o banco de dados do SQL Azure com o PowerShell



> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra como iniciar um failover planejado ou para o banco de dados do SQL com o PowerShell. Para configurar replicação geográfica, consulte [Configurar geográfica-replicação de banco de dados do SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Iniciar um failover planejado

Use o cmdlet **Set-AzureRmSqlDatabaseSecondary** com o parâmetro de **Failover** para promover um banco de dados secundário para se tornar o novo principal banco de dados, rebaixar o principal existente para se tornar um secundário. Esta funcionalidade foi projetada para um failover planejado, como durante exercícios de recuperação de desastres e requer que o banco de dados principal estejam disponíveis.

O comando executa o fluxo de trabalho a seguir:

1. Alterne temporariamente replicação para modo síncrono. Isso fará com que todas as transações pendentes ser transferidas para o secundário.

2. Alterne as funções dos dois bancos de dados na parceria replicação geográfica.  

Esta sequência garante que os dois bancos de dados são sincronizados antes das funções mudar e, portanto, sem perda de dados. Não há um curto período durante o qual os dois bancos de dados estão indisponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Toda a operação deve levar menos de um minuto para ser concluída em circunstâncias normais. Para obter mais informações, consulte [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Esse cmdlet retornará quando o processo de alternar o banco de dados secundário para primária for concluído.

O comando a seguir alterna as funções do banco de dados chamado "mydb" no servidor "srv2" sob o grupo de recursos "rg2" ao primário. A imagem principal original à qual "db2" estava conectado irá alternar para secundário após dois bancos de dados são totalmente sincronizados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Em casos raros é possível que a operação não consegue concluir e pode aparecer não responde. Nesse caso, o usuário pode chamar o comando de failover forçar (failover planejado) e aceitar a perda de dados.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar um failover planejado do banco de dados principal ao banco de dados secundário


Você pode usar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com parâmetros **– Failover** e **- AllowDataLoss** para promover um banco de dados secundário para se tornar o novo banco de dados principal de maneira planejada, forçar o rebaixamento do primário existente se tornar um secundário uma vez quando o banco de dados principal não está mais disponível.

Esta funcionalidade foi projetada para recuperação de dados quando a restauração de disponibilidade do banco de dados é fundamental e perda de dados é aceitável. Quando forçada failover é invocado, o banco de dados secundário especificado imediatamente se torna o banco de dados principal e começa a aceitar transações de gravação. Assim que o banco de dados principal original é capaz de reconectar-se com o novo banco de dados principal após a operação de failover forçada, um backup incremental é levado para o banco de dados principal original e o banco de dados principal antigo é feito em um banco de dados secundário para o novo banco de dados principal; posteriormente, é simplesmente uma réplica do novo principal.

Mas porque ponto no tempo restaurar não é suportado em bancos de dados secundários, se desejar dados de recuperação confirmados no banco de dados principal antigo que não foram replicados para o novo banco de dados principal, você deve contratar CSS para restaurar um banco de dados para o backup de log conhecidos.

> [AZURE.NOTE] Se o comando é emitido quando principal e secundário estão on-line principal antigo tornará o novo secundário imediatamente sem sincronização de dados. Se o principal é comprometimento transações quando o comando é emitido perda de dados podem ocorrer.


Se o banco de dados principal tem vários secundários o comando parcialmente terá êxito. O secundário no qual o comando foi executado se tornará primário. O principal antigo Entretanto permanecerão primário, ou seja, as duas cores primárias terminarão em estado inconsistente e conectados por um link de replicação suspenso. O usuário terá que reparar manualmente essa configuração usando uma API de "Remover secundário" em qualquer um desses bancos de dados principal.


O comando a seguir alterna as funções do banco de dados denominada "mydb" principal quando o principal não está disponível. A imagem principal original à qual "mydb" estava conectado irá alternar para secundário depois que ele estiver online novamente. Nesse momento, a sincronização pode resultar em perda de dados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Próximas etapas   

- Após o failover, verifique os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo principal. Para obter detalhes, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md).
- Para saber a recuperação após um desastre usando replicação geográfica ativo, incluindo pré e postar etapas de recuperação e executar uma análise de recuperação de desastres, consulte [Exercícios de recuperação de desastres](sql-database-disaster-recovery.md)
- Para uma postagem de blog de Sasha Nosov sobre Active geográfica replicação, consulte [Destaque sobre novos recursos de replicação de localização geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações sobre a criação de aplicativos de nuvem para usar replicação de localização geográfica ativa, consulte [criação de aplicativos de nuvem para continuidade de negócios usando replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como usar replicação geográfica ativo com pools de banco de dados elástica, consulte [Pool elástica estratégias de recuperação de desastres](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma visão geral de negócios continurity, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
