<properties
    pageTitle="O que é o banco de dados SQL? Introdução ao banco de dados SQL | Microsoft Azure"
    description="Obtenha uma introdução ao banco de dados do SQL: o sistema de gerenciamento (RDBMS) na nuvem de banco de dados de detalhes técnicos e recursos da Microsoft relacionais."
    keywords="Introdução ao sql, Introdução ao sql, o que é o banco de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>O que é o banco de dados SQL? Introdução ao banco de dados SQL

Banco de dados SQL é um serviço de banco de dados relacional na nuvem baseado no setor mecanismo do Microsoft SQL Server, com os recursos essenciais. Banco de dados SQL oferece previsível desempenho e escalabilidade sem tempo de inatividade, continuidade de negócios e proteção de dados — tudo com administração de quase zero. Você pode se concentrar em desenvolvimento rápido de aplicativos e aceleração seu tempo no mercado, ao invés de gerenciamento de máquinas virtuais e infraestrutura. Porque ele é baseado em mecanismo do [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , o banco de dados SQL suporta ferramentas do SQL Server existente, bibliotecas e APIs, que torna mais fácil para você mover e estender na nuvem.

Este artigo é uma introdução aos conceitos centrais de banco de dados SQL e recursos relacionados a desempenho, escalabilidade e capacidade de gerenciamento, com links para explorar detalhes. Se você estiver pronto para pular no, você pode [criar seu primeiro banco de dados do SQL](sql-database-get-started.md) ou [criar um pool de banco de dados elástica](sql-database-elastic-pool-create-portal.md) em minutos. Se você quiser aprofundar, assista a este vídeo de 30 minutos.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustar o desempenho e dimensionamento sem tempo de inatividade

Bancos de dados SQL está disponível em Basic, Standard e Premium *níveis de serviço*. Cada nível de serviço oferece [diferentes níveis de desempenho e recursos](sql-database-service-tiers.md) para dar suporte a lightweight cargas de trabalho pesado banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno para alguns dinheiro por mês, em seguida, [alterar o nível de serviço](sql-database-scale-up.md) manualmente ou programaticamente a qualquer momento enquanto seu aplicativo vai vírus no mundo inteiro, sem tempo de inatividade para seu aplicativo ou seus clientes.

Muitas empresas e aplicativos, sendo possível criar bancos de dados e desempenho de banco de dados único de discagem para cima ou para baixo sob demanda é suficiente, especialmente se os padrões de uso são relativamente previsíveis. Mas se você tiver padrões de uso imprevisíveis, ele pode dificultar a gerenciar custos e seu modelo de negócios.

[Pools Elástico](sql-database-elastic-pool.md) no banco de dados SQL resolver esse problema. O conceito é simple. Você alocar a um pool de desempenho e paga o desempenho coletivo do pool em vez de desempenho de banco de dados único. Você não precisa discar desempenho de banco de dados para cima ou para baixo. Os bancos de dados no pool, chamado *Elástico bancos de dados*, dimensionar automaticamente para cima e para baixo para atender à demanda. Bancos de dados Elástico consumam, mas não excedem os limites do pool, então o custo permanece previsível, mesmo se não de uso do banco de dados. Além disso, você pode [Adicionar e remover bancos de dados para o pool](sql-database-elastic-pool-manage-portal.md), seu aplicativo de alguns bancos de dados para milhares, tudo dentro de um orçamento que você controle de dimensionamento. Para saber mais sobre padrões de design para aplicativos SaaS usando pools Elástico, consulte [Os padrões de Design para vários locatários SaaS aplicativos com o Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

De qualquer forma que você vá — simples ou elástica — você não está limitado. Você pode misturar único bancos de dados com pools de banco de dados elástica e alterar os níveis de serviço de bancos de dados único e pools para criar designs inovadoras. Além disso, com o power e alcance do Azure, é possível mix-e-correspondência serviços Azure com o banco de dados SQL para atender às suas necessidades de design de aplicativo moderno exclusiva, promover a eficiência de custos e recursos e desbloquear novas oportunidades de negócios.

Mas, como você pode comparar o desempenho relativo bancos de dados e pools de banco de dados? Como posso saber quando você disca para cima e para baixo a parada de clique direito do mouse? A resposta é a unidade de transação de banco de dados (DTU) para bancos de dados único e a DTU elástica (eDTU) para bancos de dados Elástico e pools de banco de dados. Consulte [desempenho e opções de banco de dados SQL: entender o que está disponível em cada nível de serviço](sql-database-service-tiers.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Manter seu aplicativo e os negócios em funcionamento

Setor líder 99,99% disponibilidade contrato de serviço do Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), da plataforma uma rede global de data centers gerenciados Microsoft, ajuda a manter seu aplicativo executando 24/7. Com cada banco de dados do SQL, você tirar proveito de proteção de dados internas, tolerância e proteção de dados que você teria caso contrário para projetar, comprar, criar e gerenciar. Mesmo assim, dependendo das exigências de seus negócios, você pode exige camadas adicionais de proteção para garantir que seu aplicativo e sua empresa podem recuperar rapidamente em caso de um desastre, um erro ou algo mais. Com o banco de dados SQL, cada nível de serviço oferece um menu diferente de recursos que você pode usar para começar a execução e mantenha dessa forma. Você pode usar no momento restauração para retornar a um banco de dados para um estado anterior, a partir de 35 dias. Além disso, se o data center hospeda seus bancos de dados sofrer uma interrupção, você pode failover para réplicas de banco de dados em uma região diferente. Ou você pode usar réplicas para atualizações ou realocação para diferentes regiões.

![Localização geográfica-replicação de banco de dados do SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Consulte [Continuidade de negócios](sql-database-business-continuity.md) para obter detalhes sobre os recursos de continuidade de negócios diferentes disponíveis para níveis de serviço diferente.

## <a name="secure-your-data"></a>Proteger seus dados
SQL Server tem uma tradição sólida de segurança de dados que o banco de dados SQL upholds com recursos que limitam o acesso, proteger os dados e ajudarão-lo a monitorar a atividade. Consulte a [proteger seu banco de dados SQL](sql-database-security.md) para um rápido resumo das opções de segurança em banco de dados do SQL. Consulte o [Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados SQL](https://msdn.microsoft.com/library/bb510589) para uma visão mais abrangente de recursos de segurança. E, visite a [Central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre a segurança de plataforma do Azure.

## <a name="next-steps"></a>Próximas etapas
Agora que você leia uma introdução ao banco de dados SQL e respondeu à pergunta "Qual é o banco de dados SQL?", você está pronto para:

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para banco de dados único e comparações de custo de banco de dados elástica e calculadoras.
- Saiba mais sobre [pools Elástico](sql-database-elastic-pool.md).
- Introdução ao [criar seu primeiro banco de dados](sql-database-get-started.md).
- [Conectar-se e da consulta com SSMS](sql-database-connect-query-ssms.md)
- Crie seu primeiro aplicativo em c#, Java, Node, PHP, Python ou Ruby: [bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md)
- Veja um índice dos títulos e descrições de [todos os tópicos de serviço de banco de dados-sql Azure](sql-database-index-all-articles.md).
