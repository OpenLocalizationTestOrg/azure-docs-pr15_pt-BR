<properties
    pageTitle="Todos os tópicos de serviço de banco de dados SQL | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure denominada banco de dados SQL que existe em http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Todos os tópicos de serviço de banco de dados do Azure SQL

Este tópico lista cada tópico que se aplica diretamente para o serviço de **Banco de dados SQL** do Azure. Você pode pesquisar esta página da Web para palavras-chave usando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Daxko/CSI usado Azure para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços de cliente e desempenho](sql-database-implementation-daxko.md) | Saiba como Daxko/CSI usa o banco de dados SQL para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços de cliente e desempenho |
| 2 | [Azure oferece alcance global GEP e eficiência](sql-database-implementation-gep.md) | Saiba mais sobre como GEP usa o banco de dados SQL para alcançar clientes mais globais e obter mais eficiência |
| 3 | [Com o Azure, SnelStart expandiu rapidamente os seus serviços de negócios em uma taxa de 1.000 novos Azure SQL bancos de dados por mês](sql-database-implementation-snelstart.md) | Saiba mais sobre como SnelStart usa o banco de dados SQL para expandido rapidamente seus serviços de negócios em uma taxa de 1.000 novos Azure SQL bancos de dados por mês |
| 4 | [Umbraco usa Azure SQL Database para serviços rapidamente provisionar e escala para milhares de locatários na nuvem](sql-database-implementation-umbraco.md) | Saiba mais sobre como o Umbraco usa o banco de dados SQL para provisionar rapidamente e serviços de escala para milhares de locatários na nuvem |
| 5 | [Gerenciar o banco de dados do SQL Azure com o PowerShell](sql-database-manage-powershell.md) | Gerenciamento de banco de dados do SQL Azure com o PowerShell. |
| 6 | [Suporte a SSMS Azure AD MFA com o banco de dados SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Use Multi-acrescentado autenticação com SSMS para banco de dados SQL e SQL Data Warehouse. |
| 7 | [Explicando unidades de transação de banco de dados (DTUs) e as unidades de transação de banco de dados (eDTUs) elástica](sql-database-what-is-a-dtu.md) | Noções básicas sobre quais um Azure SQL Database é unidade de transação. |


## <a name="updated-articles-sql-database"></a>Artigos atualizados, banco de dados SQL

Esta seção lista artigos que foram atualizados recentemente, onde a atualização foi grande ou significativas. Para cada artigo atualizado, um trecho aproximado do texto redução adicionado é exibido. Os artigos foram atualizados dentro do intervalo de data de **2016-08-22** para **2016-10-05**.

| &nbsp; | Artigo | Texto atualizado, o trecho de código | Atualizado quando |
| --: | :-- | :-- | :-- |
| 8 | [Gerenciar o banco de dados do SQL Azure com o PowerShell](sql-database-command-line-tools.md) | Crie um grupo de recursos para nosso banco de dados SQL e recursos de Azure relacionados com o cmdlet New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" New-AzureRmResourceGroup-nome $resourceGroupName-local $resourceGroupLocation* Para obter mais informações, consulte usando o PowerShell Azure com o Azure Resource Manager (... / powershell-azure-recurso-manager.md). Para um exemplo de script, consulte Criar um banco de dados do SQL script do PowerShell (sql-banco de dados-get-iniciado-powershell.md create-a-sql-database-powershell-script). **Criar um servidor de banco de dados SQL** Crie um servidor de banco de dados SQL com o cmdlet New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Substitua o nome do seu servidor *server1* . Nomes de servidor devem ser exclusivos em todos os servidores do Azure SQL Database. Se o nome do servidor já está sendo usado, você receber um erro. Este comando pode levar alguns minutos para ser concluída. O resou | 2016-09-14 |
| 9 | [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell.md) |   Banco de dados fonte SQL (existente do banco de dados para copiar) - $sourceDbName = "db1" $sourceDbServerName = "server1" $sourceDbResourceGroupName = cópia de banco de dados SQL do "rg1" (a nova db seja criado) – $copyDbName = "db1_copy" $copyDbServerName = "server2" $copyDbResourceGroupName = "rg2" copiar um banco de dados no mesmo servidor – New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copiar um banco de dados para um servidor diferente – New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copie um banco de dados para um pool de banco de dados elástica - $poolName = "pool1" New-AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - nomedoservidor $sourceDbServerName - DatabaseName $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Criar um pool de banco de dados elástica com c#](sql-database-elastic-pool-create-csharp.md) |   Console. WriteLine ("servidor firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console. WriteLine ("firewall Server:" + fwr. FirewallRule.Id);  Console. WriteLine ("elástica pool...");  ERP ElasticPoolCreateOrUpdateResponse = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console. WriteLine ("pool elástica:" + ERP. ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console. WriteLine ("banco de dados:" + dbr. Database.Id);  Console. WriteLine ("Pressione qualquer tecla para continuar...");  Console.ReadKey();  } estático ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [Script do PowerShell para identificar bancos de dados adequados para um pool de banco de dados elástica](sql-database-elastic-pool-database-assessment-powershell.md) | Candidatos **para elástica pool de banco de dados, podemos excluir mestre e bancos de dados que já estão em um pool. Você pode adicionar mais bancos de dados à lista excluído abaixo conforme necessário** $ListOfDBs = Get-AzureRmSqlDatabase - nomedoservidor $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. DatabaseName - notin ("mestre") - e $_. CurrentServiceLevelObjectiveName - notin ("ElasticPool")- e $_. CurrentServiceObjectiveName - notin ("ElasticPool")} $outputConnectionString = "fonte de dados = $outputServerName; segurança integrada = false; inicial catálogo = $outputdatabaseName; Id de usuário = $outputDBUsername; Senha = $outputDBpassword "$destinationTableName ="resource_stats_output" **criar uma tabela no banco de dados de saída para o conjunto de métricas** $sql =" Se NOT EXISTS (Selecionar * de Objects object_id onde = OBJECT_ID(N'$($destinationTableName)') e digite na (N'U')) comece criar tabela $($destinationTableName) (nome_do_banco_de_dados varchar(128), slo varchar(20), end_time datetime, avg_cpu flutuar, avg_ | 2016-09-29 |
| 12 | [Tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos usando o portal do Azure](sql-database-get-started.md) |   ! Novo banco de dados do sql 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Clique em **Banco de dados SQL** para abrir a lâmina de banco de dados SQL. O conteúdo neste blade varia dependendo do número de suas assinaturas e seus objetos existentes (como servidores existentes).  ! Novo banco de dados do sql 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Na caixa de texto **nome do banco de dados** , forneça um nome para seu primeiro banco de dados - como "meu banco de dados". Uma marca de seleção verde indica que você forneceu um nome válido.  ! Novo banco de dados do sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Se você tiver várias assinaturas, selecione uma assinatura. 6. Em **grupo de recursos**, clique em **Criar novo** e forneça um nome para o primeiro grupo de recursos - como "meu--grupo de recursos". Uma marca de seleção verde indica que você forneceu um nome válido.  ! Novo banco de dados do sql 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Em **Selecionar fonte* | 2016-09-08 |
| 13 | [Experimente o banco de dados SQL: Usar c# para criar um banco de dados do SQL com a biblioteca de banco de dados SQL para .NET](sql-database-get-started-csharp.md) | **Criar um serviço principal para acessar recursos** O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e o capital de serviço que precisamos para autenticar nosso aplicativo C. O script saídas de valores que precisamos para o exemplo anterior de C. Para obter informações detalhadas, consulte usar o PowerShell do Azure para criar um objeto de serviço para acessar recursos (... / recurso-grupo-autenticar-serviço-principal.md).  Entrar no Azure.  AzureRmAccount adicionar se você tiver várias assinaturas, Tire comentários e definir para a assinatura que você deseja trabalhar.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}" Set-AzureRmContext - SubscriptionId $subscriptionId fornecer esses valores para seu novo aplicativo AAD.  $appName é o nome de exibição para o aplicativo, deve ser exclusivo no diretório.  $uri não precisa ser um uri real.  $secret é uma senha que você criar.  $appName = "{app-name}" $uri = "http://{app-name"} $secret = "{-senha de aplicativo}" criar um aplicativo AAD $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [Gerenciar bancos de dados do SQL Azure usando o portal do Azure](sql-database-manage-portal.md) | Para exibir ou atualizar as configurações do seu banco de dados, clique na configuração desejada na lâmina banco de dados SQL:! Configurações de banco de dados do SQL (. / media/sql-database-manage-portal/settings.png) **como encontrar um nome de servidor totalmente qualificado de bancos de dados do SQL?** Para exibir o nome do seu servidor de bancos de dados, clique em **Visão geral** no **banco de dados SQL** blade e observe o nome do servidor:! Configurações de banco de dados do SQL (. / media/sql-database-manage-portal/server-name.png) **como gerencio as regras de firewall para controlar o acesso ao meu SQL server e o banco de dados?** Para exibir, criar ou atualizar regras de firewall, clique em **Definir firewall server** na lâmina **banco de dados SQL** . Para obter detalhes, consulte Configurar uma regra de firewall de nível de servidor de banco de dados de SQL Azure usando o portal de Azure (sql-banco de dados-configurar-firewall-settings.md). ! regras de firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) **como posso alterar meu nível de desempenho ou nível de serviço do banco de dados SQL?** Para atualizar o nível de desempenho ou nível de serviço de um banco de dados do SQL, clique em * * camada de preços (s | 2016-09-20 |





## <a name="get-started"></a>Introdução

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 15 | [Cria locatários vários aplicativos com o banco de dados do SQL Azure com eficiência e isolamento](sql-database-build-multi-tenant-apps.md) | Saiba como o banco de dados SQL amplia locatários vários aplicativos |
| 16 | [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md) | Saiba como se conectar ao banco de dados do SQL no Azure usando o SQL Server Management Studio (SSMS). Em seguida, execute uma consulta de amostra usando Transact-SQL (T-SQL). |
| 17 | [Conectar ao banco de dados SQL usando .NET (c#)](sql-database-develop-dotnet-simple.md) | Use o código de amostra neste rápido começar a criar um aplicativo moderno com c# e feito por um poderoso banco de dados relacional na nuvem com o Azure SQL Database. |
| 18 | [Criar um novo pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-create-portal.md) | Como adicionar um pool de banco de dados elástica scalable a sua configuração de banco de dados SQL para facilitar a administração e compartilhamento entre muitos bancos de dados de recursos. |
| 19 | [Explore os tutoriais de banco de dados do SQL Azure](sql-database-explore-tutorials.md) | Saiba mais sobre os recursos e banco de dados SQL |
| 20 | [Tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos usando o portal do Azure](sql-database-get-started.md) | Saiba como configurar um servidor de banco de dados SQL lógico, regra de firewall do servidor, banco de dados SQL e dados de exemplo. Além disso, saiba como conectar-se com ferramentas de cliente, configurar usuários e configurar uma regra de firewall do banco de dados. |
| 21 | [Banco de dados do SQL Azure protege e protege](sql-database-helps-secures-and-protects.md) | Saiba como o banco de dados SQL ajuda seguro e proteger |
| 22 | [Banco de dados do SQL Azure aprende &amp; se adapta](sql-database-learn-and-adapt.md) | Saiba como o banco de dados SQL aprende e se adapta |
| 23 | [Escolha uma opção do SQL Server de nuvem: banco de dados do SQL Azure (PaaS) ou SQL Server no Azure VMs (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Saiba qual opção de SQL Server nuvem caiba seu aplicativo: banco de dados do SQL Azure (PaaS) ou SQL Server na nuvem em máquinas virtuais do Azure. |
| 24 | [Escalas de banco de dados do SQL Azure dinamicamente](sql-database-scale-on-the-fly.md) | Saiba como o banco de dados SQL escalas dinamicamente |
| 25 | [Explorar o início rápido de solução de banco de dados SQL Azure](sql-database-solution-quick-starts.md) | Saiba mais sobre soluções de banco de dados do SQL Azure |
| 26 | [Banco de dados do SQL Azure funciona no seu ambiente](sql-database-works-in-your-environment.md) | Saiba como banco de dados SQL ajuda, protege e protege |



## <a name="build-an-app"></a>Criar um aplicativo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 27 | [Solucionar problemas, diagnosticar e evitar erros de conexão de SQL e temporárias do banco de dados SQL](sql-database-connectivity-issues.md) | Saiba como solucionar problemas, diagnosticar e evitar que um erro de conexão de SQL ou temporárias no Azure SQL Database.  |
| 28 | [Conectar a um banco de dados do SQL com o Visual Studio](sql-database-connect-query.md) | Escreva um programa em c# para consultar e se conectar ao banco de dados SQL. Informações sobre endereços IP, cadeias de caracteres de conexão, seguro e gratuito Visual Studio. |
| 29 | [Portas, além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Conexões de cliente do ADO.NET para V12 de banco de dados do SQL Azure, às vezes, ignoram o proxy e interagem diretamente com o banco de dados. Portas diferente 1433 ficam importantes. |
| 30 | [Códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados](sql-database-develop-error-messages.md) | Saiba mais sobre os códigos de erro SQL para aplicativos de cliente do banco de dados SQL, como os erros comuns de conexão de banco de dados, problemas de cópia do banco de dados e erros gerais.  |
| 31 | [Conectar ao banco de dados SQL usando PHP no Windows](sql-database-develop-php-simple.md) | Apresenta um programa PHP de amostra que se conecta ao banco de dados do Azure SQL de um cliente do Windows e fornece links para os componentes de software necessário necessários pelo cliente. |
| 32 | [Experimente o banco de dados SQL: Usar c# para criar um banco de dados do SQL com a biblioteca de banco de dados SQL para .NET](sql-database-get-started-csharp.md) | Experimente o banco de dados SQL para desenvolvimento de aplicativos SQL e c# e criar um banco de dados do SQL Azure com c# usando a biblioteca de banco de dados SQL para .NET. |
| 33 | [Introdução aos recursos JSON no banco de dados do SQL Azure](sql-database-json-features.md) | Banco de dados do SQL Azure permite análise, consulta e formatar os dados em notação JSON JavaScript Object Notation (). |
| 34 | [Solucionar problemas de conexão ao banco de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Etapas para identificar e resolver erros comuns de conexão de banco de dados do SQL Azure. |
| 35 | [Erro "O banco de dados no servidor não está disponível atualmente" conectar ao banco de dados sql](sql-database-troubleshoot-connection.md) | Solucionar problemas de banco de dados no servidor não está disponível atualmente erro quando um aplicativo se conecta ao banco de dados SQL. |



## <a name="develop"></a>Desenvolver

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 36 | [Obter os valores necessários para um aplicativo para acessar o banco de dados SQL do código de autenticação](sql-database-client-id-keys.md) | Crie uma entidade de segurança do serviço para acessar o banco de dados SQL do código. |
| 37 | [Conectar ao banco de dados SQL usando Java com JDBC no Windows](sql-database-develop-java-simple.md) | Apresenta um exemplo de código de Java que você pode usar para se conectar ao banco de dados do SQL Azure. O exemplo usa JDBC e é executado em um computador de cliente do Windows. |
| 38 | [Conectar ao banco de dados SQL usando Node](sql-database-develop-nodejs-simple.md) | Apresenta um exemplo de código Node que você pode usar para se conectar ao banco de dados do SQL Azure. |
| 39 | [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md) | Saiba mais sobre bibliotecas de conectividade disponível e práticas recomendadas para aplicativos que se conectam ao banco de dados do SQL. |
| 40 | [Conectar ao banco de dados SQL usando Python](sql-database-develop-python-simple.md) | Apresenta um exemplo de código Python que você pode usar para se conectar ao banco de dados do SQL Azure. |
| 41 | [Conectar ao banco de dados SQL usando Ruby](sql-database-develop-ruby-simple.md) | Dê uma amostra de código Ruby, que você pode executar para se conectar ao banco de dados do SQL Azure. |
| 42 | [Introdução ao Temporal tabelas no banco de dados do SQL Azure](sql-database-temporal-tables.md) | Saiba como começar a usar usando tabelas Temporal no Azure SQL Database. |



## <a name="performance-and-scale"></a>Dimensionamento e desempenho

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 43 | [Supervisor de banco de dados SQL](sql-database-advisor.md) | O Supervisor de banco de dados do SQL Azure fornece recomendações para seus bancos de dados SQL existente que pode melhorar o desempenho de consulta atual. |
| 44 | [Supervisor de banco de dados do SQL usando o portal do Azure](sql-database-advisor-portal.md) | Você pode usar o Supervisor de banco de dados do SQL Azure no portal do Azure para revisar e implementar recomendações para seus bancos de dados SQL existente que pode melhorar o desempenho de consulta atual. |
| 45 | [Visão geral sobre referência de banco de dados do SQL Azure](sql-database-benchmark-overview.md) | Este tópico descreve a referência de banco de dados do SQL Azure usado para medir o desempenho do Azure SQL Database. |
| 46 | [Quando um pool de banco de dados elástica deve ser usado?](sql-database-elastic-pool-guidance.md) | Um pool de banco de dados elástica é um conjunto de recursos disponíveis que são compartilhados por um grupo de Elástico bancos de dados. Este documento fornece orientação para ajudar a avaliar o adequado ou usando um pool de elástica banco de dados para um grupo de bancos de dados. |
| 47 | [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md) | Explicação básica do recurso de ferramentas de banco de dados elástica do banco de dados do SQL Azure, incluindo fácil para executar o aplicativo de amostra. |
| 48 | [Banco de dados do SQL perguntas Frequentes](sql-database-faq.md) | Pergunte respostas aos clientes de perguntas comuns sobre bancos de dados de nuvem e o Azure SQL Database, o sistema de gerenciamento de banco de dados relacional (RDBMS) da Microsoft e o banco de dados como um serviço na nuvem. |
| 49 | [Introdução na memória (visualização) no banco de dados SQL](sql-database-in-memory.md) | Tecnologias do SQL na memória melhorar o desempenho da transação e cargas de trabalho de análise. Saiba como aproveitar essas tecnologias. |
| 50 | [Uso de memória OLTP (visualização) para melhorar o desempenho do aplicativo no banco de dados SQL](sql-database-in-memory-oltp-migration.md) | Adotar OLTP de na memória para melhorar o desempenho de transações em um banco de dados existente do SQL. |
| 51 | [Armazenamento OLTP na memória do monitor](sql-database-in-memory-oltp-monitoring.md) | Usarem estimativa e monitorar o armazenamento do XTP na memória, capacidade; resolver o erro de capacidade 41823 |
| 52 | [Operando o repositório de consulta no banco de dados do SQL Azure](sql-database-operate-query-store.md) | Saiba como operar o repositório de consulta no banco de dados do SQL Azure |
| 53 | [Compreensão de desempenho de banco de dados SQL](sql-database-performance.md) | O banco de dados do SQL Azure fornece ferramentas de desempenho para ajudá-lo a identificar as áreas que podem melhorar o desempenho de consulta atual. |
| 54 | [Desempenho para bancos de dados único e banco de dados do SQL Azure](sql-database-performance-guidance.md) | Este artigo pode ajudá-lo a determinar qual nível de serviço para escolher a seu aplicativo. Ele também recomenda maneiras de ajustar o aplicativo obter o máximo do Azure SQL Database. |
| 55 | [Visão de desempenho de consulta de banco de dados SQL Azure](sql-database-query-performance.md) | Monitoramento do desempenho de consulta identifica a maioria das consultas de consumo de CPU para um banco de dados do SQL Azure. |
| 56 | [Alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL](sql-database-scale-up.md) | Alterar o nível de serviço e nível de desempenho de um banco de dados do SQL Azure mostra como dimensionar seu banco de dados SQL para cima ou para baixo. Alterando o nível de preço de um banco de dados do SQL Azure. |
| 57 | [Monitoramento do desempenho de banco de dados no banco de dados do SQL Azure](sql-database-single-database-monitor.md) | Saiba mais sobre as opções para monitorar seu banco de dados com ferramentas Azure e exibições de gerenciamento dinâmico. |
| 58 | [Dicas de ajuste de desempenho do banco de dados SQL](sql-database-troubleshoot-performance.md) | Dicas para ajuste no banco de dados do Azure SQL por meio de avaliação e melhorias de desempenho. |
| 59 | [Como usar o processamento em lotes para melhorar o desempenho do aplicativo de banco de dados SQL](sql-database-use-batching-to-improve-performance.md) | O tópico fornece evidências que lotes operações de banco de dados bastante imroves a velocidade e escalabilidade de seus aplicativos do Azure SQL Database. Embora essas técnicas lotes funcionam para qualquer banco de dados do SQL Server, o foco do artigo é no Azure. |



## <a name="tools-for-scaling-out"></a>Ferramentas para dimensionar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 60 | [Padrões para vários locatários aplicativos SaaS e Azure SQL Database de design](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Este artigo discute os requisitos e padrões de arquitetura de dados comuns vários locatários do banco de aplicativos em execução em um ambiente de nuvem deve ser considerado e as compensações vários associadas a esses padrões. Ele também explica como banco de dados do SQL Azure, com seus pools Elástico e ferramentas elástica, ajudar atender a esses requisitos de maneira inflexível. |
| 61 | [Migrar bancos de dados existentes para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md) | Converter bancos de dados sharded para usar ferramentas de banco de dados elástica, criando um fragmentar Gerenciador de mapa |
| 62 | [Bancos de dados de nuvem scalable de construção](sql-database-elastic-database-client-library.md) | Criar aplicativos de banco de dados .NET scalable com a biblioteca de cliente elástica banco de dados |
| 63 | [Contadores de desempenho do Gerenciador de mapa fragmentar](sql-database-elastic-database-perf-counters.md) | ShardMapManager classe e dados dependentes roteamento contadores de desempenho |
| 64 | [Adicionando um fragmentar usando ferramentas de banco de dados elástica](sql-database-elastic-scale-add-a-shard.md) | Como usar elástica APIs de escala para adicionar novos fragmentos a um fragmentar conjunto. |
| 65 | [Implantar um serviço de divisão direta](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Divisão e mesclagem com ferramentas de banco de dados elástica |
| 66 | [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) | Como usar a classe de ShardMapManager nos aplicativos .NET para roteamento, um recurso do Elástico bancos de dados para o banco de dados do Azure SQL dependentes de dados |
| 67 | [Ferramentas de banco de dados elástica perguntas Frequentes](sql-database-elastic-scale-faq.md) | Perguntas frequentes sobre o dimensionamento de Elástico de banco de dados do SQL Azure. |
| 68 | [Elástico Glossário de ferramentas de banco de dados](sql-database-elastic-scale-glossary.md) | Explicação dos termos usados para ferramentas de banco de dados elástica |
| 69 | [Dimensionamento com o banco de dados do SQL Azure](sql-database-elastic-scale-introduction.md) | Software como um desenvolvedores de serviço (SaaS) possa criar facilmente Elástico, scalable bancos de dados na nuvem usando estas ferramentas |
| 70 | [Credenciais usadas para acessar a biblioteca de cliente elástica banco de dados](sql-database-elastic-scale-manage-credentials.md) | Como definir o nível certo de credenciais de administrador para somente leitura para os aplicativos de banco de dados elástica |
| 71 | [Vários fragmentar consultando](sql-database-elastic-scale-multishard-querying.md) | Execute consultas em fragmentos usando a biblioteca de cliente elástica banco de dados. |
| 72 | [Mover dados entre bancos de dados de nuvem dimensionada-out](sql-database-elastic-scale-overview-split-and-merge.md) | Explica como manipular fragmentos e mover dados através de um serviço de hospedagem interna usando o banco de dados Elástico APIs. |
| 73 | [Dimensionar a bancos de dados com o Gerenciador de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md) | Como usar o ShardMapManager, a biblioteca de cliente do banco de dados elástica |
| 74 | [Configuração de segurança de divisão direta](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurar x409 certificados de criptografia |
| 75 | [Atualizar um aplicativo para usar a biblioteca de cliente mais recente do banco de dados elástica](sql-database-elastic-scale-upgrade-client-library.md) | Atualizar aplicativos e bibliotecas usando o Nuget |
| 76 | [Biblioteca de cliente elástica do banco de dados com estrutura de entidades](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Usar biblioteca de cliente do banco de dados elástica e estrutura de entidade para bancos de dados de codificação |
| 77 | [Usando a biblioteca de cliente do banco de dados elástica com Dapper](sql-database-elastic-scale-working-with-dapper.md) | Usando a biblioteca de cliente do banco de dados elástica com Dapper. |
| 78 | [Aplicativos de vários locatários com ferramentas de banco de dados elástica e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Saiba como usar as ferramentas de banco de dados elástica junto com segurança em nível de linha para criar um aplicativo com uma camada de dados altamente escaláveis no Azure SQL Database que ofereça suporte a vários locatários fragmentos. |



## <a name="elastic-jobs"></a>Trabalhos Elástico

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 79 | [Criar e gerenciar escaladas check-out de bancos de dados do SQL Azure usando trabalhos Elástico (prévia)](sql-database-elastic-jobs-create-and-manage.md) | Percorra a criação e o gerenciamento de um trabalho de elástica banco de dados. |
| 80 | [Introdução ao trabalhos elástica banco de dados](sql-database-elastic-jobs-getting-started.md) | como usar trabalhos elástica banco de dados |
| 81 | [Gerenciar bancos de dados de nuvem dimensionada-out](sql-database-elastic-jobs-overview.md) | Ilustra o serviço de trabalho de banco de dados elástica |
| 82 | [Criar e gerenciar trabalhos de banco de dados elástica um banco de dados SQL usando o PowerShell (visualização)](sql-database-elastic-jobs-powershell.md) | PowerShell usada para gerenciar pools de banco de dados do Azure SQL |
| 83 | [Visão geral do banco de dados elástica trabalhos de instalação](sql-database-elastic-jobs-service-installation.md) | Percorra a instalação do recurso de trabalho elástica. |
| 84 | [Desinstalar os componentes do banco de dados elástica trabalhos](sql-database-elastic-jobs-uninstall.md) | Como desinstalar a ferramenta de trabalhos elástica banco de dados |



## <a name="elastic-pools"></a>Pools Elástico

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 85 | [O que é um pool de Elástico Azure?](sql-database-elastic-pool.md) | Gerencie centenas ou milhares de bancos de dados usando um pool. Um preço para um conjunto de unidades de desempenho pode ser distribuído no pool. Mover-se de bancos de dados ou reduzir em serão. |
| 86 | [Criar um pool de banco de dados elástica com c#](sql-database-elastic-pool-create-csharp.md) | Use técnicas de desenvolvimento do c# banco de dados para criar um pool de banco de dados elástica scalable no banco de dados do Azure SQL para que você possa compartilhar recursos em muitos bancos de dados. |
| 87 | [Criar um novo pool de banco de dados elástica com PowerShell](sql-database-elastic-pool-create-powershell.md) | Aprenda a usar o PowerShell para fora de escala Azure SQL Database recursos criando um pool de banco de dados elástica scalable para gerenciar vários bancos de dados. |
| 88 | [Script do PowerShell para identificar bancos de dados adequados para um pool de banco de dados elástica](sql-database-elastic-pool-database-assessment-powershell.md) | Um pool de banco de dados elástica é um conjunto de recursos disponíveis que são compartilhados por um grupo de bancos de dados Elástico. Este documento fornece um script do Powershell para ajudar a avaliar o adequado ou usando um pool de elástica banco de dados para um grupo de bancos de dados. |
| 89 | [Monitorar e gerenciar um pool de banco de dados elástica com c#](sql-database-elastic-pool-manage-csharp.md) | Use técnicas de desenvolvimento de banco de dados c# para gerenciar um pool de banco de dados elástica Azure SQL Database. |
| 90 | [Monitorar e gerenciar um pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-manage-portal.md) | Saiba como usar o portal do Azure e inteligência interna do banco de dados SQL para gerenciar, monitorar e tamanho direita um pool de banco de dados elástica scalable para otimizar o desempenho do banco de dados e gerenciar o custo. |
| 91 | [Monitorar e gerenciar um pool de banco de dados elástica com PowerShell](sql-database-elastic-pool-manage-powershell.md) | Aprenda a usar o PowerShell para gerenciar um pool de elástica de banco de dados. |
| 92 | [Monitorar e gerenciar um pool de elástica banco de dados com Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Use T-SQL para criar um banco de dados do SQL Azure em um pool de Elástico. Ou use T-SQL para mover o banco e sair pools. |
| 93 | [Informações sobre preços e cobrança de pool de banco de dados elástica](sql-database-elastic-pool-price.md) | Informações sobre preços específicos para pools de elástica banco de dados. |



## <a name="elastic-query"></a>Consulta elástica

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 94 | [Relatório em bancos de dados de nuvem dimensionada-out (prévia)](sql-database-elastic-query-getting-started.md) | como usar entre consultas de banco de dados do banco de dados |
| 95 | [Começar a usar consultas de bancos de dados (partição vertical) (prévia)](sql-database-elastic-query-getting-started-vertical.md) | como usar a consulta de banco de dados elástica com verticalmente particionado bancos de dados |
| 96 | [Relatórios em bancos de dados de nuvem dimensionada-out (prévia)](sql-database-elastic-query-horizontal-partitioning.md) | como configurar consultas elástica sobre partições horizontais |
| 97 | [Azure SQL Database elástica banco de dados visão geral de consultas (prévia)](sql-database-elastic-query-overview.md) | Visão geral do recurso de consulta elástica |
| 98 | [Consulta através de bancos de dados de nuvem com diferentes esquemas (prévia)](sql-database-elastic-query-vertical-partitioning.md) | como configurar consultas de bancos de dados em relação às partições verticais |



## <a name="elastic-transaction"></a>Transação elástica

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 99 | [Transações distribuídas em bancos de dados de nuvem](sql-database-elastic-transactions-overview.md) | Visão geral das transações de banco de dados elástica com banco de dados do SQL Azure |



## <a name="backup-and-recovery"></a>Recuperação e backup

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 100 | [Backups de banco de dados SQL](sql-database-automated-backups.md) | Saiba mais sobre banco de dados do SQL internas backups de banco de dados que permitem implementar um banco de dados do SQL Azure de volta para um ponto anterior no tempo ou copiar um banco de dados para um novo banco de dados em uma região geográfica (até 35 dias). |
| 101 | [Visão geral da continuidade de negócios com o banco de dados do Azure SQL](sql-database-business-continuity.md) | Saiba como Azure SQL Database dá suporte a continuidade de negócios de nuvem e a recuperação de banco de dados e ajuda a manter os aplicativos de nuvem essenciais em execução. |
| 102 | [Como restaurar uma única tabela de um backup do banco de dados do Azure SQL](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Saiba como restaurar uma única tabela de backup do banco de dados do Azure SQL. |
| 103 | [Criar um aplicativo de recuperação de nuvem usando replicação geográfica ativo no banco de dados SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Aprenda a criar soluções de recuperação de desastres de nuvem para planejamento de continuidade de negócios usando replicação geográfica para backup de dados de aplicativo com o Azure SQL Database. |
| 104 | [Restaurar um banco de dados do SQL Azure ou failover para um secundário](sql-database-disaster-recovery.md) | Saiba como recuperar um banco de dados de uma falta de Demand ou falha com o Azure SQL banco de dados ativo geográfica replicação e recursos de restauração de localização geográfica. |
| 105 | [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md) | Aprenda a orientação e práticas recomendadas para usar o banco de dados do Azure SQL para realizar exercícios de recuperação de desastres que o ajude a mantém sua missão críticas aplicativos de negócios e apresentam resiliência falhas e interrupções. |
| 106 | [Estratégias de recuperação de desastres para aplicativos usando Pool elástica do banco de dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Saiba como projetar sua solução de nuvem para recuperação de dados, escolhendo o padrão de failover à direita. |
| 107 | [Usando a classe RecoveryManager para corrigir problemas de mapa de fragmentar](sql-database-elastic-database-recovery-manager.md) | Usar a classe RecoveryManager para solucionar problemas com mapas de fragmentar |
| 108 | [Importar um arquivo BACPAC para criar um banco de dados do SQL Azure](sql-database-import.md) | Crie um banco de dados do SQL Azure importando um arquivo BACPAC existente. |
| 109 | [Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o Portal do Azure](sql-database-point-in-time-restore-portal.md) | Restaure um banco de dados do SQL Azure para um ponto anterior no tempo. |
| 110 | [Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo |
| 112 | [Recuperar um banco de dados do SQL Azure usando backups automatizados do banco de dados](sql-database-recovery-using-backups.md) | Saiba mais sobre a restauração de no momento, que permite que você reverter um banco de dados do SQL Azure para um ponto anterior no tempo (até 35 dias). |
| 113 | [Restaurar um banco de dados excluído do SQL Azure usando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Restaure um banco de dados do SQL Azure (Azure Portal) excluído. |
| 114 | [Restaurar um banco de dados do SQL Azure excluído usando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaure um banco de SQL Azure excluídos (PowerShell). |
| 115 | [Restaurar um banco de dados para um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar uma interrupção do Centro de dados](sql-database-troubleshoot-backup-and-restore.md) | Saiba como recuperar um banco de dados de nuvem de erros e interrupções usando backups e réplicas no Azure SQL Database. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 116 | [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL Database, migração de banco de dados, exportar o banco de dados, Exportar arquivo BACPAC, sqlpackage |
| 117 | [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando o SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL Database, migração de banco de dados, exportar o banco de dados, Exportar arquivo BACPAC, o Assistente para exportar aplicação de nível de dados |
| 118 | [Importar para o banco de dados SQL de um arquivo BACPAC usando SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL Database, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage |
| 119 | [Importar do BACPAC para o banco de dados do SQL usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database, banco de dados implantar, migração de banco de dados, banco de dados de importação, exportação de banco de dados, o Assistente de migração |
| 120 | [Migrar o banco de dados do SQL Server para o banco de dados do SQL usando implantar o banco de dados para o Assistente de banco de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL Database, migração de banco de dados, o Assistente de banco de dados do Microsoft Azure |
| 121 | [Migrar o banco de dados do SQL Server para o banco de dados do SQL Azure usando replicação de transação](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL Database, migração de banco de dados, importar banco de dados, replicação de transação |
| 122 | [Determine a compatibilidade de banco de dados SQL usando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL Database, migração de banco de dados, a compatibilidade de banco de dados SQL, SqlPackage |
| 123 | [Use o SQL Server Management Studio para compatibilidade de determinar banco de dados SQL antes da migração para o banco de dados do Azure SQL](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL Database, migração de banco de dados, a compatibilidade de banco de dados SQL, exportar assistente de aplicativo de camada de dados |
| 124 | [Use o Assistente de migração do SQL Azure para problemas de compatibilidade de banco de dados do SQL Server corrigir antes da migração para o banco de dados do Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL Database, migração de banco de dados, compatibilidade, o Assistente de migração do SQL Azure |
| 125 | [Migrar um banco de dados do SQL Server para o banco de dados do SQL Azure usando as ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL Database, migração de banco de dados, compatibilidade, o Assistente de migração do SQL Azure, SSDT |
| 126 | [Corrigir problemas de compatibilidade do banco de dados do SQL Server usando o SQL Server Management Studio antes de migração para o banco de dados SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL Database, migração de banco de dados, compatibilidade, o Assistente de migração do SQL Azure |
| 127 | [Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell](sql-database-export-powershell.md) | Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell |
| 128 | [Importar um arquivo BACPAC para criar um banco de dados do SQL Azure usando o PowerShell](sql-database-import-powershell.md) | Importar um arquivo BACPAC para criar um banco de dados do SQL Azure usando o PowerShell |
| 129 | [Carregar dados de CSV para depósito de dados do SQL Azure (arquivos simples)](sql-database-load-from-csv-with-bcp.md) | Para um tamanho de dados pequeno, usa bcp para importar dados para o banco de dados do SQL Azure. |
| 130 | [Mover bancos de dados entre servidores, entre assinaturas e e sair do Azure](sql-database-troubleshoot-moving-data.md) | Etapas rápidas para copiar, mover e migrar dados e bancos de dados no Azure SQL Database. |



## <a name="move-data-in-and-out"></a>Mover dados e reduzir

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 131 | [Migração de banco de dados do SQL Server para o banco de dados SQL na nuvem](sql-database-cloud-migrate.md) | Saiba quanto local do SQL Server banco de dados migração para o banco de dados do Azure SQL na nuvem. Use ferramentas de migração do banco de dados para testar a compatibilidade antes de migração de banco de dados. |
| 132 | [Copiar um banco de dados do SQL Azure](sql-database-copy.md) | Criar uma cópia de um banco de dados do SQL Azure |
| 133 | [Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o Portal do Azure](sql-database-export.md) | Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o Portal do Azure |



## <a name="security"></a>Segurança

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 134 | [Se conectando ao banco de dados SQL ou SQL Data Warehouse usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md) | Saiba como se conectar ao banco de dados SQL usando autenticação do Azure Active Directory. |
| 135 | [Sempre criptografadas: Proteger dados confidenciais no banco de dados SQL e armazenar suas chaves de criptografia no repositório de certificado do Windows](sql-database-always-encrypted.md) | Proteger dados confidenciais no seu banco de dados do SQL em minutos. |
| 136 | [Sempre criptografadas: Proteger dados confidenciais no banco de dados SQL e armazenar suas chaves de criptografia no Azure chave cofre](sql-database-always-encrypted-azure-key-vault.md) | Proteger dados confidenciais no seu banco de dados do SQL em minutos. |
| 137 | [Banco de dados SQL - suporte de clientes de nível inferior e alterações de ponto de extremidade IP para auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Saiba mais sobre o suporte de clientes de nível inferior do banco de dados SQL e IP alterações de ponto de extremidade para auditoria. |
| 138 | [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md) | Saiba como configurar o firewall para endereços IP que acessar bancos de dados do SQL Azure. |
| 139 | [Configurar regras de firewall de nível de servidor de banco de dados do Azure SQL usando a API REST](sql-database-configure-firewall-settings-rest.md) | Saiba como configurar o firewall para endereços IP que acessar bancos de dados do SQL Azure. |
| 140 | [Configurar regras de firewall de nível de banco de dados e de servidor de banco de dados de SQL Azure usando T-SQL](sql-database-configure-firewall-settings-tsql.md) | Saiba como configurar o firewall para endereços IP que acessar bancos de dados do SQL Azure. |
| 141 | [Introdução ao SQL banco de dados dinâmicos dados Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | Como começar com SQL banco de dados dinâmicos dados Masking no Portal do Azure |
| 142 | [Introdução ao SQL banco de dados dinâmicos dados Masking (Azure Portal clássico)](sql-database-dynamic-data-masking-get-started-portal.md) | Como começar com SQL banco de dados dinâmicos dados Masking no Portal de clássico do Azure |
| 143 | [Configurar regras de firewall do Azure SQL Database \- visão geral](sql-database-firewall-configure.md) | Saiba como configurar um firewall de banco de dados do SQL com regras de firewall de servidor e níveis de banco de dados para gerenciar o acesso. |
| 144 | [Tutorial do banco de dados SQL: criar contas de usuário para acessar e gerenciar um banco de dados de banco de dados SQL](sql-database-get-started-security.md) | Aprenda a criar contas de usuário para acessar e gerenciar um banco de dados. |
| 145 | [Autenticação de banco de dados do SQL e a autorização: concedendo acesso](sql-database-manage-logins.md) | Saiba mais sobre gerenciamento de segurança de banco de dados SQL, especificamente como gerenciar a segurança de acesso e faça logon de banco de dados por meio da conta principal do nível do servidor. |
| 146 | [Limitações e diretrizes de segurança de banco de dados do SQL azure](sql-database-security-guidelines.md) | Saiba mais sobre diretrizes de banco de dados do Microsoft Azure SQL e limitações relacionadas à segurança. |
| 147 | [Introdução ao detecção de ameaças de banco de dados SQL](sql-database-threat-detection-get-started.md) | Como começar com a detecção de ameaças de banco de dados do SQL no Portal do Azure |
| 148 | [Como executar tarefas administrativas comuns, como a redefinição de senha de administrador no banco de dados do SQL Azure](sql-database-troubleshoot-permissions.md) | Descreve como executar tarefas administrativas comuns no banco de dados SQL. Por exemplo, a redefinição de senha de administrador, concedendo e removendo o acesso. |



## <a name="manage-your-database"></a>Gerenciar seu banco de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 149 | [Introdução ao auditoria de banco de dados do SQL](sql-database-auditing-get-started.md) | Introdução ao auditoria de banco de dados do SQL |
| 150 | [Configurar uma regra de firewall de nível de servidor do Azure SQL Database usando o Portal do Azure](sql-database-configure-firewall-settings.md) | Saiba como configurar o firewall para endereços IP que acessar o Azure SQL server. |
| 151 | [Copiar um banco de dados de SQL Azure usando o portal do Azure](sql-database-copy-portal.md) | Criar uma cópia de um banco de dados do SQL Azure |
| 152 | [Gerenciar bancos de dados do SQL Azure usando automação do Azure](sql-database-manage-automation.md) | Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar bancos de dados do SQL Azure em escala. |
| 153 | [Visão geral: ferramentas de gerenciamento para banco de dados SQL](sql-database-manage-overview.md) | Compara ferramentas e opções de gerenciamento de banco de dados do SQL Azure |
| 154 | [Monitoramento de banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md) | Saiba como detectar e diagnosticar problemas de desempenho comuns usando modos de exibição de gerenciamento dinâmico para monitorar a Microsoft Azure SQL Database. |
| 155 | [Proteger seu banco de dados do SQL](sql-database-security.md) | Saiba mais sobre a segurança de banco de dados do SQL Azure e SQL Server, incluindo as diferenças entre a nuvem e SQL Server local quando se trata de autenticação, autorização, segurança de conexão, criptografia e conformidade. |



## <a name="extended-events"></a>Eventos estendidos

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 156 | [Código de destino do arquivo de evento para eventos estendidos no banco de dados SQL](sql-database-xevent-code-event-file.md) | Fornece PowerShell e Transact-SQL para uma amostra de código de duas fases que demonstra o destino do arquivo de evento em um evento estendido no Azure SQL Database. Armazenamento do Azure é uma parte necessária deste cenário. |
| 157 | [Ligar para o código de destino de Buffer para eventos estendidos no banco de dados SQL](sql-database-xevent-code-ring-buffer.md) | Fornece um exemplo de código Transact-SQL que é feito fácil e rápida pelo uso do destino de Buffer em anel, no Azure SQL Database. |
| 158 | [Eventos estendidos no banco de dados SQL](sql-database-xevent-db-diff-from-svr.md) | Descreve eventos estendidos (XEvents) no banco de dados do SQL Azure e como sessões de evento diferem ligeiramente sessões de evento no Microsoft SQL Server. |



## <a name="geo-replication"></a>Replicação de localização geográfica

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 159 | [Iniciar um failover planejado ou para o banco de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md) | Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o portal do Azure |
| 160 | [Iniciar um failover planejado ou para o banco de dados do SQL Azure com o PowerShell](sql-database-geo-replication-failover-powershell.md) | Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o PowerShell |
| 161 | [Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL |
| 162 | [Visão geral: Banco de dados ativo geográfica replicação do SQL](sql-database-geo-replication-overview.md) | Replicação de localização geográfica ativa permite instalação 4 réplicas de seu banco de dados em qualquer um dos data centers Azure. |
| 163 | [Configurar replicação geográfica do banco de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-portal.md) | Configurar a localização geográfica-replicação de banco de dados do SQL Azure usando o portal do Azure |
| 164 | [Configurar replicação geográfica para o banco de dados do SQL Azure com o PowerShell](sql-database-geo-replication-powershell.md) | Configurar replicação de localização geográfica ativa para o banco de dados do SQL Azure usando o PowerShell |
| 165 | [Como gerenciar a segurança do Azure SQL Database após a recuperação de dados](sql-database-geo-replication-security-config.md) | Este tópico explica considerações de segurança para gerenciar a segurança após uma restauração de banco de dados ou um failover. |
| 166 | [Configurar replicação geográfica para o banco de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurar a localização geográfica-replicação de banco de dados do SQL Azure com Transact-SQL |
| 167 | [Localização geográfica-restaurar um banco de dados do SQL Azure de um backup de localização geográfica redundante usando o Portal do Azure](sql-database-geo-restore-portal.md) | Localização geográfica-restaurar um banco de dados do SQL Azure de um backup geográfica redundantes (Azure Portal). |
| 168 | [Restaurar um banco de dados do SQL Azure de um backup geográfica redundante usando o PowerShell](sql-database-geo-restore-powershell.md) | Restaurar um banco de dados do SQL Azure em um novo servidor de um backup geográfica redundantes |



## <a name="upgrade"></a>Atualizar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 169 | [Melhor desempenho de consulta com compatibilidade 130 nível no banco de dados do SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Etapas e ferramentas para determinar qual nível de compatibilidade é melhor para seu banco de dados no banco de dados do Azure SQL ou Microsoft SQL Server |
| 170 | [Gerenciando atualizações sem interrupção de aplicativos de nuvem usando o banco de dados ativo geográfica replicação do SQL](sql-database-manage-application-rolling-upgrade.md) | Saiba como usar replicação geográfica Azure SQL Database para dar suporte a atualizações on-line do seu aplicativo de nuvem. |
| 171 | [Atualização para V12 de banco de dados do SQL Azure usando o portal do Azure](sql-database-upgrade-server-portal.md) | Explica como atualizar para V12 de banco de dados do SQL Azure incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor de V11 migrando seus bancos de dados diretamente em um pool de elástica banco de dados usando o portal do Azure. |
| 172 | [Atualização para V12 de banco de dados do SQL Azure usando o PowerShell](sql-database-upgrade-server-powershell.md) | Explica como atualizar para V12 de banco de dados do SQL Azure incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor de V11 migrando seus bancos de dados diretamente em um pool de elástica banco de dados usando o PowerShell. |
| 173 | [Planejar e preparar a atualização para V12 de banco de dados SQL](sql-database-v12-plan-prepare-upgrade.md) | Descreve os preparativos e limitações envolvidas na atualização para versão V12 do Azure SQL Database. |
| 174 | [O que há de novo no V12 de banco de dados SQL](sql-database-v12-whats-new.md) | Descreve por que terá a vantagem de sistemas de negócios que estão usando o banco de dados do SQL Azure na nuvem de atualização para versão V12 agora. |
| 175 | [Perguntas Frequentes do pôr do sol da Web e Business Edition](sql-database-web-business-sunset-faq.md) | Descubra quando os bancos de dados da Web do SQL Azure e Business serão descartados e saiba mais sobre os recursos e funcionalidades de novos níveis de serviço. |



## <a name="tools"></a>Ferramentas

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 176 | [Gerenciar o banco de dados do SQL Azure com o PowerShell](sql-database-command-line-tools.md) | Gerenciamento de banco de dados do SQL Azure com o PowerShell. |
| 177 | [Tutorial do banco de dados SQL: conectar o Excel a um banco de dados do SQL Azure e criar um relatório](sql-database-connect-excel.md) | Saiba como conectar o Microsoft Excel ao banco de dados do SQL Azure na nuvem. Importar dados para o Excel para relatar e exploração de dados. |
| 178 | [Criar um banco de dados do SQL e executar tarefas comuns de configuração de banco de dados com os cmdlets do PowerShell](sql-database-get-started-powershell.md) | Saiba como criar um banco de dados do SQL com o PowerShell. Tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell. |
| 179 | [Introdução à sincronização de dados do SQL Azure (visualização)](sql-database-get-started-sql-data-sync.md) | Este tutorial ajuda você a começar a usar a sincronização de dados do SQL Azure (visualização). |
| 180 | [Gerenciando o banco de dados do SQL Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Saiba como usar o SQL Server Management Studio para gerenciar bancos de dados e servidores de banco de dados SQL. |
| 181 | [Gerenciar bancos de dados do SQL Azure usando o portal do Azure](sql-database-manage-portal.md) | Saiba como usar o Portal do Azure para gerenciar um banco de dados relacional na nuvem usando o Portal do Azure. |
| 182 | [Alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL com o PowerShell](sql-database-scale-up-powershell.md) | Alterar o nível de serviço e nível de desempenho de um banco de dados do SQL Azure mostra como dimensionar seu banco de dados SQL para cima ou para baixo com o PowerShell. Alterando o nível de preço de um banco de dados do SQL Azure com o PowerShell. |
| 183 | [Usar o SQL Server Management Studio no Azure RemoteApp para se conectar ao banco de dados SQL](sql-database-ssms-remoteapp.md) | Use este tutorial para saber como usar o SQL Server Management Studio no Azure RemoteApp para segurança e desempenho ao se conectar ao banco de dados SQL |



## <a name="reference"></a>Referência

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 184 | [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md) | Lista o número de versão mínima para cada driver que programas cliente podem usar para se conectar ao banco de dados do Azure SQL ou Microsoft SQL Server. É fornecido um link para obter informações de versão sobre drivers lançadas pela comunidade, em vez de pela Microsoft. |
| 185 | [Limites de recursos de banco de dados do SQL Azure](sql-database-resource-limits.md) | Esta página descreve alguns limites de recursos comuns do Azure SQL Database. |
| 186 | [Diferenças de Transact-SQL de banco de dados do SQL Azure](sql-database-transact-sql-information.md) | Instruções de Transact-SQL que são inferiores totalmente compatíveis com o banco de dados do Azure SQL |



## <a name="miscellaneous"></a>Diversos

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 187 | [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell.md) | Criar cópia de um banco de dados do SQL Azure usando o PowerShell |
| 188 | [Copiar um banco de dados do SQL Azure com Transact-SQL](sql-database-copy-transact-sql.md) | Criar cópia de um banco de dados do SQL Azure com Transact-SQL |
| 189 | [Diretrizes e limitações de geral de banco de dados do SQL Azure](sql-database-general-limitations.md) | Esta página descreve algumas limitações gerais para banco de dados do SQL Azure, bem como áreas de interoperabilidade e suporte. |
| 190 | [Recomendações de nível de preço de banco de dados SQL](sql-database-service-tier-advisor.md) | Ao alterar preços níveis no portal do Azure, recomendações de nível de preço são contanto que recomendamos a camada que é melhor adequado para execução de carga de trabalho do Azure SQL banco de dados existente. Níveis de preços descrevem o nível de desempenho e nível de serviço de um banco de dados do SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Artigos relacionados de outros serviços do Azure

- [Fazer backup de seu aplicativo de serviços de aplicativo do Azure no Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Ferramentas de documentação

- [Atualizações anunciadas para o banco de dados do SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Pesquisar todos os tipos de documentação do Microsoft Azure, com filtros](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Gráficos de caminho de aprendizagem

- [Gráficos de caminho de aprendizagem para todos os serviços do Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Caminho de aprendizagem: Saiba banco de dados do SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Caminho de aprendizagem: Dimensionamento Elástico banco de dados SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


