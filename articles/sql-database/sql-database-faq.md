<properties 
   pageTitle="Banco de dados do SQL Azure perguntas Frequentes" 
   description="Pergunte respostas aos clientes de perguntas comuns sobre bancos de dados de nuvem e o Azure SQL Database, o sistema de gerenciamento de banco de dados relacional (RDBMS) da Microsoft e o banco de dados como um serviço na nuvem." 
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
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Banco de dados do SQL perguntas Frequentes

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Como ao uso de banco de dados SQL aparece na minha fatura? 
Faturas de banco de dados SQL em uma taxa por hora previsível com base no nível de serviço + nível de desempenho para bancos de dados único ou eDTUs por pool elástica banco de dados. Uso real é calculado e proporcional por hora, para que sua fatura pode mostrar frações de uma hora. Por exemplo, se houver um banco de dados por 12 horas em um mês, sua fatura mostra o uso de 0,5 dias. Além disso, os níveis de serviço + nível de desempenho e eDTUs por pool são divididos na fatura para tornar mais fácil ver o número de dias de banco de dados que você usou para cada um deles em um único mês.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>O que acontece se um banco de dados está ativo para menos de uma hora ou usa um nível de serviço superior para menos de uma hora?
Você será cobrado por cada hora existe um banco de dados usando o nível de serviço mais alto + nível de desempenho que aplicadas durante esse hora, independentemente de uso ou se o banco de dados estava ativo para menos de uma hora. Por exemplo, se você criar um banco de dados único e excluí-la mais tarde cinco minutos sua fatura reflete um encargo por hora de um banco de dados. 

Exemplos
    
- Se você criar um banco de dados básico e, em seguida, atualizá-lo imediatamente para S1 padrão, cobrado na taxa S1 padrão para a primeira hora.

- Se você atualizar um banco de dados do Basic Premium às 10:00. e atualização conclui à 1h: 35 no dia seguinte, cobrado na taxa Premium começando em 1:00 a.m. 

- Se você reduzir um banco de dados do Premium básicas às 11:00. ele seja concluído em 2:15 PM, e o banco de dados cobrada de acordo com a taxa de Premium até 3:00 p.m., após o qual ele cobrada de acordo com as taxas básicas.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Como uso do pool de banco de dados elástica aparece na minha fatura e o que acontece quando eu altero eDTUs por pool?
Encargos de pool de banco de dados elástica aparecem na fatura como elástica DTUs (eDTUs) em incrementos de mostrada em eDTUs por pool na [página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não há nenhum custo por banco de dados para pools de elástica banco de dados. Você será cobrado por cada hora que um pool existe em eDTU o mais alto, independentemente de uso ou o pool estava ativo para menos de uma hora. 

Exemplos

- Se você criar um pool de banco de dados padrão elástica com 200 eDTUs às 11:18., adicionando cinco bancos de dados ao pool cobrado para 200 eDTUs para hora inteira, começando às 11. através do restante do dia.
- No dia 2, em 5:05 a.m., 1 de banco de dados começa consumindo 50 eDTUs e detém estável por meio do dia. Bancos de dados de 2 a 5 flutuar entre 0 e 80 eDTUs. Durante o dia, você adiciona cinco outros bancos de dados que consumir variáveis eDTUs ao longo do dia. Dia 2 é um dia inteiro cobrado a eDTU 200. 
- No dia 3, em 5 a.m. Adicionar outro 15 bancos de dados. Uso de banco de dados aumenta durante o dia para o ponto onde você decidir aumentar eDTUs para o pool de 200 a 400 em 8:05 p.m. Encargos no nível 200 eDTU estavam em vigor até 8 pm e aumenta a 400 eDTUs para as horas de quatro restantes. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Como que o uso de ativos geográfica replicação em um pool de banco de dados elástica aparece na minha fatura?
Ao contrário de bancos de dados único, usar [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) com Elástico bancos de dados não tem um impacto direto de cobrança.  Somente cobrado para o eDTUs provisionada para cada um dos pools (pool primário e secundário pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Como o uso do recurso de auditoria afeta minha fatura? 
Auditoria interna do serviço de banco de dados SQL sem extra de custo e está disponível para bancos de dados Basic, Standard e Premium. Entretanto, para armazenar os logs de auditoria, os usos de recurso de auditoria que aplicam uma conta de armazenamento do Azure e taxas para tabelas e filas no armazenamento do Azure baseiam no tamanho do seu log de auditoria.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Como encontro o nível de desempenho e nível de serviço certo para bancos de dados único e pools de banco de dados elástica? 
Existem algumas ferramentas disponíveis para você. 

- Bancos de dados local, use o [Supervisor de dimensionamento DTU](http://dtucalculator.azurewebsites.net/) recomendar bancos de dados e DTUs obrigatórios e avaliar vários bancos de dados para pools de elástica banco de dados.
- Se um banco de dados único seria beneficiar sendo em um pool, o mecanismo inteligente do Azure recomenda um pool de banco de dados elástica se vir um padrão de uso históricos que garanta a ele. Consulte [Monitor e gerenciar um pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-manage-portal.md). Para obter detalhes sobre como fazer os cálculos por conta própria, consulte [Considerações de preço e desempenho para um pool de banco de dados elástica](sql-database-elastic-pool-guidance.md)
- Para ver se você precisa discar um banco de dados único para cima ou para baixo, consulte [diretrizes de desempenho para bancos de dados único](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Com que frequência posso alterar o nível de desempenho ou nível de serviço de um banco de dados? 
Com bancos de dados V12, você pode alterar o nível de serviço (entre Basic, Standard e Premium) ou o nível de desempenho dentro de um nível de serviço (por exemplo, S1 como S2) quantas vezes quiser. Bancos de dados de versão anterior, você pode alterar o nível de desempenho ou nível de serviço um total de quatro vezes em um período de 24 horas.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Com que frequência posso ajustar o eDTUs por pool? 
Quantas vezes quiser.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Quanto tempo leva para alterar o nível de desempenho ou nível de serviço de um banco de dados único ou mover um banco de dados e sair de um pool de banco de dados elástica? 
Alterar o nível de serviço de um banco de dados e mover e sair de um pool requerem o banco de dados sejam copiados na plataforma como uma operação de plano de fundo. Alterar o nível de serviço pode levar alguns minutos a várias horas dependendo do tamanho dos bancos de dados. Em ambos os casos, os bancos de dados permanecem online e disponível durante a movimentação. Para obter detalhes sobre a alteração de bancos de dados único, consulte [alterar o nível de serviço de um banco de dados](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando devo usar um banco de dados único versus Elástico bancos de dados? 
Em geral, os pools de banco de dados elástica foram projetados para um típico [padrão de aplicativo do software como serviço (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), onde não há um banco de dados por locatário ou de cliente. Compra de bancos de dados individuais e excesso de provisionamento para atender a variável e demanda para cada banco de dados de pico é geralmente não custo eficiente. Com pools, você gerencia o desempenho coletivo do pool e os bancos de dados para cima e para baixo dimensionar automaticamente. 

Mecanismo de inteligente do Azure recomenda um pool para bancos de dados quando um padrão de uso garanta a ele. Para obter detalhes, consulte [recomendações de nível de preço de banco de dados SQL](sql-database-service-tier-advisor.md). Para obter orientações detalhadas sobre como escolher entre bancos de dados único e Elástico, consulte [Considerações de preço e desempenho para pools de elástica banco de dados](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>O que significa ter até 200% de seu armazenamento de banco de dados provisionados máximo para armazenamento de backup? 
Armazenamento de backup é o armazenamento associado backups automatizados do banco de dados que são usados para [ponto-em-tempo restauração](sql-database-recovery-using-backups.md#-point-in-time-restore) e [Restauração de localização geográfica](sql-database-recovery-using-backups.md#geo-restore). Banco de dados SQL do Microsoft Azure fornece até 200% de seu armazenamento de banco de dados provisionados máximo de armazenamento de backup sem nenhum custo adicional. Por exemplo, se você tiver uma instância de banco de dados padrão com um tamanho de DB provisionado de 250 GB, são fornecidos com 500 GB de armazenamento de backup sem nenhum custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, você pode optar por reduzir o período de retenção contatando o suporte do Azure ou pagar para o armazenamento extra backup cobrado taxa padrão de armazenamento do acesso de leitura geograficamente redundante (ar-GRS). Para obter mais informações sobre cobrança de ar GRS, consulte detalhes de preços de armazenamento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Eu estou mudando do Web/Business para os novos níveis de serviço, o que eu preciso saber?
Bancos de dados da Web do SQL e Business Azure agora são descartados. As camadas Basic, Standard, Premium e elástica substituem os bancos de dados da Web e Business retiring. Nós já FAQ adicionais que deverá ajudá-lo nesse período de transição. [Perguntas Frequentes do pôr do sol da Web e Business Edition](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>O que é um tempo de latência de replicação esperada ao replicar geográfica um banco de dados entre duas regiões dentro a mesma geografia Azure?  
Estamos atualmente oferece suporte a um RPO de cinco segundos e a latência de replicação foi menor do que que quando o secundário geográfica está hospedado no Azure recomendado pares de região e o mesmo nível de serviço.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>O que é um tempo de latência de replicação esperada quando geográfica secundário é criado na mesma região do banco de dados principal?  
Com base em dados empíricos, não há muita diferença entre UE-região e o tempo de latência de replicação de região entre quando o Azure recomendado pares de região é usado. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se houver uma falha de rede entre duas regiões, como a lógica de repetição funciona quando geográfica replicação estiver configurada?  
Se houver uma desconexão, podemos repetir a cada 10 segundos para estabelecer conexões novamente.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>O que posso fazer para garantir que uma alteração crítica no banco de dados principal seja replicada?
Localização geográfica-secundário é uma réplica assíncrono e não tentamos mantê-lo na sincronização completa com o principal. Mas fornecemos um método para forçar sincronização para garantir que a replicação das alterações críticas (por exemplo, atualizações de senha). Sincronização forçada afeta o desempenho porque ela bloqueia o segmento de chamada até que todas as transações confirmadas são replicadas. Para obter detalhes, consulte [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quais ferramentas estão disponíveis para monitorar a latência de replicação entre o banco de dados principal e secundário geográfica?
Vamos expor a latência de replicação em tempo real entre o banco de dados principal e secundário de localização geográfica por meio de um departamento de trânsito. Para obter detalhes, consulte [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
