<properties 
   pageTitle="Compreensão de desempenho de banco de dados do SQL Azure | Microsoft Azure" 
   description="O banco de dados do SQL Azure fornece ferramentas de desempenho para ajudá-lo a identificar as áreas que podem melhorar o desempenho de consulta atual." 
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
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Compreensão de desempenho de banco de dados SQL

Banco de dados do SQL Azure oferece ferramentas para ajudá-lo a identificar e melhorar o desempenho dos seus bancos de dados, fornecendo recomendações e ações inteligentes de ajustes de desempenho. 

1. Navegue até o banco de dados no [Portal do Azure](http://portal.azure.com) e clique em **todas as configurações** > **desempenho **  >  **Visão geral** para abrir a página de **desempenho** . 


2. Clique em **recomendações** para abrir o [Supervisor de banco de dados do SQL](#sql-database-advisor)e clique em **consultas** para abrir [Uma visão de desempenho de consulta](#query-performance-insight).

    ![Exibir o desempenho](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Visão geral do desempenho

Clicar em **Visão geral** ou o bloco de **desempenho** , você será levado até o painel de desempenho do banco de dados. Este modo de exibição fornece um resumo de seu desempenho de banco de dados e ajuda você com o ajuste e solução de problemas de desempenho. 

![Desempenho](./media/sql-database-performance/performance.png)

- O bloco de **recomendações** fornece uma divisão de ajuste recomendações para seu banco de dados (recomendações de 3 principais são mostradas se há mais). Clicando neste bloco leva você ao **Supervisor de banco de dados do SQL**. 
- O bloco de **atividade de ajuste** fornece um resumo em andamento e concluídas ajuste ações para seu banco de dados, dando a você uma exibição rápida para o histórico de ajuste de atividade. Clicando neste bloco leva você para a exibição de histórico de ajuste completo do seu banco de dados.
- O bloco de **ajuste automático** mostra a configuração de ajuste automático do banco de dados (quais ações de ajustes são configuradas para serem aplicadas automaticamente ao seu banco de dados). Clicar neste bloco abre a caixa de diálogo de configuração de automação.
- O bloco de **consultas de banco de dados** mostra o resumo do desempenho da consulta do banco de dados (geral DTU de uso e início recurso consumir consultas). Clicando neste bloco leva você ao **Ideias de desempenho de consulta**.



## <a name="sql-database-advisor"></a>Supervisor de banco de dados SQL


[Supervisor de banco de dados do SQL](sql-database-advisor.md) fornece recomendações de ajustes inteligentes que podem ajudar a melhorar o desempenho de seu banco de dados. 

- Recomendações sobre qual índices para criar ou soltar (e uma opção para aplicar recomendações de índice automaticamente sem qualquer interação do usuário e automaticamente revertendo recomendações que tem um impacto negativo no desempenho).
- Recomendações sobre problemas de esquema são identificados no banco de dados.
- Recomendações sobre consultas vantagem de consultas com parâmetros.




## <a name="query-performance-insight"></a>Compreensão de desempenho de consulta

[Compreensão de desempenho de consulta](sql-database-query-performance.md) permite gastam menos tempo de solução de problemas de desempenho de banco de dados, fornecendo:

- Mais profunda percepção seu consumo de recursos (DTU) de bancos de dados. 
- A CPU superior consumir consultas, que potencialmente podem ser ajustadas para melhorar o desempenho. 
- A capacidade de analisar os detalhes de uma consulta. 


## <a name="additional-resources"></a>Recursos adicionais

- [Diretrizes de desempenho de banco de dados do SQL Azure para bancos de dados único](sql-database-performance-guidance.md)
- [Quando um pool de banco de dados elástica deve ser usado?](sql-database-elastic-pool-guidance.md)