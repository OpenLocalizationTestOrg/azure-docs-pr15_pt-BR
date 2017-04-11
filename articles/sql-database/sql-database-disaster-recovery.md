<properties
   pageTitle="Recuperação de banco de dados SQL | Microsoft Azure"
   description="Saiba como recuperar um banco de dados de uma falta de Demand ou falha com o Azure SQL banco de dados ativo geográfica replicação e recursos de restauração de localização geográfica."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar um banco de dados do SQL Azure ou failover para um secundário

Banco de dados do SQL Azure oferece os seguintes recursos para recuperar uma interrupção:

- [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md)
- [Restaurar a localização geográfica](sql-database-recovery-using-backups.md#point-in-time-restore)

Para saber mais sobre cenários de continuidade de negócios e os recursos de suporte a esses cenários, consulte [continuidade de negócios](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Preparar-se para o evento de uma interrupção

Para o sucesso com a recuperação para outra região de dados usando replicação de localização geográfica ativa ou backups redundantes de localização geográfica, você precisa preparar um servidor em outra data center interrupção se tornar o servidor primário deve a necessidade surgir bem como bem definiu as etapas documentadas e testadas para garantir uma recuperação suave. Estas etapas preparatórias incluem:

- Identifica o servidor lógico em outra região para tornar-se o servidor primário. Com o Active geográfica replicação, este será pelo menos uma e talvez cada um dos servidores secundários. Para restaurar de localização geográfica, isso geralmente será um servidor na [região par](../best-practices-availability-paired-regions.md) para a região na qual o banco de dados está localizado.
- Identifique e, opcionalmente, defina as regras de firewall de nível de servidor exigia em usuários acessem o novo banco de dados principal.
- Determine como você vai para redirecionar usuários para o novo servidor principal, como alterando cadeias de caracteres de conexão ou alterando entradas DNS.
- Identifique e, opcionalmente, crie o logon que deve estar presentes no banco de dados mestre no novo servidor primário e garantir estes inícios de sessão têm as permissões apropriadas no banco de dados mestre, se houver. Para obter mais informações, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md)
- Identifique as regras de alerta que deverá ser atualizado para mapear para o novo banco de dados principal.
- A configuração de auditoria no banco de dados principal atual do documento
- Execute uma [análise de recuperação de dados](sql-database-disaster-recovery-drills.md). Para simular uma interrupção para restauração de localização geográfica, você pode excluir ou renomear o banco de dados de origem para causar a falha de conectividade do aplicativo. Para simular uma interrupção de localização geográfica-replicação ativa, você pode desativar o aplicativo web ou máquina virtual conectado ao banco de dados ou failover o banco de dados para causar falhas de connectity de aplicativo.

## <a name="when-to-initiate-recovery"></a>Quando iniciar recuperação

A operação de recuperação afeta o aplicativo. Ela requer alterando a cadeia de conexão de SQL ou redirecionamento usando DNS e pode resultar em perda de dados permanente. Portanto, ele deve ser feito somente quando a interrupção for provavelmente durar mais de objetivo de tempo de recuperação do aplicativo. Quando o aplicativo for implantado em produção, você deve executar o monitoramento regular da integridade aplicativo e use os seguintes pontos de dados para que a recuperação é garantia de declaração:

1.  Falha de conectividade permanente da camada de aplicativo ao banco de dados.
2.  O portal do Azure mostra um alerta sobre um incidente na região com impacto amplo.
3.  O servidor de banco de dados do Azure SQL está marcado como degradado.

Dependendo de sua tolerância de aplicativo para o tempo de inatividade e possíveis responsabilidade de negócios, você pode considerar as seguintes opções de recuperação.

Use o [Banco de dados recuperável obter](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de localização geográfica replicado restauração mais recente.

## <a name="wait-for-service-recovery"></a>Espere a recuperação de serviço

O trabalho de equipes Azure cuidadosamente para restaurar a disponibilidade do serviço como rapidamente possível, mas dependendo raiz fazer com que ele pode levar horas ou dias.  Se o seu aplicativo pode suportar o tempo de inatividade significativo, que você pode simplesmente aguardar a recuperação ser concluída. Nesse caso, nenhuma ação de sua parte é necessária. Você pode ver o status atual do serviço no nosso [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/). Após a recuperação da região de disponibilidade do seu aplicativo será restaurada.

## <a name="failover-to-geo-replicated-secondary-database"></a>Failover replicado geográfica banco de dados secundário

Se o tempo de inatividade do seu aplicativo pode resultar em responsabilidade de negócios você deve estar usando replicado geográfica bancos de dados em seu aplicativo. Ele permitirá que o aplicativo para restaurar rapidamente disponibilidade em uma região diferente no caso de uma interrupção. Saiba como [Configurar replicação geográfica](sql-database-geo-replication-portal.md).

Para restaurar a disponibilidade dos bancos de dados, você precisa iniciar o failover para o secundário replicado geográfica usando um dos métodos suportados.

Use um dos seguintes guias failover para um banco de dados de secundário replicado geográfica:

- [Failover para um secundário replicado geográfica usando o Portal do Azure](sql-database-geo-replication-portal.md)
- [Failover para um secundário replicado geográfica usando o PowerShell](sql-database-geo-replication-powershell.md)
- [Failover para um secundário replicado geográfica usando T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Recuperar usando a restauração de localização geográfica

Se o tempo de inatividade do seu aplicativo não resultar em responsabilidade de negócios, você pode usar restauração geográfica como um método para recuperar seus bancos de dados do aplicativo. Ele cria uma cópia do banco de dados de seu backup geográfica redundantes mais recente.

Use um dos seguintes guias geográfica-restaurar um banco de dados em uma nova região:

- [Localização geográfica-restaurar um banco de dados para uma nova região usando o Portal do Azure](sql-database-geo-restore-portal.md)
- [Localização geográfica-restaurar um banco de dados para uma nova região usando o PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurar seu banco de dados após a recuperação

Se você estiver usando replicação geográfica failover ou restauração geográfica recuperar uma interrupção, você deve garantir que a conectividade com os novos bancos de dados está configurada corretamente para que a função de aplicativo normal pode ser reiniciada. Esta é uma lista de verificação de tarefas para preparar sua produção recuperado do banco de dados.

### <a name="update-connection-strings"></a>Atualizar cadeias de caracteres de Conexão

Porque seu banco de dados recuperado residirão em um servidor diferente, você precisa atualizar a cadeia de conexão do seu aplicativo para apontar para o servidor.

Para obter mais informações sobre a alteração de cadeias de caracteres de conexão, consulte a linguagem de desenvolvimento apropriadas para sua [biblioteca de conexão](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar regras de Firewall

Você precisa certificar-se de que as regras de firewall configuradas no servidor e no banco de dados correspondam aqueles que foram configurados no servidor primário e o banco de dados principal. Para obter mais informações, consulte [como: configurar definições de Firewall (Azure SQL Database)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurar o logon e usuários de banco de dados

Você precisa certificar-se de que todos os logons usados pelo seu aplicativo existem no servidor que hospeda seu banco de dados recuperado. Para obter mais informações, consulte [Configuração de segurança para replicação de localização geográfica](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Você deve configurar e testar suas regras de firewall do servidor e logon (e suas permissões) durante uma análise de recuperação de desastres. Esses objetos de nível de servidor e sua configuração podem não estar disponíveis durante a interrupção.

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetria

Você precisa certificar-se de que as configurações de regra de alerta existentes são atualizadas para mapear para o banco de dados recuperado e o servidor diferente.

Para obter mais informações sobre regras de alerta de banco de dados, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [Integridade do serviço de controle](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Habilitar a auditoria

Se for necessário fazer auditoria acesse seu banco de dados, você precisa habilitar auditoria após a recuperação de banco de dados. Um bom indicador que auditoria é necessária é que aplicativos cliente usam cadeias de caracteres de conexão segura em um padrão de *. database.secure.windows.net. Para obter mais informações, consulte [Introdução ao auditoria de banco de dados do SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Próximas etapas

- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade de negócios, consulte [cenários de continuidade](sql-database-business-continuity.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
