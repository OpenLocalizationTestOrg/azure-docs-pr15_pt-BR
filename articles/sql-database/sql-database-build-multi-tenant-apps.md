<properties
   pageTitle="Banco de dados do SQL Azure cria locatários vários aplicativos com eficiência e isolamento"
   description="Saiba como o banco de dados SQL amplia locatários vários aplicativos"
   keywords=""
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
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Cria locatários vários aplicativos com o banco de dados do SQL Azure com eficiência e isolamento

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Aproveitar Elástico pools e criar aplicativos de vários locatários mais eficientes

Se você for um desenvolvimento de SaaS escrever um aplicativo de vários locatário e manipulação de muitos clientes, você geralmente, fazer compensações no cliente do desempenho, gerenciamento e segurança. Com o Azure Pools SQL banco de dados de elástica banco de dados, você não tem mais tornar esse compromisso. Esses pools ajudam você a gerenciar e monitorar aplicativos de vários locatários e obter benefícios de isolamento de um cliente-por-banco de dados. Consulte [padrões de Design para aplicativos de vários locatários SaaS com banco de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![Construir-multi-locatário-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Você controla a escala automática

Pools dimensionar automaticamente desempenho e capacidade de armazenamento para bancos de dados elástica instantâneos. Você pode controlar o desempenho atribuído a um pool, adicionar ou remover Elástico bancos de dados sob demanda e definir o desempenho de bancos de dados Elástico sem afetar o custo geral do pool de. Isso significa que você não precisa se preocupar em gerenciar o uso de bancos de dados individuais.

[Leia a documentação](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Gerenciamento inteligente do seu ambiente

Recomendações de dimensionamento internos identificam proativamente os bancos de dados que faria beneficiar pools. Essas recomendações permitem "" hipóteses para otimização rápida atender às suas metas de desempenho. Monitoramento e painéis de solução de problemas de desempenho de rich ajudam você a visualizar utilização de históricos de pool.

[Leia a documentação](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Desempenho e preço para atender às suas necessidades

Básico, padrão, e pools Premium fornecem uma ampla variedade de desempenho, armazenamento e opções de preços. Pools podem conter até 400 Elástico bancos de dados. Bancos de dados Elástico podem dimensionar automaticamente até unidades de transação de banco de dados elástica 1000 (eDTU).

[Leia a documentação](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Ferramentas elástica

Além de pools Elástico, existem recursos de banco de dados SQL para ajudar a gerenciar atividades operacionais entre vários bancos de dados:

**Execute consultas de bancos de dados e relatórios.**  
[Consulta de banco de dados elástica](sql-database-elastic-query-overview.md) permite executar consultas ou relatórios em bancos de dados no pool de elástica e acesso remotos dados armazenados em muitos bancos de dados do seu pool de uma só vez.

**Execute transações de banco de dados cruzada.**  
[Transações de banco de dados elástica](sql-database-elastic-transactions-overview.md) permitem que você execute transações que abrangem vários bancos de dados em bancos de dados do SQL e executam operações (isto é, quando transações financeiras de processamento nos bancos de dados, ou quando Atualizando estoque em um banco de dados e pedidos).

**Execute as mesmas operações em vários bancos de dados.**  
[Trabalhos de banco de dados elástica](sql-database-elastic-jobs-overview.md) executar operações administrativas como reconstruir índices ou atualizar esquemas em cada banco de dados no pool de elástica.

Vá para a home page para ver que outro banco de dados do SQL tem a oferecer.
[Fazer check-out](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Próximas etapas

Obtenha uma [assinatura gratuita do Azure](https://azure.microsoft.com/get-started/) e [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Explore todos os [recursos do banco de dados SQL](https://azure.microsoft.com/services/sql-database/).
 
Examine a [Visão geral técnica do banco de dados SQL](sql-database-technical-overview.md).  
