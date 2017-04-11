<properties
   pageTitle="Operacional repositório de consulta no banco de dados do SQL Azure"
   description="Saiba como operar o repositório de consulta no banco de dados do SQL Azure"
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
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Operando o repositório de consulta no banco de dados do SQL Azure 

Repositório de consulta no Azure é um recurso de banco de dados totalmente gerenciado que continuamente coleta e apresenta informações detalhadas de históricas sobre todas as consultas. Você pode pensar sobre o armazenamento de consulta semelhante ao gravador de dados de voos do avião que significativamente simplifica ambos para nuvem de solução de problemas de desempenho de consulta e os clientes locais. Este artigo explica aspectos específicos de operacionais repositório de consulta no Azure. Usando esses dados de consulta previamente coletados, pode rapidamente diagnosticar e resolver problemas de desempenho e, portanto, gastar mais tempo concentrando-se em seus negócios. 

Repositório de consulta está [globalmente disponível](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) no Azure SQL Database desde novembro de 2015. Repositório de consulta é a base para análise de desempenho e ajuste de recursos, como [Supervisor de banco de dados do SQL e o painel de controle de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). No momento da publicação deste artigo, repositório de consulta é executado em mais de 200.000 bancos de dados do usuário no Azure, coletar informações relacionadas à consulta para vários meses, sem interrupção.

> [AZURE.IMPORTANT] A Microsoft está no processo de ativação do repositório de consulta para bancos de dados do SQL Azure todos (novos e existentes). 

## <a name="optimal-query-store-configuration"></a>Configuração ideal Store de consulta

Esta seção descreve os padrões de configuração ideal que foram projetados para garantir a operação confiável do repositório de consulta e recursos dependentes, como [Supervisor de banco de dados do SQL e o painel de controle de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuração padrão é otimizada para coleta de dados contínuo, que é tempo mínimo gasto em seus estados desativado/READ_ONLY.

| Configuração | Descrição | Padrão | Comentário |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Especifica o limite para o espaço de dados que o repositório de consulta pode levar dentro do banco de dados de cliente z | 100 | Impostas para novos bancos de dados |
| INTERVAL_LENGTH_MINUTES | Define o tamanho da janela de tempo durante o qual as estatísticas de tempo de execução coletados para planos de consulta são agregadas e persistentes. Cada plano de consulta ativa tem no máximo uma linha para um período de tempo definido com esta configuração | 60   | Impostas para novos bancos de dados |
| STALE_QUERY_THRESHOLD_DAYS | Política de limpeza baseada no tempo que controla o período de retenção de estatísticas de tempo de execução persistentes e consultas inativas | 30 | Impostas para novos bancos de dados e bancos de dados com padrão anterior (367) |
| SIZE_BASED_CLEANUP_MODE | Especifica se a limpeza de dados automática ocorre quando o limite de aproximar de tamanho de armazenamento de consulta de dados | Automático | Impostas para todos os bancos de dados |
| QUERY_CAPTURE_MODE | Especifica se todas as consultas ou apenas um subconjunto de consultas são controladas | Automático | Impostas para todos os bancos de dados |
| FLUSH_INTERVAL_SECONDS | Especifica o período máximo durante o qual capturado runtime estatísticas são mantidas na memória, antes de liberar para disco | 900 | Impostas para novos bancos de dados |
||||||

> [AZURE.IMPORTANT] Esses padrões são aplicadas automaticamente na etapa final de ativação de repositório de consulta em todos os bancos de dados do SQL Azure (consulte a observação importante de anterior). Após essa claro para cima, Azure SQL Database não alterando configuração valores definidos pelo clientes, a menos que eles afetarem negativamente carga de trabalho principal ou operações confiáveis do armazenamento de consulta.

Se você quiser manter-se com suas configurações personalizadas, use [ALTER DATABASE com opções de armazenamento de consulta](https://msdn.microsoft.com/library/bb522682.aspx) para reverter a configuração para o estado anterior. Confira [Práticas recomendadas com o armazenamento de consulta](https://msdn.microsoft.com/library/mt604821.aspx) para saber como início escolhido parâmetros de configuração ideal.

## <a name="next-steps"></a>Próximas etapas

[Compreensão de desempenho de banco de dados SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte os seguintes artigos:

- [Um gravador de dados de voos para seu banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Monitorar o desempenho usando o armazenamento de consulta](https://msdn.microsoft.com/library/dn817826.aspx)

- [Cenários de uso de repositório de consulta](https://msdn.microsoft.com/library/mt614796.aspx)

- [Monitorar o desempenho usando o armazenamento de consulta](https://msdn.microsoft.com/library/dn817826.aspx) 
