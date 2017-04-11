<properties
   pageTitle="Soluções de recuperação de desastres - banco de dados ativo geográfica replicação do SQL em nuvem | Microsoft Azure"
   description="Aprenda a criar soluções de recuperação de desastres de nuvem para planejamento de continuidade de negócios usando replicação geográfica para backup de dados de aplicativo com o Azure SQL Database."
   keywords="nuvem de recuperação de dados, soluções de recuperação de desastres, backup de dados do aplicativo, replicação de localização geográfica, planejamento de continuidade de negócios"
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
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Criar um aplicativo de recuperação de nuvem usando replicação geográfica ativo no banco de dados SQL


> [AZURE.NOTE] [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) agora está disponível para todos os bancos de dados em todas as camadas.



Saiba como usar [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) no banco de dados SQL para os aplicativos do banco de dados de design e apresentam resiliência regionais falhas e interrupções graves. Para planejamento de continuidade de negócios, considere a topologia de implantação do aplicativo, o contrato de nível de serviço que você está destinando, latência de tráfego e custos. Neste artigo, vamos examinar os padrões de aplicativos comuns e aborde as vantagens e desvantagens de cada opção. Para obter informações sobre Active geográfica replicação com Pools elástica, consulte [Pool elástica estratégias de recuperação de desastres](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Padrão de design 1: implantação ativo-passivo para recuperação de nuvem com um banco de dados localizado

Essa opção é mais adequada para aplicativos com as seguintes características:

+ Instância ativa em uma única região Azure
+ Dependência forte acesso de leitura e gravação (RW) para dados
+ Conectividade de região de cruz entre a lógica do aplicativo e o banco de dados não for aceitável devido a latência e o tráfego de custo    

Nesse caso, a topologia de implantação do aplicativo é otimizada para lidar com desastres regionais quando todos os componentes de aplicativo são afetados e precisam failover como uma unidade. Para redundância geográfica, a lógica do aplicativo e o banco de dados são replicados em outra região, mas elas não são usadas para a carga de trabalho do aplicativo sob as condições normais. O aplicativo na região secundário deve ser configurado para usar uma cadeia de conexão de SQL no banco de dados secundário. Gerenciador de tráfego está configurado para usar o [método de roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Gerenciador de tráfego Azure](../traffic-manager/traffic-manager-overview.md) é usada neste artigo ilustração somente para fins de. Você pode usar qualquer solução de balanceamento de carga que suporta o método de roteamento de failover.    

Além das instâncias do aplicativo principal, você deve considerar Implantando um pequeno [aplicativo de função de trabalho](cloud-services-choose-me.md#tellmecs) que monitora seu banco de dados principal pela emissão periódicos comandos (LIN) de somente leitura de T-SQL. Você pode usá-lo para acionar automaticamente failover, para gerar um alerta no console de administração do seu aplicativo, ou ambos. Para garantir que o serviço de monitoramento de mensagens não é afetada pelo interrupções de toda a região, você deve implantar as monitoramento instâncias do aplicativo em cada região e ter cada instância conectada ao banco de dados principal na região principal e o banco de dados secundário na região local. 

> [AZURE.NOTE] Ambos os aplicativos de monitoramento deve estar ativa e investigar bancos de dados principais e secundários. O último pode ser usado para detectar uma falha na região secundário e alerta quando o aplicativo não está protegido.     

O diagrama a seguir mostra essa configuração antes de uma interrupção.

![Configuração de localização geográfica-replicação de banco de dados do SQL. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Após uma interrupção na região primária, o aplicativo de monitoramento detecta que o banco de dados principal não é acessível e registra um alerta. Dependendo do seu SLA de aplicativo, você pode decidir quantos testes de monitoramento consecutivas falhar antes de declarar uma interrupção de banco de dados. Para obter failover coordenado do ponto de extremidade do aplicativo e o banco de dados, você deve ter o aplicativo de monitoramento executar as seguintes etapas:

1. [Atualizar o status do principal ponto de extremidade](https://msdn.microsoft.com/library/hh758250.aspx) para disparar failover de ponto de extremidade.
2. Ligue para o banco de dados secundário para [Iniciar o failover de banco de dados](sql-database-geo-replication-portal.md).

Após o failover, o aplicativo processará as solicitações de usuário na região secundário, mas permanecerá localizado com o banco de dados porque o banco de dados principal agora estará na região secundário. Este cenário é ilustrado próximo diagrama. Em todos os diagramas, linhas sólidas indicam conexões ativas, linhas pontilhadas indicam conexões suspensos e sinais de parada indicar gatilhos de ação.


![Replicação geográfica: Failover banco de dados secundário. Backup de dados do aplicativo.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se uma interrupção acontece na região secundário, o link de replicação entre o principal e o banco de dados secundário está suspenso e o aplicativo de monitoramento registra um alerta que o banco de dados principal é exposto. O desempenho do aplicativo não é afetado, mas o aplicativo opera expostos e, portanto, ambas as regiões correndo alto risco caso falharem sucessivamente.

> [AZURE.NOTE] Recomendamos somente configurações de implantação com uma única região de DR. Isso ocorre porque a maioria das regiões Azure tem duas regiões. Essas configurações não serão proteger seu aplicativo contra uma falha grave de ambas as regiões. Em um evento provável de falha, você pode recuperar seus bancos de dados em uma região de terceiro usando a [operação de restauração de localização geográfica](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Depois que a interrupção é reduzida, o banco de dados secundário é automaticamente sincronizado com o principal. Durante a sincronização, o desempenho do primário pode ser afetado ligeiramente dependendo da quantidade de dados que precisam ser sincronizado. O diagrama a seguir ilustra uma interrupção na região secundário.

![Banco de dados secundário sincronizados com o principal. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


As principais **vantagens** desse padrão de design são:

+ A cadeia de conexão de SQL é definida durante a implantação do aplicativo em cada região e não altera após failover.
+ O desempenho do aplicativo não é afetado pelo failover como o aplicativo e o banco de dados sempre estão localizados.

A principal **desvantagem** é que a instância do aplicativo redundantes na região secundário só é usada para recuperação de dados.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Padrão de design 2: implantação ativa para balanceamento de carga de aplicativo
Essa opção de recuperação de desastres de nuvem é mais adequada para aplicativos com as seguintes características:

+ Alta taxa de leituras de banco de dados para gravações
+ Latência de gravação do banco de dados não afeta a experiência do usuário final  
+ Lógica de somente leitura pode ser separada da lógica de leitura e gravação, usando uma cadeia de conexão diferentes
+ Lógica de somente leitura não dependem de dados totalmente sendo sincronizados com as atualizações mais recentes  

Se seus aplicativos tiverem essas características, balanceamento de carga as conexões de usuário final entre várias instâncias do aplicativo em diferentes regiões pode melhorar o desempenho e a experiência do usuário final. Para implementar o balanceamento de carga, cada região deve ter uma instância ativa do aplicativo com a lógica de (RW) de leitura e gravação conectada ao banco de dados principal na região primária. A lógica de somente leitura (LIN) deve ser conectada a um banco de dados secundário na mesma região como a instância do aplicativo. Gerenciador de tráfego deve ser configurado para usar o [roteamento de alternada](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou [roteamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md) com o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) ativado para cada instância do aplicativo.

Como padrão #1, você deve considerar implantar um aplicativo de monitoramento semelhante. Mas diferentemente padrão #1, o aplicativo de monitoramento não será responsável por disparo o failover de ponto de extremidade.

> [AZURE.NOTE] Embora esse padrão use mais de um banco de dados secundário, somente um dos secundários seria usado para failover pelos motivos observados anteriormente. Porque este padrão requer acesso somente leitura para o secundário, ele requer replicação de localização geográfica ativa.

Gerenciador de tráfego deve ser configurado para desempenho roteamento para direcionar as conexões de usuário para a instância de aplicativo mais próxima de localização geográfica do usuário. O diagrama a seguir ilustra essa configuração antes de uma interrupção.
![Sem interrupção: desempenho roteamento para mais próximo de aplicativo. Replicação de localização geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se uma queda de banco de dados for detectada na região primária, você inicia o failover do banco de dados principal para uma das regiões secundários, alterando o local do banco de dados principal. O Gerenciador de tráfego automaticamente exclui o ponto de extremidade off-line da tabela de roteamento, mas continua rotear o tráfego de usuário final para as instâncias de onlinehttps restantes. Porque o banco de dados principal agora está em uma região diferente, todas as instâncias onlinehttps devem alterar seu cadeia de conexão de SQL de leitura e gravação para se conectar ao novo principal. É importante que você faça essa alteração antes de iniciar o failover de banco de dados. As sequências de conexão de SQL somente leitura devem permanecer inalteradas conforme eles sempre apontam para o banco de dados na mesma região. As etapas de failover são:  

1. Alterar cadeias de caracteres de conexão SQL de leitura e gravação para apontar para o novo principal.
2. Ligue para o banco de dados secundário designado em ordem para [failover de iniciação de banco de dados](sql-database-geo-replication-portal.md).

O diagrama a seguir ilustra a nova configuração após o failover.
![Configuração após failover. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

No caso de uma falha em uma das regiões secundários, o Gerenciador de tráfego remove automaticamente o ponto de extremidade offline na região da tabela de roteamento. O canal de replicação para o banco de dados secundário naquela região está suspenso. Porque as regiões restantes receber o tráfego de usuário adicionais nesse cenário, o desempenho do aplicativo é afetado durante a interrupção. Depois que a interrupção é reduzida, o banco de dados secundário na região afetado imediatamente é sincronizado com o principal. Durante a sincronização desempenho do primário pode ser afetado ligeiramente dependendo da quantidade de dados que precisam ser sincronizado. O diagrama a seguir ilustra uma interrupção em uma das regiões secundários.

![Interrupção na região secundário. Nuvem recuperação - replicação geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

A principal **vantagem** desse padrão de design é que você pode dimensionar a carga de trabalho do aplicativo em vários secundários para atingir o desempenho ideal do usuário final. As **vantagens e desvantagens** dessa opção são:

+ Conexões de leitura e gravação entre as instâncias do aplicativo e o banco de dados tem variações de latência e custo
+ Desempenho do aplicativo é afetado durante a interrupção
+ Instâncias do aplicativo são necessárias para alterar dinamicamente a cadeia de conexão SQL após failover de banco de dados.  

> [AZURE.NOTE] Uma abordagem semelhante pode ser usada para transferir especializadas cargas de trabalho como trabalhos, ferramentas de business intelligence ou backups de relatório. Normalmente essas cargas de trabalho consumam recursos significativos de banco de dados, portanto, é recomendável que você designa um banco de dados secundários para eles com o nível de desempenho que coincidem com a carga de trabalho antecipada.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Padrão de design 3: implantação ativo-passivo para preservação de dados  
Essa opção é mais adequada para aplicativos com as seguintes características:

+ Qualquer perda de dados é comercial alto risco. O failover de banco de dados só pode ser usado como último recurso se a interrupção for permanente.
+ O aplicativo pode operar em "modo somente leitura" por um período de tempo.

Nesse padrão, o aplicativo alterna para o modo somente leitura quando conectado ao banco de dados secundário. A lógica do aplicativo na região primária está localizada no banco de dados principal e funciona no modo de leitura e gravação (RW). A lógica do aplicativo na região secundário está localizada no banco de dados secundária e está pronta para operar no modo somente leitura (LIN).  Gerenciador de tráfego deve ser configurado para usar o [roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) com o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) ativado para ambas as instâncias do aplicativo.

O diagrama a seguir ilustra essa configuração antes de uma interrupção.
![Implantação de ativo-passivo antes do failover. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando o Gerenciador de tráfego detecta uma falha de conectividade para a região principal, ele alterna automaticamente o tráfego de usuário para instância do aplicativo na região secundário. Com esse padrão, é importante que você **não** iniciar failover de banco de dados após a interrupção for detectada. O aplicativo na região secundário está ativado e opera no modo somente leitura usando o banco de dados secundário. Isso é ilustrado pelo diagrama a seguir.

![Interrupção: O aplicativo em modo somente leitura. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Depois que a interrupção na região primária é reduzida, o Gerenciador de tráfego detecta a restauração de conectividade na região primária e alterna o tráfego de usuário volta para a instância do aplicativo na região primária. Essa instância do aplicativo currículos e opera no modo de leitura / gravação usando o banco de dados principal.

> [AZURE.NOTE] Porque este padrão requer acesso somente leitura para o secundário requer replicação de localização geográfica ativa.

No caso de uma falha na região secundário, o Gerenciador de tráfego marca o ponto de extremidade do aplicativo na região primária como degradado e o canal de replicação está suspenso. No entanto, essa interrupção não afeta o desempenho do aplicativo durante a interrupção. Depois que a interrupção é reduzida, o banco de dados secundário imediatamente é sincronizado com o principal. Durante a sincronização desempenho do primário pode ser afetado ligeiramente dependendo da quantidade de dados que precisam ser sincronizado.

![Interrupção: Banco de dados secundário. Recuperação de nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Esse padrão de design tem várias **vantagens**:

+ Ele evita perda de dados durante as interrupções temporárias.
+ Ele não exige implantar um aplicativo de monitoramento conforme a recuperação for disparada pelo Gerenciador de tráfego.
+ Tempo de inatividade depende somente rapidez Gerenciador de tráfego detecta a falha de conectividade, que é configurável.

As **compensações** são:

+ Aplicativo deve ser capaz de operar em modo somente leitura.
+ Ele é necessário para dinamicamente alternar quando ele está conectado a um banco de dados somente leitura.
+ Continuação das operações de leitura e gravação requer a recuperação da região primária, o que significa acesso a dados completo pode ser desabilitado por horas ou até mesmo dias.

> [AZURE.NOTE] No caso de uma interrupção de serviço permanente na região, você deve manualmente ativar failover de banco de dados e aceitar a perda de dados. O aplicativo será funcional na região secundário com acesso de leitura / gravação ao banco de dados.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: escolher o design de um aplicativo de recuperação de nuvem

Sua estratégia de recuperação de desastres de nuvem específico pode combinar ou estender esses padrões de design para melhor atender às necessidades de seu aplicativo.  Conforme mencionado anteriormente, a estratégia escolhida baseia-se no SLA desejar oferecer aos seus clientes e a topologia de implantação do aplicativo. Para ajudar a orientar sua decisão, a tabela a seguir compara as escolhas com base na perda de dados estimados ou recuperação aponte objetivo (RPO) e o tempo de recuperação (ERTER) estimado.

| Padrão | RPO | ERTER
| :--- |:--- | :---
| Implantação de ativo-passivo para recuperação de dados com acesso de banco de dados localizados | Acesso de leitura e gravação < 5 s | Tempo de detecção de falha + API de failover chamar + teste de verificação de aplicativo
| Implantação ativa para balanceamento de carga de aplicativo | Acesso de leitura e gravação < 5 s | Tempo de detecção de falha + failover API chamada + cadeia de conexão de SQL alterar + teste de verificação de aplicativo
| Implantação de ativo-passivo para preservação de dados | Acesso somente leitura < 5 s acesso de leitura e gravação = zero | Acesso somente leitura = tempo de detecção de falha de conectividade + teste de verificação do aplicativo <br>Acesso de leitura e gravação = tempo para reduzir a interrupção

## <a name="next-steps"></a>Próximas etapas

- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
- Para obter informações sobre Active geográfica replicação com Pools elástica, consulte [Pool elástica estratégias de recuperação de desastres](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
