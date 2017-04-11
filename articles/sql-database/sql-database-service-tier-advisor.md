<properties 
   pageTitle="Preços recomendações de camada para banco de dados do SQL Azure" 
   description="Ao alterar preços níveis no portal do Azure, recomendações de nível de preço são desde que recomendamos a camada que é melhor adequado para execução de carga de trabalho do Azure SQL banco de dados existente. Níveis de preços descrevem o nível de desempenho e nível de serviço de um banco de dados do SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Recomendações de nível de preço de banco de dados SQL

 Recomendações de nível de preços sugerem o nível de desempenho e nível de serviço que é mais adequado para execução de carga de trabalho do SQL Azure banco de dados existente.

> [AZURE.NOTE] Preços recomendações de camada só estão disponíveis para bancos de dados da Web e Business e pools de banco de dados elástica – e disponível somente no [portal do Azure](https://portal.azure.com/).


Obter preços recomendações de nível durante as seguintes tarefas:

- [Alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL](sql-database-scale-up.md)
- [Atualize o Azure SQL server para V12](sql-database-upgrade-server-portal.md)
- Navegue até seu servidor V12. Consulte [recomendações de nível de preço de banco de dados SQL](sql-database-service-tier-advisor.md).
- [Criar um pool de banco de dados elástica](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Visão geral

O serviço de banco de dados SQL analisa desempenho atual e requisitos de recurso avaliando o uso de recursos históricos de um banco de dados do SQL. Além disso, o nível de serviço aceitáveis mínimo é determinado com base no tamanho do banco de dados e recursos habilitados [continuidade de negócios](sql-database-business-continuity.md) . 

Essas informações são analisadas e o nível de desempenho e nível de serviço que é melhor adequado para execução de carga de trabalho normal do banco de dados e manutenção é atual conjunto de recursos é recomendado.

- O serviço examina 15 a 30 dias anteriores de dados históricos (uso do recurso, o tamanho do banco de dados e atividade de banco de dados) e executa uma comparação entre a quantidade de recursos consumidas e as limitações reais dos níveis de serviço atualmente disponíveis e níveis de desempenho.
- Dados são analisados em 15 segundos e conjunto de resultados do cada intervalo é categorizado em nível de serviço existente e nível de desempenho que é melhor adequado para lidar com a carga de trabalho do conjunto de resultados.
- Esses 15 segundos amostras, em seguida, são agregadas na análise 15-30 dias maior e o nível de desempenho e nível de serviço que ideal pode manipular 95% da carga de trabalho histórica é recomendado.

### <a name="recommendations"></a>Recomendações

Com base no uso de seu banco de dados, no momento há 2 categorias de recomendações que podem ser encontradas:


| Recomendação | Descrição |
| :--- | :--- |
| Atualizar | Atualizar para um novo nível. |
| Indisponível | Um banco de dados requer uma carga de trabalho mínima ou aproximadamente 35 dias de atividade. Não há dados suficientes para fornecer uma recomendação válida. |

## <a name="getting-pricing-tier-recommendations"></a>Obtendo recomendações de nível de preço

Obter recomendações de nível de preço selecionando um banco de dados da Web ou Business existente, clique em **todas as configurações**, clique **camada de preços (escala DTUs)**. (Recomendações de camada preços também estão disponíveis quando você [atualizar o Azure SQL server V12](sql-database-upgrade-server-portal.md).)

1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar** > **bancos de dados SQL**.
4. Na lâmina **bancos de dados SQL** , clique em banco de dados que você deseja ver uma recomendação para:

    ![Selecione o banco de dados][1]

5. Na lâmina banco de dados, selecione **todas as configurações** e selecione o **nível de preços (escala DTUs)**.


7. Os **níveis de preços de recomendado** abrir onde você pode clique a camada sugerida e clique no botão **Selecionar** para alterar para essa camada.

    ![Inscrever-se para a visualização][4]

8. Opcionalmente, clique em **Exibir detalhes de uso** para abrir a lâmina de **Preços detalhes de recomendação de nível** onde você pode exibir o nível recomendado para o banco de dados, uma comparação de recursos entre os níveis atuais e recomendados e um gráfico de análise de uso do recurso histórica.

    ![Inscrever-se para a visualização][5]



## <a name="summary"></a>Resumo

Recomendações de nível preços oferecem uma experiência automatizada para coletar dados de telemetria para cada banco de dados do SQL e recomendar a melhor serviço/desempenho de nível nível combinação com base em requisitos de recurso e necessidades de desempenho real de um banco de dados. Na lâmina Configurações clique **camada de preços (escala DTUs)** para ver a preços recomendações de camadas para bancos de dados da Web e Business.



## <a name="next-steps"></a>Próximas etapas

Dependendo dos detalhes do seu banco de dados específico, executar uma atualização ou downgrade geralmente não acontece instantaneamente. O portal fornecerá notificações como as transições de banco de dados para sua nova camada, ou você pode monitorar o status da atualização consultando o modo de exibição de [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) no banco de dados mestre o banco de dados do SQL Server.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
