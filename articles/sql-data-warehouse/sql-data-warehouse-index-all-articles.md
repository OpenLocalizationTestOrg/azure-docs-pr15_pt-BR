<properties
    pageTitle="Todos os tópicos de serviço de SQL Data Warehouse | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure denominada SQL Data Warehouse que existe em http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos os tópicos de serviço de depósito de dados do SQL Azure

Este tópico lista cada tópico que se aplica diretamente para o serviço **SQL Data Warehouse** do Azure. Você pode pesquisar esta página da Web para palavras-chave usando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Backups de depósito de dados do SQL](sql-data-warehouse-backups.md) | Saiba mais sobre backups de banco de dados interno de depósito de dados do SQL que permitem que você restaure um depósito de dados do SQL Azure um ponto de restauração ou uma região geográfica diferente. |


## <a name="updated-articles-sql-data-warehouse"></a>Artigos atualizados, depósito de dados do SQL

Esta seção lista artigos que foram atualizados recentemente, onde a atualização foi grande ou significativas. Para cada artigo atualizado, um trecho aproximado do texto redução adicionado é exibido. Os artigos foram atualizados dentro do intervalo de data de **2016-08-22** para **2016-10-05**.

| &nbsp; | Artigo | Texto atualizado, o trecho de código | Atualizado quando |
| --: | :-- | :-- | :-- |
| 2 | [Carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Para rastrear r.command selecione bytes e arquivos, s.request_id, r.status, contagem (distinta input_name) como nbr_files, soma (s.bytes_processed) / 1024/1024 como gb_processed de sys.dm_pdw_exec_requests r junção interna sys.dm_pdw_dms_external_work s em r.request_id = s.request_id onde r. rótulo = ' CTAS: cso de carga. DimProduct ' OR r. rótulo = ' CTAS: cso de carga. GROUP BY r.command dos FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, desc gb_processed;  | 2016-09-07 |
| 3 | [Restaurar o SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) | **Posso restaurar um pausado data warehouse?** Para restaurar um depósito de dados que está pausado, é necessário primeiro ativá-la novamente. Depois que o data warehouse estiver online novamente, você tem sete dias de pontos de restauração à sua escolha. **Restaurar para uma região geográfica redundantes** Se você estiver usando o armazenamento de localização geográfica redundantes, você pode restaurar o data warehouse para seu centro de dados pares em uma região geográfica diferentes. O data warehouse é restaurado desde o último backup diariamente. **Restaurar a linha do tempo** Você pode restaurar um banco de dados para qualquer ponto de restauração nos últimos sete dias. Instantâneos iniciar cada quatro a oito horas e estão disponíveis para sete dias. Quando um instantâneo é mais de sete dias, ela expire e seu ponto de restauração não está mais disponível. **Restaurar os custos** A carga de armazenamento para o depósito de dados restaurados é cobrada na taxa de armazenamento do Azure Premium. Se você pausar um depósito de dados restaurados, cobrado para armazenamento a taxa de armazenamento do Azure Premium. A vantagem de pausa não é que estiver charge | 2016-09-29 |





## <a name="get-started"></a>Introdução

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 4 | [Autenticação depósito de dados do SQL Azure](sql-data-warehouse-authentication.md) | Autenticação do Active Directory (AAD) e SQL Server Azure depósito de dados do SQL Azure. |
| 5 | [Práticas recomendadas para depósito de dados do SQL Azure](sql-data-warehouse-best-practices.md) | Recomendações e práticas recomendadas que você deve saber como você desenvolvem soluções para depósito de dados do SQL Azure. Esses ajudará você a tenha êxito. |
| 6 | [Drivers para depósito de dados do SQL Azure](sql-data-warehouse-connection-strings.md) | Cadeias de caracteres de Conexão e drivers para SQL Data Warehouse |
| 7 | [Conectar ao depósito de dados do SQL Azure](sql-data-warehouse-connect-overview.md) | Como encontrar o servidor de nome e uma conexão de cadeia de caracteres para seu depósito de dados do SQL Azure |
| 8 | [Analisar dados com aprendizado de máquina do Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Use o aprendizado de máquina do Azure para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizado de máquina previsão. |
| 9 | [Depósito de dados do SQL Azure (sqlcmd) de consulta](sql-data-warehouse-get-started-connect-sqlcmd.md) | Consultando depósito de dados do SQL Azure com o sqlcmd utilitário de linha de comando. |
| 10 | [Criar um banco de dados do SQL Data Warehouse usando Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) | Saiba como criar um depósito de dados do SQL Azure com TSQL |
| 11 | [Como criar um tíquete para SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) | Como criar um tíquete no armazém de dados do SQL Azure. |
| 12 | [Carregar os dados com dados Azure fábrica](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Saiba como carregar os dados com fábrica de dados do Azure |
| 13 | [Carregar os dados com PolyBase no depósito de dados do SQL](sql-data-warehouse-get-started-load-with-polybase.md) | Saiba o que é PolyBase e como usá-lo para data warehouse cenários. |
| 14 | [Criar um depósito de dados do SQL Azure](sql-data-warehouse-get-started-provision.md) | Saiba como criar um depósito de dados do SQL Azure no portal do Azure |
| 15 | [Criar depósito de dados do SQL usando o PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Criar SQL Data Warehouse usando o PowerShell |
| 16 | [Visualizar dados com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualizar dados de depósito de dados do SQL com o Power BI |
| 17 | [Consulta SQL Azure Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Consulta SQL Data Warehouse com o Visual Studio. |



## <a name="develop"></a>Desenvolver

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 18 | [Otimizando transações para depósito de dados do SQL](sql-data-warehouse-develop-best-practices-transactions.md) | Diretrizes de práticas recomendadas sobre como escrever atualizações de transação eficiente no armazém de dados do SQL Azure |
| 19 | [Gerenciamento de concorrência e carga de trabalho no depósito de dados do SQL](sql-data-warehouse-develop-concurrency.md) | Compreenda o gerenciamento de concorrência e carga de trabalho no depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 20 | [Criar tabela como selecionar (CTAS) no depósito de dados do SQL](sql-data-warehouse-develop-ctas.md) | Dicas para codificação com a tabela de criar como instrução select (CTAS) depósito de dados do SQL Azure para desenvolver soluções. |
| 21 | [SQL dinâmico no depósito de dados do SQL](sql-data-warehouse-develop-dynamic-sql.md) | Dicas para usar SQL dinâmico do depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 22 | [Agrupar por opções no depósito de dados do SQL](sql-data-warehouse-develop-group-by-options.md) | Dicas para implementar o grupo de opções no armazém de dados do SQL Azure para desenvolver soluções. |
| 23 | [Usar rótulos às consultas de instrumentos em SQL Data Warehouse](sql-data-warehouse-develop-label.md) | Dicas para usar rótulos às consultas de instrumentos do depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 24 | [Loops em SQL Data Warehouse](sql-data-warehouse-develop-loops.md) | Dicas para substituir cursores no armazém de dados do SQL Azure para desenvolver soluções e loops de Transact-SQL. |
| 25 | [Procedimentos armazenados no SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) | Dicas para implementar procedimentos armazenados no armazém de dados do SQL Azure para desenvolvimento de soluções. |
| 26 | [Transações em SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) | Dicas para implementar transações depósito de dados do SQL Azure para desenvolver soluções. |
| 27 | [Esquemas definidos pelo usuário no depósito de dados do SQL](sql-data-warehouse-develop-user-defined-schemas.md) | Dicas para usar esquemas de Transact-SQL no armazém de dados do SQL Azure para desenvolvimento de soluções. |
| 28 | [Atribuir variáveis em SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) | Dicas para atribuindo variáveis de Transact-SQL no depósito de dados do SQL Azure para desenvolver soluções. |
| 29 | [Modos de exibição no depósito de dados do SQL](sql-data-warehouse-develop-views.md) | Dicas para usar modos de exibição de Transact-SQL no armazém de dados do SQL Azure para desenvolvimento de soluções. |
| 30 | [Decisões de design e técnicas de codificação para depósito de dados do SQL](sql-data-warehouse-overview-develop.md) | Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para depósito de dados do SQL. |



## <a name="manage"></a>Gerenciar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 31 | [Gerenciar power de computação no depósito de dados do SQL Azure (visão geral)](sql-data-warehouse-manage-compute-overview.md) | Escala de desempenho recursos no armazém de dados do SQL Azure. Dimensionar ajustando DWUs ou pausar e continuar recursos de computação para economizar custos. |
| 32 | [Gerenciar power de computação no depósito de dados do SQL Azure (portal Azure)](sql-data-warehouse-manage-compute-portal.md) | Poder de computação do Azure tarefas portal gerenciar. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos. |
| 33 | [Gerenciar power de computação no depósito de dados do SQL Azure (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Tarefas do PowerShell para gerenciar calcular power. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos. |
| 34 | [Gerenciar power de computação no Azure SQL dados depósito (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Tarefas do PowerShell para gerenciar calcular power. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos. |
| 35 | [Gerenciar power de computação no depósito de dados do SQL Azure (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tarefas de Transact-SQL (T-SQL) para desempenho de escala-out ajustando DWUs. Economizar custos dimensionando volta durante horários de pico. |
| 36 | [Monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md) | Saiba como monitorar sua carga de trabalho usando DMVs. |
| 37 | [Gerenciar bancos de dados no depósito de dados do SQL Azure](sql-data-warehouse-overview-manage.md) | Visão geral de gerenciar bancos de dados do SQL Data Warehouse. Inclui ferramentas de gerenciamento, DWUs e desempenho de escala-out, solução de problemas de desempenho de consulta, estabelecendo políticas de segurança e restaurar um banco de dados de corrupção de dados ou de uma interrupção regional. |
| 38 | [Consultas de usuário do monitor no depósito de dados do SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Visão geral das considerações, melhores práticas e tarefas de monitoramento de consultas de usuário no depósito de dados do SQL Azure |
| 39 | [Restaurar o SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) | Visão geral das opções de restauração de banco de dados para recuperar um banco de dados no depósito de dados do SQL Azure. |
| 40 | [Restaurar um depósito de dados do SQL Azure (Portal)](sql-data-warehouse-restore-database-portal.md) | Tarefas de portal Azure para restaurar um depósito de dados do SQL Azure. |
| 41 | [Restaurar um depósito de dados do SQL Azure (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tarefas do PowerShell para restaurar um depósito de dados do SQL Azure. |
| 42 | [Restaurar um depósito de dados do SQL Azure (API REST)](sql-data-warehouse-restore-database-rest-api.md) | Tarefas de API REST para restaurar um depósito de dados do SQL Azure. |



## <a name="tables-and-indexes"></a>Tabelas e índices

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 43 | [Tipos de dados para tabelas no depósito de dados do SQL](sql-data-warehouse-tables-data-types.md) | Guia de Introdução com tipos de dados para tabelas de depósito de dados do SQL Azure. |
| 44 | [Distribuir tabelas no depósito de dados do SQL](sql-data-warehouse-tables-distribute.md) | Primeiros passos com distribuir tabelas no depósito de dados do SQL Azure. |
| 45 | [Tabelas de indexação no depósito de dados do SQL](sql-data-warehouse-tables-index.md) | Guia de Introdução com tabela indexação no depósito de dados do SQL Azure. |
| 46 | [Visão geral das tabelas no depósito de dados do SQL](sql-data-warehouse-tables-overview.md) | Guia de Introdução com tabelas de depósito de dados do Azure SQL. |
| 47 | [Tabelas de partição no depósito de dados do SQL](sql-data-warehouse-tables-partition.md) | Primeiros passos com tabela partição no armazém de dados do SQL Azure. |
| 48 | [Gerenciando estatísticas nas tabelas no depósito de dados do SQL](sql-data-warehouse-tables-statistics.md) | Guia de Introdução com estatísticas nas tabelas no depósito de dados do SQL Azure. |
| 49 | [Tabelas temporárias em SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) | Primeiros passos com tabelas temporárias no armazém de dados do SQL Azure. |



## <a name="integrate"></a>Integrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 50 | [Usar dados Azure fábrica com depósito de dados do SQL](sql-data-warehouse-integrate-azure-data-factory.md) | Dicas para usar o Azure dados fábrica (AAD) com depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 51 | [Usar aprendizado de máquina Azure com o SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Tutorial para usar aprendizado de máquina do Azure com depósito de dados do SQL Azure para desenvolver soluções. |
| 52 | [Usar a análise de fluxo Azure com depósito de dados do SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) | Dicas para usar a análise de fluxo Azure com depósito de dados do SQL Azure para desenvolver soluções. |
| 53 | [Usar o Power BI com depósito de dados do SQL](sql-data-warehouse-integrate-power-bi.md) | Dicas para usar o Power BI com depósito de dados do SQL Azure para desenvolver soluções. |
| 54 | [Aproveitar outros serviços com o SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Ferramentas e parceiros com soluções que se integram ao depósito de dados do SQL.  |



## <a name="load"></a>Carga

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 55 | [Carregar dados do armazenamento de blob do Microsoft Azure no depósito de dados do SQL Azure (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Saiba como carregar os dados com fábrica de dados do Azure |
| 56 | [Carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Saiba como usar PolyBase para carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse. Carrega algumas tabelas de dados públicos para o esquema de depósito de dados de varejo de Contoso. |
| 57 | [Carregar dados do SQL Server em um depósito de dados do SQL Azure (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Usa bcp para exportar dados do SQL Server para arquivos simples, AZCopy para importar dados para o armazenamento de blob do Microsoft Azure e PolyBase para receber os dados em depósito de dados do SQL Azure. |
| 58 | [Carregar dados do SQL Server em um depósito de dados do SQL Azure (arquivos simples)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Para um tamanho de dados pequeno, usa bcp para exportar dados do SQL Server para arquivos simples e importar os dados diretamente no depósito de dados do SQL Azure. |
| 59 | [Carregar dados do SQL Server em Azure dados depósito SSIS (SQL)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Mostra como criar um pacote do SQL Server Integration Services (SSIS) para mover dados de uma grande variedade de fontes de dados para o SQL Data Warehouse. |
| 60 | [Carregar os dados com PolyBase no depósito de dados do SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Usa bcp para exportar dados do SQL Server para arquivos simples, AZCopy para importar dados para o armazenamento de blob do Microsoft Azure e PolyBase para receber os dados em depósito de dados do SQL Azure. |
| 61 | [Guia para usar PolyBase no depósito de dados do SQL](sql-data-warehouse-load-polybase-guide.md) | Diretrizes e recomendações para usar PolyBase em cenários de depósito de dados do SQL. |
| 62 | [Carregar dados de exemplo em um depósito de dados do SQL](sql-data-warehouse-load-sample-databases.md) | Carregar dados de exemplo em um depósito de dados do SQL |
| 63 | [Carregar os dados com bcp](sql-data-warehouse-load-with-bcp.md) | Saiba quais bcp e como usá-lo para data warehouse cenários. |
| 64 | [Carregar dados em um depósito de dados do SQL Azure](sql-data-warehouse-overview-load.md) | Saiba os cenários comuns para carregamento em SQL Data Warehouse de dados. Eles incluem usando PolyBase, armazenamento de blob do Microsoft Azure, arquivos simples e entrega de disco. Você também pode usar ferramentas de terceiros. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 65 | [Migrar o código SQL depósito de dados do SQL](sql-data-warehouse-migrate-code.md) | Dicas para migrar seu código SQL depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 66 | [Migrar seus dados](sql-data-warehouse-migrate-data.md) | Dicas para migrar seus dados depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 67 | [Utilitário de migração de depósito de dados (prévia)](sql-data-warehouse-migrate-migration-utility.md) | Migre para o SQL Data Warehouse. |
| 68 | [Migrar seu esquema depósito de dados do SQL](sql-data-warehouse-migrate-schema.md) | Dicas para migrar seu esquema depósito de dados do SQL Azure para desenvolvimento de soluções. |
| 69 | [Migrar sua solução depósito de dados do SQL](sql-data-warehouse-overview-migrate.md) | Guia de migração por trazer sua solução para plataforma de depósito de dados do SQL Azure. |



## <a name="partners"></a>Parceiros

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 70 | [Parceiros de inteligência de negócios de depósito de dados do SQL](sql-data-warehouse-partner-business-intelligence.md) | Listas de parceiros de inteligência de negócios de terceiros com soluções que oferecem suporte ao SQL Data Warehouse. |
| 71 | [Parceiros de integração de dados do SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) | Lista de parceiros de terceiros com soluções de integração de dados que oferecem suporte ao depósito de dados do SQL Azure. |
| 72 | [Parceiros de gerenciamento de dados do SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) | Lista de parceiros de gerenciamento de dados de terceiros com soluções que oferecem suporte ao SQL Data Warehouse. |



## <a name="reference"></a>Referência

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 73 | [Tópicos de referência para SQL Data Warehouse](sql-data-warehouse-overview-reference.md) | Links de conteúdo de referência para depósito de dados do SQL. |
| 74 | [Cmdlets do PowerShell e as APIs REST para depósito de dados do SQL](sql-data-warehouse-reference-powershell-cmdlets.md) | Encontre os cmdlets do PowerShell superiores para depósito de dados do SQL Azure incluindo como pausar e continuar a um banco de dados. |
| 75 | [Elementos de idioma](sql-data-warehouse-reference-tsql-language-elements.md) | Lista de links para conteúdo de referência para os elementos de linguagem Transact-SQL usada para depósito de dados do SQL. |
| 76 | [Tópicos de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Links para conteúdo de referência para os tópicos de Transact-SQL usados pelo SQL Data Warehouse. |
| 77 | [Modos de exibição do sistema](sql-data-warehouse-reference-tsql-system-views.md) | Links para sistema exibe o conteúdo para depósito de dados do SQL. |



## <a name="security"></a>Segurança

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 78 | [Clientes de nível inferior de depósito de dados do SQL - suportam para dados de auditoria e dinâmicos Masking](sql-data-warehouse-auditing-downlevel-clients.md) | Saiba mais sobre o suporte de clientes de nível inferior SQL Data Warehouse para dados de auditoria |
| 79 | [Auditoria no depósito de dados do SQL Azure](sql-data-warehouse-auditing-overview.md) | Introdução ao auditoria no depósito de dados do SQL Azure |
| 80 | [Começar com transparente dados criptografia (TDE) no depósito de dados do SQL](sql-data-warehouse-encryption-tde.md) | Criptografia de dados transparente (TDE) no depósito de dados do SQL |
| 81 | [Começar com criptografia de dados transparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Criptografia de dados transparente (TDE) em SQL Data Warehouse (T-SQL) |
| 82 | [Proteger um banco de dados no depósito de dados do SQL](sql-data-warehouse-overview-manage-security.md) | Dicas para proteger um banco de dados no depósito de dados do SQL Azure para desenvolvimento de soluções. |



## <a name="miscellaneous"></a>Diversos

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 83 | [Instalar o Visual Studio 2015 e SSDT para depósito de dados do SQL](sql-data-warehouse-install-visual-studio.md) | Instalar o Visual Studio e ferramentas de desenvolvimento do SQL Server (SSDT) para depósito de dados do SQL Azure |
| 84 | [Migração para detalhes de armazenamento Premium](sql-data-warehouse-migrate-to-premium-storage.md) | Instruções para migrar um depósito de dados do SQL existente para o armazenamento de premium |
| 85 | [Introdução ao detecção de ameaças](sql-data-warehouse-security-threat-detection.md) | Como começar com a detecção de ameaças |
| 86 | [Limites de capacidade de depósito de dados do SQL](sql-data-warehouse-service-capacity-limits.md) | Valores máximos para conexões, bancos de dados, tabelas e consultas para SQL Data Warehouse. |
| 87 | [Solução de problemas de depósito de dados do SQL Azure](sql-data-warehouse-troubleshoot.md) | Solucionando problemas de depósito de dados do SQL Azure. |

