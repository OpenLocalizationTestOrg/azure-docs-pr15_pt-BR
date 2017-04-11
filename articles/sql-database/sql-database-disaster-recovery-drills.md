<properties 
   pageTitle="Exercícios de recuperação de desastres de banco de dados SQL | Microsoft Azure" 
   description="Aprenda a orientação e práticas recomendadas para usar o banco de dados do Azure SQL para realizar exercícios de recuperação de desastres que o ajude a mantém sua missão críticas aplicativos de negócios e apresentam resiliência falhas e interrupções." 
   services="sql-database" 
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Executar análise de recuperação de desastres

É recomendável que a validação de prontidão de aplicativo de fluxo de trabalho de recuperação é executada periodicamente. Verificando o comportamento do aplicativo e as implicações de perda de dados e/ou a interrupção que o failover envolve é uma boa prática de engenharia. Também é um requisito pela maioria dos padrões do setor como parte da certificação de continuidade de negócios.

Executar uma análise de recuperação de desastres consiste em:

- Simulando interrupção de camada de dados
- Recuperando 
- Validar recuperação de postagem de integridade de aplicativos

Dependendo de como o fluxo de trabalho para executar o exercício você [projetado seu aplicativo para continuidade de negócios](sql-database-business-continuity.md), pode variar. Abaixo, podemos descrevem as práticas recomendadas conduzindo um exercício de recuperação de desastres no contexto do Azure SQL Database. 

##<a name="geo-restore"></a>Restaurar a localização geográfica

Para evitar a perda de dados ao conduzir uma análise de recuperação de desastres, é recomendável executar o análise usando um ambiente de teste, criando uma cópia do ambiente de produção e usá-lo para verificar o fluxo de trabalho de failover do aplicativo.
 
####<a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode excluir ou renomear o banco de dados de origem. Isso causará falha de conectividade do aplicativo. 

####<a name="recovery"></a>Recuperação

- Execute a restauração de localização geográfica do banco de dados em um servidor diferente como descrito [aqui](sql-database-disaster-recovery.md). 
- Altere a configuração do aplicativo para se conectar aos bancos de dados recuperados e siga o guia de [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####<a name="validation"></a>Validação

- Conclua o exercício verificando a recuperação de postagem de integridade de aplicativo (isto é, cadeias de caracteres de conexão, logon, testes de funcionalidade básica ou outra parte validações dos procedimentos de aprovações de aplicativo padrão).

##<a name="geo-replication"></a>Replicação geográfica

Para um banco de dados que é protegido usando replicação geográfica o exercício de busca detalhada envolve failover planejado para o banco de dados secundário. O failover planejado garante que o principal e os bancos de dados secundários em sincronia quando as funções são alternadas. Ao contrário de failover não planejado, essa operação não resultará em perda de dados, para que o exercício pode ser executado no ambiente de produção. 

####<a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode desativar o aplicativo web ou máquina virtual conectado ao banco de dados. Isso resultará em falhas de conectividade para os clientes da web.

####<a name="recovery"></a>Recuperação

- Verifique se a configuração do aplicativo na região DR aponta para o primeiro secundário que se tornará o novo primário totalmente acessível. 
- Executar [planejado failover](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) para tornar o banco de dados secundário um novo primário
- Siga o guia de [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####<a name="validation"></a>Validação

- Conclua o exercício verificando a recuperação de postagem de integridade de aplicativo (isto é, cadeias de caracteres de conexão, logon, testes de funcionalidade básica ou outra parte validações dos procedimentos de aprovações de aplicativo padrão).


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre cenários de continuidade de negócios, consulte [cenários de continuidade](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
