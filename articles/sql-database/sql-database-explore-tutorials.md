<properties
   pageTitle="Explore os tutoriais de banco de dados do SQL Azure | Microsoft Azure"
   description="Saiba mais sobre os recursos e banco de dados SQL"
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
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Explore os tutoriais de banco de dados do SQL Azure

Os links a seguir levam você a uma visão geral de cada área de recurso listados e um tutorial passo-por-início simple para cada área. Escopo de solução rápida inícios que demonstram o uso do banco de dados SQL em uma solução completa com base em cenários do mundo real, consulte [Azure SQL banco de dados solução rápida começa](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Usando o SQL Server Management Studio

Os seguintes tutoriais, você aprenderá sobre como usar o SQL Server Management Studio para administrar e consultar Azure SQL Database.


> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Tutorial  | Descrição  |
|---|---|---|
| [Conectar ao banco de dados do SQL Azure usando um logon principal do nível de servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Neste tutorial, você aprenderá para se conectar ao banco de dados do SQL Azure usando um logon principal do nível do servidor.|
| [Conectar ao banco de dados do SQL Azure como um usuário](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Neste tutorial, você aprenderá como se conectar a um banco de dados do SQL Azure usando uma conta de usuário de nível de banco de dados.|
||||

## <a name="elastic-pools"></a>Pools Elástico

Os seguintes tutoriais, você aprenderá sobre usando [pools Elástico](sql-database-elastic-pool.md) para gerenciar as metas de desempenho para vários bancos de dados com padrões de uso amplamente imprevisíveis e variáveis.

| Tutorial  | Descrição  |
|---|---|---|
| [Criar um pool de Elástico](sql-database-elastic-pool-create-portal.md) | Neste tutorial, você saiba como criar um pool scalable de bancos de dados do SQL Azure. |
| [Monitorar um banco de dados Elástico](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Neste tutorial, você aprenderá monitorar a um banco de dados Elástico individual para problemas potenciais. |
| [Adicionar um alerta para um recurso de pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Neste tutorial, você saiba como adicionar regras para os recursos que envia email para as pessoas ou cadeias de alerta para pontos de extremidade de URL quando o recurso atinge um limite de utilização que você configurar. |
| [Mover um banco de dados em um pool Elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Neste tutorial, você saiba como mover um banco de dados em um pool Elástico. |
| [Tira um banco de dados um pool Elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Neste tutorial, você aprenderá a tira um banco de dados um pool Elástico. |
| [Alterar as configurações de desempenho de um pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Neste tutorial, você aprenderá ajustar os limites de armazenamento e desempenho para o pool. |
||||

## <a name="elastic-database-jobs"></a>Trabalhos de banco de dados elástica

Os seguintes tutoriais, você aprenderá sobre o uso de [trabalhos elástica de banco de dados](sql-database-elastic-jobs-overview.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md) | Neste tutorial, você saiba como usar os recursos de ferramentas de banco de dados elástica usando um aplicativo de sharded simples. |
| [Introdução ao trabalhos Elástico Azure SQL Database](sql-database-elastic-jobs-getting-started.md)  | Neste tutorial, você vai aprender como a como criar e gerenciar trabalhos gerenciar um grupo de bancos de dados relacionados.  | 
||||

## <a name="elastic-queries"></a>Consultas elástica

Os seguintes tutoriais, você aprenderá a execução de [consultas elástica](sql-database-elastic-query-overview.md). 

| Tutorial  | Descrição  |
|---|---|---|
| [Consultando um banco de dados (sharded) horizontalmente particionado)](sql-database-elastic-query-getting-started.md) | Neste tutorial, você aprenderá a criar relatórios de todos os bancos de dados em um banco de dados (sharded) horizontalmente particionado usando o [query elástica](sql-database-elastic-query-overview.md) |
| [Consultando um banco de dados particionado verticalmente)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Neste tutorial, você aprenderá a criar relatórios de todos os bancos de dados em um verticalmente banco de dados usando o [query elástica](sql-database-elastic-query-overview.md) |
| [Migrar um banco de dados existente para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md)| Neste tutorial, você aprende a dimensionar horizontalmente o banco de dados (fragmentar) um SQL Azure. |
||||

## <a name="performance-optimization"></a>Otimização de desempenho

Os seguintes tutoriais, você aprenderá a otimizar o [desempenho de bancos de dados único](sql-database-performance-guidance.md). Para otimizar o desempenho de vários bancos de dados, consulte [pools Elástico](#elastic-pools).

| Tutorial  | Descrição  |
|---|---|---|
| [Alterar o nível de desempenho e nível de serviço do seu banco de dados](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Neste tutorial, você aprenderá dimensionar ou dimensionar o desempenho de um banco de dados do SQL Azure usando níveis de serviço. |
| [Visão de desempenho consulta de Supervisor banco de dados SQL](sql-database-performance.md#performance-overview) | Neste tutorial, você saiba como abrir e usar uma visão de desempenho consulta de Supervisor de banco de dados SQL.|
| [Recomendações de desempenho de Supervisor de banco de dados SQL](sql-database-advisor.md#viewing-recommendations) | Neste tutorial, você saiba como exibir e aplicar recomendações de desempenho de Supervisor de banco de dados do SQL. |
| [Revisar superior CPU consumir consultas](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Neste tutorial, você saiba como usar uma visão de desempenho consulta de Supervisor de banco de dados SQL para examinar superior CPU consumir consultas.|
| [Exibindo detalhes de consulta individual](sql-database-query-performance.md#viewing-individual-query-details)| Neste tutorial, você saiba como usar uma visão de desempenho consulta de Supervisor de banco de dados SQL para exibir os detalhes de desempenho de consulta individuais.|
||||

## <a name="sql-database-migration-and-archive"></a>Arquivamento e migração de banco de dados SQL 

Os seguintes tutoriais, você aprenderá sobre como [migrar um banco de dados do SQL Server existente para Azure SQL Database](sql-database-cloud-migrate.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Detectar problemas de compatibilidade usando as ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Neste tutorial, você saiba como usar as ferramentas de dados do SQL Server para Visual Studio para determinar a compatibilidade do Azure SQL Database. |
| [Corrigindo problemas de compatibilidade usando as ferramentas de dados do SQL Server para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Neste tutorial, você saiba como usar as ferramentas de dados do SQL Server para Visual Studio para corrigir problemas de compatibilidade do Azure SQL Database. |
| [Determine a compatibilidade de banco de dados SQL usando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Neste tutorial, você saiba como usar o utilitário de linha de comando SQLPackage.exe para determinar a compatibilidade do Azure SQL Database.|
| [Determine a compatibilidade de banco de dados SQL usando o SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Neste tutorial, você saiba como usar o SQL Server Management Studio para determinar a compatibilidade do Azure SQL Database.|
| [Migrar o banco de dados do SQL Server para o banco de dados do SQL usando implantar o banco de dados para o Assistente de banco de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Neste tutorial, você aprenderá como migrar um banco de dados do SQL Server compatível para banco de dados do SQL Azure usando o banco de dados implantar ao Assistente de banco de dados do Microsoft Azure no SQL Server Management Studio.
| [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando o SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Neste tutorial, você aprenderá como exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o Assistente para exportação de aplicativo de camada de dados no SQL Server Management Studio.|
| [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Neste tutorial, você aprenderá como exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o utilitário de linha de comando SQLPackage.exe.|
| [Importar um arquivo BACPAC no banco de dados do SQL Azure usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Neste tutorial, você aprenderá como importar um banco de dados para o banco de dados do Azure SQL de um arquivo BACPAC usando o Assistente para exportação de aplicativo de camada de dados no SQL Server Management Studio. |
| [Importar um arquivo BACPAC no banco de dados do SQL Azure usando SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Neste tutorial, você aprenderá como importar um banco de dados para o banco de dados do Azure SQL de um arquivo BACPAC usando o utilitário de linha de comando SQLPackage. |
| [Importar um arquivo BACPAC no banco de dados do SQL Azure usando o portal do Azure](sql-database-import.md) | Neste tutorial, você aprenderá como importar um banco de dados para o banco de dados do Azure SQL de um arquivo BACPAC que é armazenado em um blob Azure usando o Portal do Azure.|
| [Importar um arquivo BACPAC no banco de dados do SQL Azure usando o PowerShell](sql-database-import-powershell.md) | Neste tutorial, você aprenderá como importar um banco de dados para o banco de dados do Azure SQL de um arquivo BACPAC usando o PowerShell.|
| [Arquivar um banco de dados do SQL Azure usando o portal do Azure](sql-database-export.md#export-your-database) | Neste tutorial, você aprenderá arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o portal do Azure. |
| [Arquivar um banco de dados do SQL Azure usando o PowerShell](sql-database-export-powershell.md) | Neste tutorial, você aprenderá arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o PowerShell. |
| [Copiar um banco de dados do SQL Azure usando o portal do Azure](sql-database-copy.md#copy-your-sql-database) | Neste tutorial, você saiba como copiar um banco de dados do SQL Azure usando o portal do Azure. |
| [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Neste tutorial, você saiba como copiar um banco de dados do SQL Azure usando o PowerShell. |
| [Copiar um banco de dados do SQL Azure com Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Neste tutorial, você saiba como copiar um banco de dados do SQL Azure com Transact-SQL. |
||||

##<a name="develop"></a>Desenvolver

Os seguintes tutoriais, você aprenderá sobre o [Desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md) e usar [bibliotecas de conectividade](sql-database-libraries.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Conectar ao banco de dados SQL usando .NET (c#)](sql-database-develop-dotnet-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando c#. |
| [Conectar ao banco de dados SQL usando Java](sql-database-develop-java-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando Java. |
| [Conectar ao banco de dados SQL usando Node](sql-database-develop-nodejs-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando Node. |
| [Conectar ao banco de dados SQL usando PHP](sql-database-develop-php-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando PHP. |
| [Conectar ao banco de dados SQL usando Python](sql-database-develop-python-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando Python. |
| [Conectar ao banco de dados SQL usando Ruby](sql-database-develop-ruby-simple.md) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando Ruby. |
||||
 
## <a name="database-access"></a>Acesso de banco de dados

Os seguintes tutoriais, você aprenderá a [criar e gerenciar usuários e logon](sql-database-manage-logins.md).

| Tutorial  | Descrição  |
|---|---|---|
| [Criar uma regra de firewall de nível de servidor de banco de dados do Azure SQL usando o portal do Azure](sql-database-configure-firewall-settings.md)  | Neste tutorial, você saiba como configurar um firewall de nível de servidor de banco de dados SQL usando o portal do Azure.  |
| [Criar uma regra de firewall de nível de banco de dados com Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Neste tutorial, você aprenderá a criar uma regra de firewall de nível de banco de dados com Transact-SQL.|
| [Gerenciar regras de firewall de nível de servidor usando Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Neste tutorial, você aprenderá como gerenciar um firewall de nível de servidor de banco de dados de SQL Azure com Transact-SQL.|
| [Gerenciar regras de firewall de nível de servidor usando o PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Neste tutorial, você aprenderá como gerenciar um firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell.|
| [Gerenciar regras de firewall de nível de servidor usando a API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Neste tutorial, você aprenderá como gerenciar um firewall de nível de servidor de banco de dados do Azure SQL usando a API REDEFINIR.|
| [Conectar ao banco de dados do SQL Azure usando um logon principal do nível de servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Neste tutorial, você aprenderá para se conectar ao banco de dados do SQL Azure usando um logon principal do nível do servidor.|
| [Concedendo acesso de banco de dados para um logon] (sql-database-manage-logins.md#granting-database-access-to-a-login() | Neste tutorial, você aprenderá conceder acesso de banco de dados para um logon de nível de servidor.|
| [Criar novo usuário de banco de dados usando o SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Neste tutorial, você saiba como criar um novo usuário de banco de dados em um banco de dados existente usando o SSMS.|
| [Conceder permissões de db_owner novo usuário de banco de dados](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Neste tutorial, você saiba como conceder permissões de db_owner de usuário de um banco de dados existente.|
| [Conectar ao banco de dados do SQL Azure como um usuário](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Neste tutorial, você saiba como se conectar a um banco de dados do SQL Azure usando uma conta de usuário de nível de banco de dados.|
||||


## <a name="data-security"></a>Segurança de dados

Os seguintes tutoriais, você aprenderá a [proteção de dados do Azure SQL Database](sql-database-security.md). 

| Tutorial  | Descrição  |
|---|---|---|
| [Habilitar a detecção de ameaças para seu banco de dados usando o portal do Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Neste tutorial, você saiba como configurar a detecção de ameaças no portal do Azure para seu banco de dados.|
| [Usar dados confidenciais sempre criptografadas de seguro](sql-database-always-encrypted-azure-key-vault.md) | Neste tutorial, você usará o assistente sempre criptografados para proteger dados confidenciais em um banco de dados do SQL Azure.|
| [Dados de senstive seguras usando criptografia de dados transparente](https://msdn.microsoft.com/library/dn948096.aspx)| Neste tutorial, você saiba como usar a criptografia de dados transparente para proteger os dados de senstive.|
| [Criptografar uma coluna de dados](https://msdn.microsoft.com/library/ms179331.aspx)| Neste tutorial, você aprenderá criptografar uma coluna de dados com Transact-SQL.|
| [Configurar a máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | Neste tutorial, você saiba como configurar a máscara de dados dinâmicos para seu banco de dados do SQL Azure. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Continuidade de negócios e consulta escala-Out

Os seguintes tutoriais, você aprenderá sobre como usar [localização geográfica restauração e replicação de localização geográfica ativa](sql-database-business-continuity.md) para reccover de erros, continuidade de negócios e para consulta escala-out.

| Tutorial  | Descrição  |
|---|---|---|
| [Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o Portal do Azure](sql-database-point-in-time-restore-portal.md)| Neste tutorial, você saiba como restaurar o banco de dados para um ponto anterior no tempo usando o portal do Azure.|
| [Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Neste tutorial, você aprenderá a restaurar seu banco de dados para um ponto anterior no tempo usando o PowerShell|
| [Restaurar um banco de dados excluído do SQL Azure usando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Neste tutorial, você saiba como restaurar um banco de dados excluído usando o portal do Azure.|
| [Restaurar um banco de dados excluído do SQL Azure usando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Neste tutorial, você saiba como restaurar um banco de dados excluído usando o PowerShell.|
| [Configurar a localização geográfica-replicação de banco de dados do SQL Azure usando o portal do Azure](sql-database-geo-replication-portal.md)| Neste tutorial, você saiba como configurar replicação de localização geográfica ativa usando o portal do Azure.|
| [Configurar a localização geográfica-replicação de banco de dados do SQL Azure usando o PowerShell](sql-database-geo-replication-powershell.md)| Neste tutorial, você aprenderá a configurar replicação de localização geográfica ativa usando o PowerShell.|
| [Configurar a localização geográfica-replicação de banco de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-transact-sql.md)| Neste tutorial, você aprenderá a configurar replicação de localização geográfica ativa usando Transact-SQL.|
| [Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o portal do Azure](sql-database-geo-replication-failover-portal.md) | Neste tutorial, você vai aprender como failover para uma réplica secundário replicado geográfica usando o portal do Azure.|
| [Iniciar um failover planejado ou para o banco de dados do SQL Azure usando o PowerShell](sql-database-geo-replication-failover-powershell.md) | Neste tutorial, você vai aprender como failover para uma réplica secundário replicado geográfica usando o PowerShell.|
| [Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Neste tutorial, você vai aprender como failover para uma réplica secundário replicado geográfica usando Transact-SQL.|
||||

## <a name="data-sync"></a>Sincronização de dados

Neste tutorial, você aprenderá sobre a [Sincronização de dados](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial  | Descrição  |
|---|---|---| 
| [Introdução à sincronização de dados do SQL Azure (visualização)](sql-database-get-started-sql-data-sync.md)  | Neste tutorial, você vai aprender os conceitos básicos da sincronização de dados do SQL Azure usando o Portal de clássico do Azure. |
||||

## <a name="next-steps"></a>Próximas etapas

[Explorar o início rápido de solução de banco de dados SQL Azure](sql-database-solution-quick-starts.md)
