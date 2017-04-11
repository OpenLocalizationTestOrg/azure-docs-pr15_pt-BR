<properties 
   pageTitle="Criação de soluções de nuvem para a recuperação de dados usando a localização geográfica-replicação de banco de dados do SQL | Microsoft Azure"
   description="Saiba como projetar sua solução de nuvem para recuperação de dados, escolhendo o padrão de failover à direita."
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
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Estratégias de recuperação de desastres para aplicativos usando Pool elástica do banco de dados SQL 

Passar dos anos que aprendemos que os serviços de nuvem não são ocorrências de seguro e graves pode e irá acontecer. Banco de dados SQL fornece uma série de recursos para fornecer para a continuidade de negócios do seu aplicativo quando essas ocorrências ocorrerem. Bancos de dados [Elástico pools](sql-database-elastic-pool.md) e autônomo suportam o mesmo tipo de recursos de recuperação de desastres. Este artigo descreve várias estratégias de DR para pools elástica aproveitar esses recursos de continuidade de negócios do banco de dados SQL.

Para os fins deste artigo, usaremos o padrão de aplicativo de ISV SaaS canônico:

<i>Uma nuvem moderna baseado web application disposições um banco de dados SQL para cada usuário final. O ISV tem um grande número de clientes e, portanto, usa muitos bancos de dados, conhecidos como bancos de dados do locatário. Como os bancos de dados do locatário geralmente têm padrões de atividade imprevisíveis, o ISV usa um pool Elástico para tornar o banco de dados de custo muito previsíveis por longos períodos de tempo. O pool Elástico também simplifica o gerenciamento de desempenho quando a atividade de usuário picos. Além de bancos de dados do locatário o aplicativo também usa vários bancos de dados para gerenciar perfis de usuário, segurança, coletar padrões de uso etc. Disponibilidade dos locatários individuais não afeta a disponibilidade de aplicativos como todo. No entanto, a disponibilidade e o desempenho de bancos de dados de gerenciamento é essencial para a função do aplicativo e se os bancos de dados de gerenciamento estiverem offline todo o aplicativo está offline.</i>  

O restante do papel falaremos sobre as estratégias de DR abrangendo uma variedade de cenários dos aplicativos de inicialização confidenciais de custo para aqueles com os requisitos de disponibilidade rigorosas.  

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Custo de inicialização confidencial

<i>Sou um negócio de inicialização e estou muito confidencial de custo.  Quero simplificar a implantação e gerenciamento do aplicativo e estiver disposto a ter um SLA limitado para clientes individuais. Mas quero garantir que o aplicativo como um todo nunca está offline.</i>

Para satisfazer o requisito de simplicidade, você deve implantar todos os bancos de dados do locatário em um pool Elástico na região Azure de sua escolha e implantar os bancos de dados de gerenciamento como bancos de dados replicado geográfica autônomo. Para a recuperação de erros de locatários, use a localização geográfica restauração, que vem sem nenhum custo adicional. Para garantir a disponibilidade dos bancos de dados de gerenciamento, eles devem ser replicados de localização geográfica para outra região (etapa 1). O custo contínuo da configuração de recuperação de desastres neste cenário é igual ao custo total dos bancos de dados secundários. Essa configuração é ilustrada no diagrama seguinte.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

No caso de uma falha na região primária, as etapas de recuperação para trazer seu aplicativo online são ilustradas próximo diagrama.

- Imediatamente failover o gerenciamento de bancos de dados (2) à região do DR. 
- Alterar a cadeia de conexão do aplicativo para apontar para a região de DR. Todas as novas contas e bancos de dados de Inquilino serão criados na região DR. Os clientes existentes verá seus dados temporariamente indisponíveis.
- Crie o pool Elástico com a mesma configuração que o pool original (3). 
- Use localização geográfica restauração para criar cópias do locatário bancos de dados (4). Você pode considerar disparo a restaura individuais pelas conexões de usuário final ou usar algum outro esquema de prioridade específica do aplicativo.

Neste ponto seu aplicativo é online novamente na região DR, mas alguns clientes terão atraso ao acessar seus dados.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a interrupção foi temporária, é possível que a região primária será recuperada por Azure antes de todos as restaura concluída na região DR. Nesse caso, você deve coordenar movendo o aplicativo novamente para a região principal. O processo levará as etapas ilustradas no diagrama próximo.
 
- Cancele todas as solicitações de restauração geográfica pendentes.   
- Failover os bancos de dados de gerenciamento para a região principal (5). Observação: Após a recuperação da região as antigas primárias automaticamente tornaram secundários. Agora elas alternará funções novamente. 
- Alterar a cadeia de conexão do aplicativo que apontam para a região principal. Agora todas as novas contas e bancos de dados de Inquilino serão criados na região primária. Alguns clientes existentes verá seus dados temporariamente indisponíveis.   
- Defina todos os bancos de dados do pool de DR somente leitura para garantir que eles não podem ser modificados na região DR (6). 
- Para cada banco de dados do pool de DR que foram alterados desde a recuperação, renomear ou excluir os bancos de dados correspondentes no pool principal (7). 
- Copie os bancos de dados atualizados do pool de DR para o pool principal (8). 
- Excluir o pool de DR (9)

Neste ponto seu aplicativo será online na região primária com todos os inquilinos bancos de dados disponíveis no pool principal.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Os principais **benefícios** dessa estratégia é baixo custo contínuo redundância de dados camada. Backups são executados automaticamente pelo serviço de banco de dados SQL com nenhuma reconfiguração de aplicativo e sem nenhum custo adicional.  O custo é incorrido somente quando os bancos de dados Elástico são restaurados. A **desvantagem** é que a recuperação completa de todos os bancos de dados do locatário levará tempo significativo. Ele dependerá o total de número de restaura você iniciará na região DR e tamanho geral os bancos de dados do locatário. Mesmo se você priorizar restaura dos alguns locatários sobre outras pessoas, você irão competir com todos as outras restaura iniciadas na mesma região como o serviço decidir e aceleração para minimizar o impacto geral sobre bancos de dados dos clientes existentes. Além disso, a recuperação de bancos de dados do locatário não pode iniciar até que o novo pool Elástico na região DR é criado.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicativo com serviço hierárquico 

<i>Eu sou um aplicativo SaaS com ofertas de serviço hierárquico e SLAs diferentes para clientes de avaliação e para os clientes de pagamento. Para os clientes de avaliação, tenho que reduzir o custo tanto quanto possível. Clientes de avaliação podem levar o tempo de inatividade, mas quero reduzir sua probabilidade. Para os clientes de pagamento, qualquer tempo de inatividade é um risco de voo. Por isso quero garantir que pagando clientes sempre são capazes de acessar seus dados.</i> 

Para oferecer suporte a esse cenário, você deve separar os locatários avaliação de locatários pagos colocando-os em pools Elástico separados. Os clientes de avaliação teria eDTU inferior por locatário e SLA inferior com um tempo de recuperação maior. Os clientes de pagamento seria em um pool com maior eDTU por locatário e um SLA superior. Para garantir o tempo de recuperação mais baixo, bancos de dados dos clientes pagamento locatário devem ser replicados por localização geográfica. Essa configuração é ilustrada no diagrama seguinte. 

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como o primeiro cenário, os bancos de dados de gerenciamento serão bastante ativos para que você usar um banco de dados replicado geográfica autônomo-lo (1). Isso garante o desempenho previsível para novas assinaturas de clientes, atualizações de perfil e outras operações de gerenciamento. A região em que residem as cores primárias dos bancos de dados gerenciamento será a região principal e a região em que residem as secundárias sobre os bancos de dados de gerenciamento será a região de DR.

Bancos de dados dos clientes pagamento locatário terá bancos de dados ativos no pool "pago" provisionado na região primária. Você deve provisionar um pool secundário com o mesmo nome na região DR. Cada locatário seria geográfica replicados para o pool de secundário (2). Isso permitirá uma recuperação rápida de todos os bancos de dados de Inquilino usando failover. 

Se ocorrer uma falha na região primária, as etapas de recuperação para trazer seu aplicativo online são ilustradas no diagrama seguinte:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Falhe imediatamente sobre os bancos de dados de gerenciamento à região do DR (3).
- Altere a cadeia de conexão do aplicativo para apontar para a região de DR. Agora todas as novas contas e bancos de dados de Inquilino serão criados na região DR. Os clientes de avaliação existentes verá seus dados temporariamente indisponíveis.
- Failover bancos de dados do locatário paga ao pool na região DR imediatamente restaurar sua disponibilidade (4). Como o failover é uma alteração de nível de metadados rápida que você pode considerar uma otimização onde os failovers individuais são disparadas por demanda pelas conexões de usuário final. 
- Se o tamanho da sua eDTU de pool secundário era menor do que o primário porque os bancos de dados secundários necessária somente a capacidade para processar os logs de alteração enquanto fossem secundários, você deve aumentar imediatamente a capacidade do pool agora para acomodar a carga de trabalho completa de todos os locatários (5). 
- Crie o novo pool Elástico com o mesmo nome e a mesma configuração na região DR para bancos de dados dos clientes avaliação (6). 
- Depois de pool dos clientes avaliação é criado, use geográfica restauração para restaurar os bancos de dados individuais locatário de avaliação para o novo pool (7). Você pode considerar disparo a restaura individuais pelas conexões de usuário final ou usar algum outro esquema de prioridade específica do aplicativo.

Neste ponto seu aplicativo é online novamente na região DR. Todos os clientes de pagamento tem acesso aos seus dados enquanto os clientes avaliação terão atraso ao acessar seus dados.

Quando a região primária é recuperada por Azure *depois de* ter restaurado o aplicativo na região DR que você pode continuar executando o aplicativo naquela região ou você pode decidir falha volta para a região principal. Se a região primária for recuperado *antes do* processo de failover for concluído, você deve considerar falha voltar imediatamente. O failback executará as etapas ilustradas no diagrama seguinte: 
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Cancele todas as solicitações de restauração geográfica pendentes.   
- Failover os bancos de dados de gerenciamento (8). Após a recuperação da região principal antigo tinha se torna automaticamente o secundário. Agora ele se tornará o principal novamente.  
- Failover do locatário pago bancos de dados (9). Da mesma forma, após a recuperação da região as antigas primárias se torna automaticamente secundários. Agora que eles se tornará as primárias novamente. 
- Defina os bancos de dados restaurados avaliação que foram alterados na região DR para somente leitura (10).
- Para cada banco de dados do pool de avaliação clientes DR que foram alterados desde a recuperação, renomear ou excluir o banco de dados correspondente no pool principal de avaliação clientes (11). 
- Copie os bancos de dados atualizados do pool de DR para o pool principal (12). 
- Excluir o pool de DR (13) 

> [AZURE.NOTE] A operação de failover é assíncrona. Para minimizar o tempo de recuperação é importante que você executa o comando de failover o locatário dos bancos de dados em lotes de pelo menos 20 bancos de dados. 

Os principais **benefícios** dessa estratégia é que ele fornece o SLA mais alto para os clientes de pagamento. Ele também garante que o novo tentativas estão desbloqueadas assim que o pool de DR avaliação é criado. A **desvantagem** é que essa configuração aumentará o custo total de bancos de dados do locatário dos custos do pool de DR secundário para clientes pagos. Além disso, se o pool secundário tem um tamanho diferente, os clientes de pagamento terão desempenho inferior após failover até que a atualização do pool na região DR seja concluída. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicativo geograficamente distribuído com o serviço hierárquico

<i>Eu tenho um aplicativo SaaS com ofertas de serviço hierárquico. Eu quero oferecem um SLA muito rigorosas para meus clientes pagas e minimizar o risco de impacto quando interrupções ocorrem porque mesmo breve interrupção pode causar insatisfação do cliente. É fundamental que os clientes de pagamento sempre podem acessar seus dados. As tentativas são gratuitas e um SLA não é oferecido durante o período de avaliação.</i> 

Para oferecer suporte a esse cenário, você deve ter três pools Elástico separados. Dois pools de tamanho igual com alta eDTUs por banco de dados devem ser provisionados em duas regiões diferentes para conter bancos de dados dos clientes paga locatário. O pool de terceiro que contém os avaliação locatários teria um eDTUs inferior por banco de dados e ser provisionado em um dos dois região.

Para garantir o tempo de recuperação mais baixo durante interrupções locatário dos clientes pagamento bancos de dados devem ser replicados geográfica com 50% dos bancos de dados principais em cada uma das duas regiões. Da mesma forma, cada região teria 50% dos bancos de dados secundários. Dessa forma se uma região estiver offline apenas 50% de bancos de dados dos clientes paga poderia ser afetados e teria failover. Outros bancos de dados seriam permanecerão intactos. Essa configuração é ilustrada no diagrama a seguir:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Como os cenários anteriores, os bancos de dados de gerenciamento serão bastante ativos para que você deve configurá-los como autônomo replicado geográfica bancos de dados do (1). Isso garante o desempenho previsível de novas assinaturas de clientes, atualizações de perfil e outras operações de gerenciamento. Região A seria a região principal para os bancos de dados de gerenciamento e a região B será usada para recuperação dos bancos de dados de gerenciamento.

Bancos de dados dos clientes pagamento locatário será também replicado geográfica, mas com primárias e secundárias dividido entre A região e região B (2). Dessa forma os bancos de dados principal do locatário afetados por falta de pode failover a região outros e se tornam disponíveis. A outra metade dos bancos de dados locatário não será afetada de forma alguma. 

O diagrama seguinte ilustra as etapas de recuperação a seguir se ocorrer uma falha na região r.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Falhe imediatamente sobre gerenciamento bancos de dados à região B (3).
- Altere a cadeia de conexão do aplicativo para apontar para os bancos de dados de gerenciamento na região B. modificar o gerenciamento de bancos de dados para garantir que as novas contas e bancos de dados de Inquilino serão criados na região B e os bancos de dados existentes do locatário serão encontrados lá também. Os clientes de avaliação existentes verá seus dados temporariamente indisponíveis.
- Failover bancos de dados do locatário paga ao pool 2 na região B imediatamente restaurar sua disponibilidade (4). Como o failover é uma alteração de nível de metadados rápida que você pode considerar uma otimização onde os failovers individuais são disparadas por demanda pelas conexões de usuário final. 
- Desde agora pool 2 contém somente bancos de dados principais que a carga de trabalho total no pool aumentará para que você deve imediatamente aumentar seu tamanho eDTU (5). 
- Crie o novo pool Elástico com o mesmo nome e a mesma configuração na região B para bancos de dados dos clientes avaliação (6). 
- Uma vez criado o pool use localização geográfica restauração para restaurar o banco de dados individuais locatário de avaliação para o pool (7). Você pode considerar disparo a restaura individuais pelas conexões de usuário final ou usar algum outro esquema de prioridade específica do aplicativo.


> [AZURE.NOTE] A operação de failover é assíncrona. Para minimizar o tempo de recuperação é importante que você executa o comando de failover o locatário dos bancos de dados em lotes de pelo menos 20 bancos de dados. 

Neste ponto seu aplicativo estiver online novamente na região B. Todos os clientes de pagamento tem acesso aos seus dados enquanto os clientes avaliação terão atraso ao acessar seus dados.

Quando A região é recuperado você precisa decidir se deseja usar região B para clientes de avaliação ou failback usando o pool de clientes avaliação na região r. Um critério pode ser a % de bancos de dados do locatário de avaliação modificados desde a recuperação. Independentemente dessa decisão você precisará saldo novamente os locatários pagos entre dois pools. o diagrama seguinte ilustra o processo quando os bancos de dados do locatário de avaliação falham voltar à região r.  
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Cancele todas as solicitações de restauração geográfica pendentes para avaliação pool de DR.   
- Failover do banco de dados de gerenciamento (8). Após a recuperação da região, o principal antigo automaticamente tinha se tornou o secundário. Agora ele se tornará o principal novamente.  
- Selecione quais locatário pago bancos de dados falhará para failover de 1 e iniciação de pool para seus secundários (9). Após a recuperação da região todos os bancos de dados no pool de 1 automaticamente se tornou secundários. Agora 50% delas ficará primárias novamente. 
- Reduza o tamanho do pool de 2 ao eDTU original (10).
- Definir todas as restaurados avaliação bancos de dados na região B somente leitura (11).
- Para cada banco de dados do pool de DR avaliação que foram alterados desde a recuperação renomear ou excluir o banco de dados correspondente no pool principal avaliação (12). 
- Copie os bancos de dados atualizados do pool de DR para o pool principal (13). 
- Excluir o pool de DR (14) 

Os principais **benefícios** dessa estratégia são:

- Suporta o SLA mais rigorosas para os clientes de pagamento porque garante que uma interrupção não pode afetar mais de 50% os bancos de dados do locatário. 
- Ela garante que o novo tentativas estão desbloqueadas assim que a trilha de pool de DR é criada durante a recuperação. 
- Ele permite o uso mais eficiente da capacidade de pool como 50% de bancos de dados secundários no pool 1 e 2 do pool garante sejam menos ativo e os bancos de dados principais.

A principal **concessões** são:

- As operações CRUD contra os bancos de dados de gerenciamento terá menor latência para os usuários finais conectados à região A que os usuários finais conectado à região B como elas serão executadas contra o principal dos bancos de dados de gerenciamento.
- Requer mais complexa do design do banco de dados de gerenciamento. Por exemplo, cada registro de locatário precisa ter uma marca de local que precisa ser alterado durante failover e failback.  
- Os clientes de pagamento podem notar um desempenho mais baixo que o normal até que a atualização do pool na região B seja concluída. 

## <a name="summary"></a>Resumo

Este artigo aborda as estratégias de recuperação de desastres para a camada de banco de dados usado por um aplicativo de vários locatário SaaS ISV. A estratégia escolhida deve se basear as necessidades do aplicativo, como o modelo de negócios, o SLA que você deseja oferecer aos seus clientes, orçamento restrição etc... Cada estratégia descrita descreve os benefícios e desvantagem para que você pode tomar uma decisão informada. Além disso, o seu aplicativo específico provavelmente incluirá outros componentes do Azure. Portanto, você deve examinar suas orientações de continuidade de negócios e coordenar a recuperação da camada do banco de dados com elas. Para saber mais sobre o gerenciamento de recuperação de aplicativos de banco de dados no Azure, consulte [Criando soluções de nuvem para recuperação de dados](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Próximas etapas

- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
