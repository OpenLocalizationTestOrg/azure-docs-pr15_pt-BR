<properties
    pageTitle="Monitorar o armazenamento de memória XTP | Microsoft Azure"
    description="Usarem estimativa e monitorar o armazenamento do XTP na memória, capacidade; resolver o erro de capacidade 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Armazenamento OLTP na memória do monitor

Ao usar [OLTP na memória](sql-database-in-memory.md), dados em tabelas de memória otimizada e variáveis de tabela residem em armazenamento na memória OLTP. Cada nível de serviço Premium tem um tamanho máximo de armazenamento de memória OLTP, que é documentado no [artigo de níveis de serviço de banco de dados do SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Depois que esse limite for excedido, inserir e atualizar operações podem iniciar falha (com o erro 41823). Nesse momento você será necessário como excluir dados para recuperar memória ou atualizar a camada de desempenho do banco de dados.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinar se dados se ajustarem o limite de armazenamento de memória

Determinar o limite de armazenamento: consulte o [artigo de níveis de serviço de banco de dados SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) para os caps de armazenamento dos níveis diferentes de serviço Premium.

Estimando requisitos de memória para um trabalhos de memória otimizada tabela da mesma maneira para o SQL Server que ela faz no Azure SQL Database. Leve alguns minutos para examinar esse tópico no [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Observe que a tabela e linhas de variável de tabela, bem como índices, contam o tamanho de dados de usuário máx. Além disso, ALTER TABLE precisa espaço suficiente para criar uma nova versão de toda a tabela e seus índices.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Você pode monitorar o uso de armazenamento de memória como um percentual do [armazenamento capitular para seu nível de desempenho](sql-database-service-tiers.md#service-tiers-for-single-databases) no Azure [portal](https://portal.azure.com/): 

- Na lâmina banco de dados, localize a caixa de utilização do recurso e clique em Editar.
- Selecione a métrica `In-Memory OLTP Storage percentage`.
- Para adicionar um alerta, na caixa de utilização de recursos para abrir a lâmina métrica, clique em Adicionar alerta.

Ou, use a seguinte consulta para mostrar a utilização de armazenamento de memória:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corrigir situações de falta de memória - erro 41823

Executando resultados de falta de memória em operações de inserção, atualização e criar falhando com mensagem de erro 41823.

Mensagem de erro 41823 indica que as tabelas de memória otimizada e variáveis de tabela excedeu o tamanho máximo.

Para resolver esse erro, ou:


- Excluir dados das tabelas memória otimizada, possivelmente transferindo os dados às tabelas tradicionais, baseado em disco; ou,
- Atualize a camada de serviço para uma com armazenamento suficiente na memória para os dados que você precisa manter nas tabelas de memória otimizada.

## <a name="next-steps"></a>Próximas etapas
Recursos adicionais sobre sobre [monitoramento Azure SQL Database usando modos de exibição de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)
