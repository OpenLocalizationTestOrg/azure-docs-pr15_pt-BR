A unidade de transação de banco de dados (DTU) é a unidade de medida no banco de dados SQL que representa a energia relativa de bancos de dados com base em uma medida do mundo real: a transação de banco de dados. Tivemos um conjunto de operações que são típicos para uma transação online processamento de solicitação de (OLTP) e, em seguida, medido quantas transações podem ser concluídas por segundo em totalmente carregado condições (que é a versão resumida, você pode ler os detalhes terríveis na [Visão geral de avaliação de desempenho](../articles/sql-database/sql-database-benchmark-overview.md)). 

Por exemplo, um banco de dados Premium P11 com 1750 DTUs fornece 350 x DTU mais calcular energia que um banco de dados básico com 5 DTUs. 

![Introdução ao banco de dados SQL: único DTUs de banco de dados por nível e nível.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Se você estiver migrando um banco de dados existente do SQL Server, você pode usar uma ferramenta de terceiros, [A Calculadora de DTU do banco de dados de SQL Azure](http://dtucalculator.azurewebsites.net/), para obter uma estimativa do nível de desempenho e serviço camada que seu banco de dados pode exigir no Azure SQL Database.

### <a name="dtu-vs-edtu"></a>DTU versus eDTU

O DTU para bancos de dados único traduz diretamente para o eDTU para Elástico bancos de dados. Por exemplo, um banco de dados em um pool de banco de dados elástica básica oferece eDTUs até 5. Esse é o mesmo desempenho de um único banco de dados básico. A diferença é que o banco de dados Elástico não consumir qualquer eDTUs do pool até que ele precisa. 

![Introdução ao banco de dados SQL: pools Elástico por nível.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Um exemplo simples ajuda. Colocar um pool de banco de dados elástica básica com 1000 DTUs e solte 800 bancos de dados nele. Como 200 apenas dos 800 bancos de dados estão sendo usados em qualquer ponto no tempo (5 DTU X 200 = 1000), você não acerta capacidade do pool e não prejudicar o desempenho do banco de dados. Este exemplo é simplificado para clareza. A matemática real é um pouco mais complexa. O portal faz a matemática para você e faz uma recomendação com base no uso de banco de dados histórico. Consulte [Considerações de preço e desempenho para um pool de banco de dados elástica](../articles/sql-database/sql-database-elastic-pool-guidance.md) para aprender como funcionam as recomendações, ou para fazer as contas por conta própria. 
