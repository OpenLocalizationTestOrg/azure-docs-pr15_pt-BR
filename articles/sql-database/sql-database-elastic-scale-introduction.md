<properties
    pageTitle="Adição de servidores com o Azure SQL Database | Microsoft Azure"
    description="Software como um desenvolvedores de serviço (SaaS) possa criar facilmente Elástico, scalable bancos de dados na nuvem usando estas ferramentas"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Dimensionamento com o banco de dados do SQL Azure

Você pode facilmente dimensionar bancos de dados do SQL Azure usando as ferramentas de **Banco de dados elástica** . Esses recursos e ferramentas permitem que você usar os recursos de banco de dados praticamente ilimitado do **Banco de dados do SQL Azure** para criar soluções para transações cargas de trabalho e Software especialmente como aplicativos de serviço (SaaS). Recursos de banco de dados Elástico são compostos das seguintes opções:

* [Biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md): A biblioteca de cliente é um recurso que permite criar e manter bancos de dados sharded.  Consulte [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).
* [Ferramenta de divisão direta de banco de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md): move dados entre bancos de dados sharded. Isso é útil para mover dados de um banco de dados de vários locatário para um banco de dados único locatário (ou vice-versa). Consulte [o banco de dados Elástico tutorial de ferramenta de divisão direta](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Trabalhos de banco de dados elástica](sql-database-elastic-jobs-overview.md) (visualização): Use trabalhos para gerenciar um grande número de bancos de dados do SQL Azure. Execute operações administrativas como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta de dados de desempenho ou conjunto de telemetria do locatário (cliente) usando trabalhos facilmente.
* [Consulta de banco de dados elástica](sql-database-elastic-query-overview.md) (visualização): permite que você executa uma consulta Transact-SQL que abranja vários bancos de dados. Isso permite a conexão para ferramentas de relatório como o Excel, PowerBI, Tableau, etc.
* [Transações elástica](sql-database-elastic-transactions-overview.md): esse recurso permite que você execute transações que abrangem vários bancos de dados no Azure SQL Database. Transações de banco de dados elástica estão disponíveis para aplicativos .NET usando ADO .NET e integram a experiência de programação familiar usando as [classes Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

O gráfico a seguir mostra uma arquitetura que inclui os **recursos de banco de dados elástica** em relação um conjunto de bancos de dados.

Nesse gráfico, as cores do banco de dados representam esquemas. Bancos de dados com a mesma cor compartilham o mesmo esquema.

1. Um conjunto de **bancos de dados do SQL Azure** são hospedados no Azure usando arquitetura de fragmentação.
2. A **biblioteca de cliente do banco de dados elástica** é usada para gerenciar um conjunto de fragmentar.
3. Um subconjunto dos bancos de dados são colocados em um **banco de dados elástica pool**. (Consulte [o que é um pool?](sql-database-elastic-pool.md)).
4. Um **banco de dados elástica trabalho** executa agendadas ou ad-hoc T-SQL scripts em relação a todos os bancos de dados.
5. A **ferramenta de mesclagem de divisão** é usado para mover dados de um fragmentar para outro.
6. A **consulta de banco de dados elástica** permite que você escrever uma consulta que se expande por todos os bancos de dados do conjunto de fragmentar.
7. **Transações elástica** permite executar transações que se estendem por vários bancos de dados. 


![Elástica ferramentas de banco de dados][1]


## <a name="why-use-the-tools"></a>Por que usar as ferramentas?

Elasticidade obtenção e escala para aplicativos em nuvem foi simples para VMs e armazenamento de blob-- simplesmente adicionar ou subtrair unidades ou aumentam energia. Mas ela permaneceu um desafio para o estado de processamento de dados em bancos de dados relacionais. Desafios surgiram nesses cenários:

* Aumentando e diminuindo capacidade para a parte de banco de dados relacional de sua carga de trabalho.
* Gerenciando pontos de acesso que podem surgir afetando um subconjunto específico de dados – como um particularmente ocupado-cliente final (Locatário).

Normalmente, cenários como esses foram resolvidos investindo em servidores de banco de dados de maior escala para o aplicativo de suporte. No entanto, essa opção é limitada na nuvem onde todo o processamento acontece em hardware mercadoria predefinido. Em vez disso, distribuição de dados e processamento através de muitos bancos de dados estruturados de forma idêntica (um padrão de escala-out conhecido como "fragmentação") fornece uma alternativa abordagens tradicionais de escala-up tanto em termos de custo e elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Dimensionamento horizontal e vertical

A figura a seguir mostra as dimensões horizontais e verticais da escala, que são as maneiras básicas que Elástico bancos de dados podem ser dimensionados.

![Horizontal versus Scaleout Vertical][2]

Dimensionamento horizontal refere-se ao adicionando ou removendo bancos de dados para ajustar o desempenho geral ou capacidade. Isso se chama "dimensionamento" check-out. Fragmentação, no qual os dados são particionados através de um conjunto de bancos de dados idênticos estruturados, é uma maneira comum para implementar o dimensionamento horizontal.  

Dimensionamento vertical se refere a aumentar ou diminuir o nível de desempenho de um banco de dados individual — isso também é conhecido como "dimensionamento para cima".

A maioria dos aplicativos de banco de dados de escala de nuvem usará uma combinação dessas duas estratégias. Por exemplo, um Software como um aplicativo de serviço pode usar dimensionamento horizontal para provisionar novos clientes finais e dimensionamento vertical para permitir que o banco de dados de cada fim-do cliente aumente ou diminua recursos como necessário a carga de trabalho.

* Dimensionamento horizontal é gerenciado usando a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md).

* Dimensionamento vertical realizado usando cmdlets do PowerShell do Azure para alterar o nível de serviço, ou colocando bancos de dados em um pool de Elástico.

## <a name="sharding"></a>Fragmentação

*Fragmentação* é uma técnica para distribuir grandes quantidades de dados estruturados de forma idêntica em um número de bancos de dados independentes. É especialmente popular com desenvolvedores de nuvem criando Software como um ofertas de serviço (SAAS) para clientes finais ou grande porte. Esses clientes finais são conhecidas como "locatários". Fragmentação pode ser necessária para qualquer número dos motivos:  

* A quantidade total de dados é muito grande para caber dentro das restrições de um único banco de dados
* A taxa de transferência de transação da carga de trabalho geral excede os recursos de um banco de dados
* Locatários podem exigir isolamento físico uns dos outros, para que bancos de dados separados são necessárias para cada locatário
* Talvez seja necessário residem em regiões diferentes para conformidade, desempenho ou motivos geopolíticas diferentes seções de um banco de dados.

Em outros cenários, como a inclusão de dados de dispositivos distribuídos, fragmentação pode ser usada para preencher um conjunto de bancos de dados que são organizados no. Por exemplo, um banco de dados separado pode ser dedicado para cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas sharded) e consultas Recuperando informações para um intervalo de data devem ser roteadas pelo aplicativo ao subconjunto de bancos de dados que aborda o intervalo em questão.

Fragmentação funciona melhor quando cada transação em um aplicativo pode ser restrita a um único valor de uma chave de fragmentação. Que garante que todas as transações serão locais para um banco de dados específico.

## <a name="multi-tenant-and-single-tenant"></a>Locatário único e vários locatário

Alguns aplicativos usam a abordagem mais simples de criação de um banco de dados separado para cada site principal. Este é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup e restauração e recurso de dimensionamento no detalhamento do locatário. Com fragmentação de locatário único, cada banco de dados está associado a um valor de ID de locatário específico (ou valor da chave do cliente), mas essa chave precisa nem sempre estar presente nos dados em si. É responsabilidade do aplicativo para rotear cada solicitação para o banco de dados apropriado – e a biblioteca de cliente pode simplificar isso.

![Único locatário versus vários locatários][4]

Outros cenários de pacote vários locatários juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Este é um típico **padrão de locatário vários fragmentação** – e ele pode ser controlado pelo fato de que um aplicativo gerencia grandes números de locatários muito pequenos. Em vários locatário fragmentação, as linhas nas tabelas de banco de dados foram projetadas para executar uma tecla identificando a ID de locatário ou tecla de fragmentação. Novamente, a camada de aplicativo é responsável por roteamento de solicitação de um locatário ao banco de dados apropriado, e isso pode oferecer suporte a biblioteca de cliente elástica banco de dados. Além disso, a segurança em nível de linha pode ser usado para filtrar quais linhas cada locatário pode acessar – para obter detalhes, consulte [aplicativos de vários locatários com ferramentas de banco de dados elástica e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuição dos dados entre bancos de dados pode ser necessárias com o padrão de locatário vários fragmentação e isso é facilitado pela ferramenta dividido direta elástica banco de dados. Para saber mais sobre padrões de design para aplicativos SaaS usando pools Elástico, consulte [Os padrões de Design para vários locatários SaaS aplicativos com o Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados de vários bancos de dados único aluguel

Ao criar um aplicativo de SaaS, é comum para oferecer aos clientes potenciais uma versão de avaliação do software. Nesse caso, ele é econômico para usar um banco de dados de vários locatário para os dados. No entanto, quando um cliente potencial torna-se um cliente, um banco de dados único locatário é melhor, desde que ele oferece o melhor desempenho. Se o cliente criou dados durante o período de avaliação, use a [ferramenta de mesclagem de divisão](sql-database-elastic-scale-overview-split-and-merge.md) de mover os dados de vários locatário para o novo banco de dados único locatário.

## <a name="next-steps"></a>Próximas etapas

Para um aplicativo de amostra que demonstra a biblioteca de cliente, consulte [Introdução às ferramentas de Datababase elástica](sql-database-elastic-scale-get-started.md).

Para converter bancos de dados existentes para usar as ferramentas, consulte [Migrar bancos de dados existentes para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver as especificações do pool elástica banco de dados, consulte [Considerações de preço e desempenho para um pool de banco de dados elástica](sql-database-elastic-pool-guidance.md)ou criar um novo pool com o [tutorial](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

