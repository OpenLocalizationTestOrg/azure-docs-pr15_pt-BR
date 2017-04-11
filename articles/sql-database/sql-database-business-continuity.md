<properties
   pageTitle="Continuidade de negócios em nuvem - recuperação - banco de dados SQL do banco de dados | Microsoft Azure"
   description="Saiba como Azure SQL Database dá suporte a continuidade de negócios de nuvem e a recuperação de banco de dados e ajuda a manter os aplicativos de nuvem essenciais em execução."
   keywords="continuidade de negócios, continuidade de negócios de nuvem, recuperação de banco de dados, recuperação de banco de dados"
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
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Visão geral da continuidade de negócios com o banco de dados do Azure SQL

Esta visão geral descreve os recursos que o banco de dados do Azure SQL fornece para continuidade de negócios e recuperação de dados. Ele fornece opções, recomendações e tutoriais para recuperar de interrupções eventos que podem causar perda de dados ou o banco de dados e o aplicativo indisponível. Discussão inclui o que fazer quando um usuário ou erro de aplicativo afeta a integridade dos dados, uma região Azure tem uma interrupção ou seu aplicativo requerem manutenção. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Recursos de banco de dados SQL que você pode usar para fornecer continuidade de negócios

Banco de dados SQL fornece vários recursos de continuidade de negócios, incluindo backups automatizados e replicação de banco de dados opcional. Cada possui características diferentes de tempo de recuperação estimado (ERTER) e possibilidade de perda de dados para as transações recentes. Depois de compreender essas opções, você pode escolher entre eles - e, na maioria dos cenários, usá-los juntos para cenários diferentes. À medida que você desenvolve o seu plano de continuidade de negócios, você precisa compreender o tempo máximo aceitável antes do aplicativo recuperará totalmente após o evento de interrupção - esse é o seu objetivo de tempo de recuperação (RTO). Você também precisa compreender a quantidade máxima de dados as atualizações recentes (intervalo de tempo) o aplicativo pode suportar perder ao recuperar após o evento de interrupção - o objetivo de ponto de recuperação (RPO). 

A tabela a seguir compara o ERTER e RPO para os três cenários mais comuns.

| Recurso |  Nível básico | Nível padrão  | Nível de Premium |
|---|---|---|---|
| Ponto no tempo restaurar de backup | Qualquer ponto de restauração dentro de 7 dias   | Qualquer ponto de restauração em 35 dias  | Qualquer ponto de restauração em 35 dias |
Localização geográfica restauração de backups replicados geográfica | ERTER < 12h, RPO < 1h   | ERTER < 12h, RPO < 1h   | ERTER < 12h, RPO < 1h |
|Replicação de localização geográfica ativa | ERTER < 30s, RPO < 5s   | ERTER < 30s, RPO < 5s | ERTER < 30s, RPO < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>Usar backups de banco de dados para recuperar um banco de dados

Banco de dados SQL executa automaticamente uma combinação de banco de dados completo backups semanais diferenciais do banco de dados por hora backups e backups de log de transação cada cinco minutos para proteger seus negócios contra perda de dados. Esses backups são armazenados no armazenamento localmente redundante para 35 dias para bancos de dados nos níveis de serviço Standard e Premium e sete dias para bancos de dados no nível de serviço básico - consulte [níveis de serviço](sql-database-service-tiers.md) para obter mais detalhes sobre níveis de serviço. Se o período de retenção para seu nível de serviço não atender às suas necessidades de negócios, você pode aumentar o período de retenção, [alterando o nível de serviço](sql-database-scale-up.md). O banco de dados completo e diferencial backups também são replicados para um [Centro de dados pares](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção do Centro de dados - consulte [backups automáticos de banco de dados](sql-database-automated-backups.md) para obter mais detalhes.

Você pode usar esses backups automáticos de banco de dados para recuperar um banco de dados de vários eventos interrupções, tanto no seu data center para outro data center. Usando backups de banco de dados automática, a estimativa de tempo de recuperação depende de vários fatores, incluindo o número total de bancos de dados recuperando na mesma região ao mesmo tempo, o tamanho do banco de dados, o tamanho do log de transação e largura de banda de rede. Na maioria dos casos, o tempo de recuperação é menos de 12 horas. Ao recuperar a outra região de dados, a possibilidade de perda de dados é limitado para 1 hora pelo armazenamento de localização geográfica redundantes de backups de banco de dados diferencial por hora. 

> [AZURE.IMPORTANT] Para recuperar usando backups automatizados, você deve ser um membro da função Colaborador do SQL Server ou o proprietário da assinatura - consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). Você pode recuperar usando o portal do Azure, PowerShell ou a API REST. Você não pode usar Transact-SQL.

Use backups automatizados como o mecanismo de recuperação e continuidade de negócios se seu aplicativo:

- Não é considerado missão críticas.
- Não tem um SLA de vinculação, portanto, o tempo de inatividade de 24 horas ou mais não resultará em responsabilidade financeira.
- Tem uma baixa taxa de alteração de dados (baixos transações por hora) e perder até uma hora de alteração é uma perda de dados aceitável. 
- É o custo confidenciais. 

Se precisar de recuperação mais rápida, use [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) (discutido Avançar). Se você precisa ser capaz de recuperar dados de um período mais de 35 dias, considere o arquivamento do seu banco de dados regularmente para um arquivo BACPAC (um arquivo compactado que contém o esquema de banco de dados e os dados associados) armazenados em armazenamento de blob do Microsoft Azure ou em outro local de sua escolha. Para obter mais informações sobre como criar um arquivo de banco de dados transacionalmente consistente, consulte [criar uma cópia do banco de dados](sql-database-copy.md) e [Exportar a cópia do banco de dados](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Use replicação de localização geográfica ativa para reduzir o tempo de recuperação e limitar a perda de dados associada a uma recuperação

Além de usar backups de banco de dados para recuperação de banco de dados em caso de uma interrupção de negócios, você pode usar [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) para configurar um banco de dados para ter até quatro bancos de dados secundários legíveis nas regiões da sua escolha. Esses bancos de dados secundários são mantidos sincronizados com o banco de dados principal usando um mecanismo de replicação assíncrona. Esse recurso é usado para proteger contra interrupções de negócios em caso de uma interrupção do Centro de dados ou durante uma atualização do aplicativo. Localização geográfica-replicação ativo também pode ser usada para fornecer o melhor desempenho de consulta para consultas de somente leitura para usuários dispersos geograficamente.

Se o banco de dados principal fica offline inesperadamente, ou você precisa colocá-lo offline para atividades de manutenção, você pode rapidamente promover um secundário para se tornar o principal (também chamado de um failover) e configurar aplicativos para se conectar ao primário recentemente promovido. Com um failover planejado, há sem perda de dados. Com um failover não planejado, pode haver alguns pequena quantidade de perda de dados para as transações muito recentes devido a natureza da replicação assíncrona. Após um failover, você pode posterior failback - acordo com um plano ou quando o data center vem online novamente. Em todos os casos, os usuários experiência uma pequena quantidade de tempo de inatividade em precisam se reconectar. 

> [AZURE.IMPORTANT] Para usar replicação de localização geográfica ativa, você deve ser o proprietário da assinatura ou ter permissões administrativas no SQL Server. Você pode configurar e failover usando o portal do Azure, PowerShell ou a API REST usando permissões com assinaturas ou Transact-SQL usando permissões dentro do SQL Server.

Use replicação de localização geográfica ativa se seu aplicativo atender a qualquer um desses critérios:

- Missão é fundamental.
- Tem um contrato de nível de serviço (SLA) que não permitem 24 horas ou mais de tempo de inatividade.
- Tempo de inatividade resultará em responsabilidade financeira.
- Tem uma alta taxa de dados de alteração é alta e perder uma hora de dados não for aceitável.
- O custo adicional de replicação de localização geográfica ativa é menor que a responsabilidade financeira potencial e associada perda de negócios.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Recuperar um banco de dados após um erro de usuário ou aplicativo

* Ninguém é perfeito! Um usuário pode excluir acidentalmente alguns dados, solte inadvertidamente uma tabela importante ou até mesmo descartar um banco de dados inteiro. Ou, um aplicativo pode substituir acidentalmente dados bons com dados inválidos devido a um defeito de aplicativo. 

Nesse cenário, estas são as opções de recuperação.

### <a name="perform-a-point-in-time-restore"></a>Executar uma restauração no momento

Você pode usar os backups automatizados para recuperar uma cópia de seu banco de dados a um ponto de boa conhecido em tempo, desde que tempo está dentro do período de retenção de banco de dados. Depois que o banco de dados for restaurado, você pode substituir o banco de dados original com o banco de dados restaurado ou copie os dados necessários dos dados restaurados para o banco de dados original. Se o banco de dados usa replicação de localização geográfica ativa, é recomendável copiar os dados necessários da cópia restaurada no banco de dados original. Se você substituir o banco de dados original com o banco de dados restaurado, você precisará reconfigurar e sincronizar novamente replicação de localização geográfica ativa (que pode demorar algum tempo para um banco de dados grande). 

Para obter mais informações e para obter etapas detalhadas para restaurar um banco de dados a um ponto no tempo usando o portal do Azure ou usando o PowerShell, consulte [Restaurar no momento](sql-database-recovery-using-backups.md#point-in-time-restore). Você não poderá recuperar usando Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Se o banco de dados é excluído, mas não tenha sido excluído servidor lógico, você pode restaurar o banco de dados excluído para o ponto em que ele foi excluído. Isso restaura um backup do banco de dados no mesmo servidor SQL lógico da qual ele foi excluído. Você pode restaurá-lo usando o nome original ou fornecer um novo nome ou o banco de dados restaurado.

Para obter mais informações e para obter etapas detalhadas para restaurar um banco de dados excluído usando o portal do Azure ou usando o PowerShell, consulte [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore). Você não pode restaurar usando Transact-SQL.

> [AZURE.IMPORTANT] Se o servidor lógico for excluído, você não poderá recuperar um banco de dados excluído. 

### <a name="import-from-a-database-archive"></a>Importar de um arquivo de banco de dados

Se a perda de dados ocorreu fora do período de retenção atual para backups automatizados e você tiver sido arquivando o banco de dados, você pode [Importar um arquivo BACPAC arquivado](sql-database-import.md) para um novo banco de dados. Neste ponto, você pode substituir o banco de dados original com o banco de dados importado ou copie os dados necessários dos dados importados para o banco de dados original. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Recuperar um banco de dados para outra região de uma interrupção do Centro de dados regionais Azure

<!-- Explain this scenario -->

Embora seja rara, um centro de dados Azure pode ter uma interrupção. Quando ocorre uma interrupção, ele causa uma interrupção de negócios que só pode durar alguns minutos ou pode durar horas. 

- Outra alternativa é aguardar para ficar online novamente quando a interrupção do Centro de dados está acima do seu banco de dados. Isso funciona para aplicativos que podem ter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou avaliação gratuita que você não precisa trabalhar em constantemente. Quando um data center tem uma interrupção, não sabe por quanto tempo dura a interrupção, para que esta opção só funciona se não precisar seu banco de dados por um tempo.
- Outra opção é um dos failover para outra região de dados se você estiver usando replicação de localização geográfica ativa ou a recuperação usando backups de banco de dados redundantes de localização geográfica (restauração de localização geográfica). Failover leva apenas alguns segundos, enquanto a recuperação de backups leva horas.

Quando você executar a ação, quanto tempo leva para recuperar e quanto você provoca em caso de uma interrupção do Centro de dados de perda de dados depende de como você decide usar os recursos de continuidade de negócios discutidos acima em seu aplicativo. Na verdade, você pode optar por usar uma combinação de backups de banco de dados e replicação de localização geográfica ativa dependendo das suas necessidades de aplicativo. Para uma discussão sobre as considerações de design de aplicativo para bancos de dados autônomos e Elástico pools utilização desses recursos de continuidade de negócios, consulte [Design um aplicativo para recuperação de nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres elástica Pool](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As seções a seguir fornecem uma visão geral das etapas para recuperar usando backups de banco de dados ou replicação de localização geográfica ativa. Para obter etapas detalhadas incluindo planejamento de requisitos, etapas de recuperação de postagem e informações sobre como simular uma interrupção para executar uma análise de recuperação de desastres, consulte [recuperar um banco de dados SQL de uma interrupção](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma interrupção

Independentemente do recurso de continuidade de negócios que você usa, faça o seguinte:

- Identificar e preparar o servidor de destino, inclusive regras de firewall do nível do servidor, logon e permissões no nível do banco de dados mestre.
- Determinar como redirecionar clientes e aplicativos do cliente para o novo servidor
- Outras dependências, como configurações e alertas de auditoria do documento 
 
Se você não planeja e preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação demora adicionais e provavelmente também exigem solução de problemas de uma vez Enfatize - uma combinação incorreta.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Failover para um banco de dados de secundário replicado geográfica 

Se você estiver usando replicação de localização geográfica ativa como o mecanismo de recuperação, [forçar um failover para um secundário replicado localização geográfica](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Em segundos, o secundário é promovido o novo primário e está pronto para gravar novas transações e responder a perguntas - com apenas alguns segundos de perda de dados para os dados que tinham ainda não foram replicados. Para obter informações sobre como automatizar o processo de failover, consulte [Design um aplicativo para recuperação de nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Ao data center vem online novamente, você poderá failback para a imagem principal original (ou não).

### <a name="perform-a-geo-restore"></a>Executar uma restauração de localização geográfica 

Se você estiver usando backups automatizados de com replicação de armazenamento geográfica redundante como o mecanismo de recuperação, [Iniciar uma recuperação de banco de dados usando a restauração de localização geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). Recuperação geralmente ocorrerá dentro de 12 horas - com perda de dados de até uma hora determinada pelo quando o último backup diferencial por hora com duplicado e sendo usado. Até que a recuperação for concluída, o banco de dados não é possível registrar todas as transações ou responder a perguntas. 

> [AZURE.NOTE] Se o data center vier online novamente antes de migrar seu aplicativo os ao banco de dados recuperado, você simplesmente pode cancelar a recuperação.  

### <a name="perform-post-failover--recovery-tasks"></a>Lançar realizar failover / tarefas de recuperação 

Após a recuperação de qualquer mecanismo de recuperação, você deve executar as seguintes tarefas adicionais antes que os usuários e aplicativos são novamente em funcionamento:

- Clientes de redirecionamento e aplicativos cliente para o novo servidor e o banco de dados restaurado
- Certifique-se de que as regras de firewall apropriado do nível do servidor estão no lugar para usuários conectar (ou use [firewalls de nível de banco de dados](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Certifique-se de logon apropriado e permissões no nível do banco de dados mestre estão no lugar (ou use [contidos usuários](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurar auditoria, conforme apropriado
- Configurar alertas, conforme apropriado

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar um aplicativo com o tempo de inatividade mínimo

Às vezes, um aplicativo precisa ser colocado offline devido a manutenção planejada como uma atualização do aplicativo. [Gerenciar atualizações de aplicativo](sql-database-manage-application-rolling-upgrade.md) descreve como usar replicação de localização geográfica ativa para habilitar atualizações sem interrupção do seu aplicativo de nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação no caso de algo errado. Este artigo examina dois métodos diferentes de organizar o processo de atualização e aborda as vantagens e desvantagens de cada opção.

## <a name="next-steps"></a>Próximas etapas

Para uma discussão de considerações de design do aplicativo para bancos de dados autônomos e pools Elástico, consulte [Design de um aplicativo para recuperação de nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres elástica Pool](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






