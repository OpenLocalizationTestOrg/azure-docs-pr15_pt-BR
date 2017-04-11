<properties
   pageTitle="Continuidade de negócios em nuvem - restaurar um banco de dados excluído - banco de dados SQL | Microsoft Azure"
   description="Saiba mais sobre a restauração de no momento, que permite que você reverter um banco de dados do SQL Azure para um ponto anterior no tempo (até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar um banco de dados do SQL Azure usando backups automatizados do banco de dados

Banco de dados SQL fornece três opções para recuperação de banco de dados usando [que backups automatizados do banco de dados SQL](sql-database-automated-backups.md). Você pode restaurar um banco de dados dos backups iniciado pelo serviço durante o [período de retenção](sql-database-service-tiers.md) para:

- Um novo banco de dados no mesmo servidor lógico recuperado para um determinado ponto no tempo no período de retenção. 
- Um banco de dados no mesmo servidor lógico recuperado para o horário de exclusão para um banco de dados excluído.
- Um novo banco de dados em qualquer servidor lógico em qualquer região recuperado para os backups diários mais recentes no armazenamento de blob replicado geográfica (ar-GRS).

Você também pode usar o [banco de dados SQL backups automatizados](sql-database-automated-backups.md) para criar um [banco de dados copiar](sql-database-copy.md) em qualquer servidor lógico em qualquer região transacionalmente consistente com o banco de dados atual do SQL. Você pode usar cópia do banco de dados e [Exportar para um BACPAC](sql-database-export.md) para arquivar uma cópia transacionalmente consistente de um banco de dados para armazenamento de longo prazo além do seu período de retenção, ou para transferir uma cópia do seu banco de dados para um local ou máquina virtual do Azure instância do SQL Server.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups automatizados do banco de dados é afetado por um número de fatores: 
 - O tamanho do banco de dados
 - O nível de desempenho do banco de dados
 - O número de logs de transação envolvidos
 - A quantidade de atividade que precisa ser reproduzidos para recuperar para o ponto de restauração
 - A largura de banda de rede se a restauração é para uma região diferente 
 - O número de solicitações simultâneas restauração estão sendo processadas na região de destino. 
 
 Para um banco de dados muito grande e/ou ativo a restauração pode levar horas. Se houver interrupção prolongada em uma região, é possível que haja grande número de solicitações de restauração geográfica estão sendo processadas por outras regiões. Se houver um grande número de solicitações isso pode aumentar o tempo de recuperação para bancos de dados nessa região. A maioria dos banco de dados restaura concluído dentro de 12 horas.

 Não há nenhuma funcionalidade interna em massa restauração. O [Azure SQL Database: recuperação de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script é um exemplo de uma maneira de realizar essa tarefa.

> [AZURE.IMPORTANT] Para recuperar usando backups automatizados, você deve ser um membro da função Colaborador do SQL Server na assinatura ou ser o proprietário da assinatura. Você pode recuperar usando o portal do Azure, PowerShell ou a API REST. Você não pode usar Transact-SQL. 

## <a name="point-in-time-restore"></a>Restaurar no momento

No momento restaurar permite restaurar um banco de dados existente como um novo banco de dados para um ponto anterior no tempo no mesmo servidor lógico usando [que backups automatizados do banco de dados SQL](sql-database-automated-backups.md). Você não pode substituir o banco de dados existente. Você pode restaurar um ponto anterior no tempo usando o [portal do Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauração no momento: Portal Azure](sql-database-point-in-time-restore-portal.md)
- [Restaurar no momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

O banco de dados pode ser restaurado em qualquer nível de desempenho ou elástica pool. É necessário garantir que você tem uma cota DTU suficiente no servidor lógico ou pool elástica. Tenha em mente que a restauração cria um novo banco de dados e que o nível de desempenho e nível de serviço do banco de dados restaurado pode ser diferente o estado atual do banco de dados ao vivo. Após a conclusão, o banco de dados restaurado é um normal totalmente acessível online banco de dados cobrado a taxas normais com base em seu nível de desempenho e nível de serviço. Você não provoca encargos até concluir a restauração do banco de dados.

Você geralmente pode restaurar um banco de dados para um ponto de earler para fins de recuperação. Ao fazer isso, você pode tratar o banco de dados restaurado como substituto do banco de dados original ou usá-lo para recuperar dados e, em seguida, atualize o banco de dados original. 

- ***Substituição de banco de dados:*** Se o banco de dados restaurado destina-se como substituto do banco de dados original, você deve verificar o nível de desempenho e/ou nível de serviço são apropriado e dimensionar o banco de dados, se necessário. Você pode renomear o banco de dados original e dê ao banco de dados restaurado o nome original usando o comando ALTER DATABASE em T-SQL. 
- ***Recuperação de dados:*** Se você planeja recuperar dados de banco de dados restaurado para recuperar de um erro de usuário ou aplicativo, você precisará separadamente escrever e executar qualquer scripts de recuperação de dados que você precisa para extrair dados de banco de dados restaurado o banco de dados original. Embora a operação de restauração pode levar muito tempo para ser concluída, o banco de dados restaurando ficará visível na lista de banco de dados em todo. Se você excluir o banco de dados durante a restauração, ele cancelará a operação e você não será cobrado do banco de dados que não foi concluída a restauração. 

Para obter informações detalhadas sobre como usar restauração no momento para recuperar de erros de usuário e do aplicativo, consulte restauração [Point-in-Time](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Restauração do banco de dados excluídos

Restauração do banco de dados excluídos permite restaurar um banco de dados excluído para o horário de exclusão para um banco de dados excluído no mesmo servidor lógico usando [que backups automatizados do banco de dados SQL](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Se você excluir uma instância do servidor de banco de dados de SQL Azure, todos seus bancos de dados também são excluídos e não poderão ser recuperados. Não há nenhum suporte para restaurar um servidor excluído neste momento.

Você pode usar a mesma ou um novo nome de banco de dados para o banco de dados restaurado. Você pode usar o [portal do Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou o [REST (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Excluídos restauração do banco de dados: portal do Azure](sql-database-restore-deleted-database-portal.md)
- [Excluídos restauração do banco de dados: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Restaurar a localização geográfica

Localização geográfica restauração permite que você restaurar um banco de dados SQL em qualquer servidor em qualquer região Azure do mais recente replicado geográfica [backup automatizado de diário](sql-database-automated-backups.md). Localização geográfica restauração utiliza um backup geográfica redundante como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou Data Center está inacessível devido a uma interrupção. Você pode usar o [portal do Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), ou o [REST (createMode = recuperação)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Geográfica restauração: Portal Azure](sql-database-geo-restore-portal.md)
- [Localização geográfica restauração: PowerShell](sql-database-geo-restore-powershell.md)

Localização geográfica restauração é a opção de recuperação padrão quando o banco de dados está indisponível por causa de um incidente na região em que o banco de dados está hospedado. Se um incidente de grande escala em um resulta de região em indisponibilidade do seu aplicativo de banco de dados, você pode usar localização geográfica restauração para restaurar um banco de dados do backup mais recente para um servidor em qualquer outra região. Todos os backups são replicados geográfica e pode ter um atraso entre quando o backup estiver sendo usado e localização geográfica-replicados para o Azure blob em uma região diferente. Este atraso pode ser até uma hora para que em caso de um desastre pode haver para cima a perda de dados de 1 hora, ou seja, RPO de até 1 hora. A seguir mostra a restauração do banco de dados do último backup diariamente.

![restaurar a localização geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Para obter informações detalhadas sobre como usar localização geográfica restauração para recuperar uma interrupção, consulte [recuperar uma interrupção](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Localização geográfica restauração está disponível com todos os níveis de serviço, é mais básico das soluções de recuperação de desastres disponíveis no banco de dados do SQL com o maior RPO e tempo de recuperação de estimativa (ERTER). Para bancos de dados básicos com tamanho máximo de 2 GB geográfica restauração fornece uma solução de DR razoável com um ERTER de 12 horas. Standard ou Premium bancos de dados maiores, se significativamente menores tempos de recuperação forem desejados ou para reduzir a probabilidade de perda de dados, você deve considerar usando replicação de localização geográfica ativa. Replicação de localização geográfica ativa oferece uma muito menor RPO e ERTER como somente requer que você iniciar um failover para um secundário continuamente replicado. Para obter detalhes, consulte [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Executar programaticamente recuperação usando backups automatizados

Conforme discutido acima, em addiition portal do Azure, recuperação de banco de dados pode ser executada através de usando o PowerShell do Azure e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtém um ou mais bancos de dados.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Obtém um banco de dados excluído que você pode restaurar.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtém um backup geográfica redundantes de um banco de dados.|
|[Restaurar-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaura um banco de dados do SQL.|
||||

### <a name="rest-api"></a>API REST

|API|Descrição|
|---|-----------|
|[RESTO (createMode = recuperação)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaura um banco de dados|
|[Obter criar ou atualizar Status de banco de dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retorna o status durante uma operação de restauração|
||||



## <a name="summary"></a>Resumo

Backups automáticos protegem seus bancos de dados do usuário e erros do aplicativo, exclusão acidental de banco de dados e interrupções prolongadas. Essa solução de custo zero zero-administração está disponível com todos os bancos de dados do SQL. 

## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
