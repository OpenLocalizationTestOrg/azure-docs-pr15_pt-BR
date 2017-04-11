<properties
    pageTitle="Replicação de localização geográfica ativa do banco de dados do SQL Azure"
    description="Replicação de localização geográfica ativa permite instalação 4 réplicas de seu banco de dados em qualquer um dos data centers Azure."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Visão geral: Banco de dados ativo geográfica replicação do SQL

Replicação de localização geográfica ativa permite que você configure até quatro bancos de dados secundários legíveis em locais de centro de dados iguais ou diferentes (regiões). Bancos de dados secundários estão disponíveis para consultar e failover no caso de uma interrupção do Centro de dados ou a impossibilidade de se conectar ao banco de dados principal.

>[AZURE.NOTE] Localização geográfica-replicação ativa (legíveis secundários) agora está disponível para todos os bancos de dados em todos os níveis de serviço. Em abril de 2017, o tipo de secundário não legível será desativado e bancos de dados não legível existentes serão automaticamente atualizados para secundários legíveis.

 Você pode configurar replicação de localização geográfica ativa usando o [portal do Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), ou o [API REST - criar banco de dados de atualização ou](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurar: Portal do Azure](sql-database-geo-replication-portal.md)
- [Configurar: PowerShell](sql-database-geo-replication-powershell.md)
- [Configurar: T-SQL](sql-database-geo-replication-transact-sql.md)

Se, por qualquer motivo falhar seu banco de dados principal ou simplesmente precisa ser colocado offline, você pode *failover* para qualquer um dos seus bancos de dados secundários. Quando failover é ativado para um banco de dados secundários, todos os outros secundários são automaticamente vinculados no novo principal.

Você pode failover para um secundário usando o [portal do Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), a [API REST - Failover planejado](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)ou [API REST - Failover não planejado](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Failover: Portal Azure](sql-database-geo-replication-failover-portal.md)
- [Failover: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Failover: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Após o failover, verifique os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo principal. Para obter detalhes, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md).


O recurso de replicação de localização geográfica ativa implementa um mecanismo para fornecer redundância de banco de dados dentro da mesma região do Microsoft Azure ou em diferentes regiões (redundância geográfica). Replicação de localização geográfica ativa assíncrona replica confirmadas transações de banco de dados para até quatro cópias do banco de dados em diferentes servidores, usando isolamento de instantâneo confirmada (RCSI) para isolamento de leitura. Quando a replicação de localização geográfica ativa estiver configurada, um banco de dados secundário é criado no servidor especificado. O banco de dados original torna-se o banco de dados principal. O banco de dados principal assíncrona replica transações confirmadas para cada banco de dados secundários. Somente as transações completas são duplicadas. Enquanto a qualquer momento determinado, o banco de dados secundário pode ser um pouco atrás de um banco de dados principal, os dados secundários certamente nunca tenha transações parciais. Os dados específicos do RPO podem ser encontrados na [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

Uma das principais vantagens de ativo geográfica replicação é que ele fornece uma solução nível de banco de dados de recuperação com o tempo de recuperação baixa. Quando você coloca o banco de dados secundário em um servidor em uma região diferente, adicione resiliência máxima ao seu aplicativo. Redundância de região de cruz permite que aplicativos recuperar da perda permanente de um data center inteiro ou partes de um data center causados por desastres naturais, graves erros humanos ou age mal-intencionados. A figura a seguir mostra que um exemplo de ativo geográfica replicação configurado com um primário na região Centro Norte dos EUA e secundários na região Sul Central-nos.

![Relação de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Outro benefício importante é que os bancos de dados secundários são legíveis e podem ser usados para transferir cargas de trabalho somente leitura como trabalhos de relatório. Se você pretende usar o banco de dados secundário para balanceamento de carga, pode criá-lo na mesma região do principal. Criando um secundário na mesma região, não aumentará resistência do aplicativo a falhas graves.  

Outros cenários onde replicação de localização geográfica ativa pode ser usada incluem:

- **Migração de banco de dados**: você pode usar replicação de localização geográfica ativa para migrar um banco de dados de um servidor para outro online com o tempo de inatividade mínimo.
- **Atualizações de aplicativo**: você pode criar um secundário extra como uma cópia de volta falhas durante as atualizações de aplicativo.

Para obter continuidade de negócios real, adicionar redundância de banco de dados entre data centers é somente parte da solução. Recuperar uma aplicativo (serviço) ponta a ponta após uma falha grave requer a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software do cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes são flexíveis para as mesmas falhas e ficam disponíveis dentro o objetivo de tempo de recuperação (RTO) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender os recursos que eles oferecem e garantias. Em seguida, você deverá executar etapas adequadas para garantir que seu serviço funciona durante o failover dos serviços do qual ele depende. Para obter mais informações sobre as soluções de design para recuperação de dados, consulte [Criando soluções de nuvem para desastres recuperação usando Active geográfica replicação](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Ativos recursos de replicação geográfica
O recurso de replicação de localização geográfica ativa fornece os seguintes recursos essenciais:

- **Replicação assíncrona automáticas**: você só pode criar um banco de dados secundário, adicionando um banco de dados existente. O secundário só pode ser criado em um servidor de banco de dados do Azure SQL diferente. Uma vez criado, o banco de dados secundário é preenchido com os dados copiados do banco de dados principal. Esse processo é conhecido como propagação. Depois de banco de dados secundário foi criado e propagado, atualizações para o banco de dados primário são assíncrona duplicadas ao banco de dados secundário automaticamente. Replicação assíncrona significa que as transações sejam realizadas no banco de dados principal antes que eles são replicados para o banco de dados secundário. 

- **Vários bancos de dados secundários**: dois ou mais bancos de dados secundários aumentam a redundância e o nível de proteção para o banco de dados principal e o aplicativo. Se houver vários bancos de dados secundários, o aplicativo permanecerá protegido, mesmo se um banco de dados secundários Falha. Se há apenas um banco de dados secundário e ele falha, o aplicativo é exposto alto risco até um novo banco de dados secundário é criado.

- **Bancos de dados secundários legíveis**: um aplicativo pode acessar um banco de dados secundário para operações de somente leitura usando os objetos de segurança iguais ou diferentes usados para acessar o banco de dados principal. Os bancos de dados secundários operam no modo de isolamento de instantâneo para garantir a replicação das atualizações do primário (repetição de log) não é atrasada por consultas executadas no secundário.

>[AZURE.NOTE] A repetição do log está atrasada no banco de dados secundário se há atualizações de esquema que ele está recebendo do principal que exigem um bloqueio de esquema no banco de dados secundário.

- **Replicação geográfica ativa de bancos de dados do pool elástica**: replicação de localização geográfica ativa pode ser configurada para qualquer banco de dados em qualquer pool elástica banco de dados. O banco de dados secundário pode ser em outro pool de elástica banco de dados. Bancos de dados regulares, o secundário pode ser um banco de dados elástica pool e vice-versa desde que os níveis de serviço são os mesmos. 

- **Nível de desempenho configurável do banco de dados secundário**: um banco de dados secundário pode ser criado com o nível de desempenho inferior principal. Bancos de dados principais e secundários devem ter o mesmo nível de serviço. Esta opção não é recomendada para aplicativos com atividade de gravação do banco de dados alto porque a latência de replicação maior aumenta o risco de perda de dados substanciais após um failover. Além disso, após failover o desempenho do aplicativo é afetado até que o novo primário é atualizado para um nível superior do desempenho. O gráfico de porcentagem de IO log no portal Azure fornece uma boa maneira de estimar o nível de desempenho mínimo do secundário que é necessário para sustentar a carga de replicação. Por exemplo, se seu banco de dados principal P6 (1000 DTU) e seu log de porcentagem de IO é 50% secundário precisa ter pelo menos P4 (500 DTU). Você também pode recuperar os dados de IO de log usando modos de exibição de banco de dados de [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) .  Para obter mais informações sobre os níveis de desempenho de banco de dados SQL, consulte [desempenho e opções de banco de dados SQL](sql-database-service-tiers.md). 

- **Controlado por usuário failover e failback**: um banco de dados secundário pode explicitamente ser alternado para a função principal a qualquer momento pelo aplicativo ou pelo usuário. Durante uma interrupção real a "planejada" opção deve ser usada, que promove imediatamente um secundário seja o principal. Quando o principal falha recupera e está disponível novamente, o sistema automaticamente marca principal recuperado como um secundário e colocá-lo atualizado com o novo primário. Devido a natureza assíncrona de replicação, uma pequena quantidade de dados pode ser perdida durante failovers planejados se um primário falhar antes que ele replica as alterações mais recentes para o secundário. Quando um principal com vários secundários Falha ao longo, o sistema automaticamente reconfigura os relacionamentos de replicação e links secundários restantes para o principal recentemente promovido sem a necessidade de intervenção do usuário. Após a interrupção que a causou é reduzida, talvez seja desejável retornar o aplicativo para a região principal. Para fazer isso, o comando failover deve ser chamado com a opção "planejada". 

- **Guardar as credenciais e regras de firewall em sincronia**: Recomendamos usando [as regras de firewall do banco de dados](sql-database-firewall-configure.md) para replicado geográfica portanto essas regras de bancos de dados pode ser replicado com o banco de dados para garantir que todos os bancos de dados secundários têm as mesmas regras de firewall como o principal. Essa abordagem elimina a necessidade de clientes manualmente configurar e manter as regras de firewall nos servidores que hospedam tanto os bancos de dados principais e secundários. Da mesma forma, usando [contidos usuários banco de dados](sql-database-manage-logins.md) para acesso a dados garante bancos de dados principais e secundários sempre têm o mesmo usuário credenciais isso durante um failover, há sem interrupções devido a incompatibilidades com logon e senhas. Com a adição do [Azure Active Directory](../active-directory/active-directory-whatis.md), os clientes podem gerenciar o acesso do usuário aos bancos de dados principais e secundários e eliminando a necessidade de gerenciamento de credenciais em totalmente de bancos de dados.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados principal
Você pode atualizar ou fazer downgrade um banco de dados principal para um nível de desempenho diferentes (dentro do mesmo nível de serviço) sem desconectar bancos de dados secundários. Durante a atualização, recomendamos que você atualizar o banco de dados secundário primeiro e, em seguida, o principal. Quando fazer downgrade, inverter a ordem: downgrade principal primeiro e, em seguida, fazer downgrade secundário. 

Banco de dados secundário deve estar no mesmo nível de serviço como o principal, por migrar seu banco de dados principal para um nível de serviço diferente requer que você terminar o vínculo de replicação geográfica e renomear o banco de dados secundário ou simplesmente solte. Em seguida, migrar o principal para o novo nível de serviço e reconfigure replicação geográfica. Novo secundário será criado automaticamente com o mesmo nível de desempenho como o primário por padrão.

## <a name="preventing-the-loss-of-critical-data"></a>Impedir a perda de dados críticos
Devido a alta latência das redes de longa distância, cópia contínuo usa um mecanismo de replicação assíncrona. Replicação assíncrona torna inevitável perda de dados, se ocorrer uma falha. No entanto, alguns aplicativos podem exigir sem perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) imediatamente após a confirmação da transação. Chamando **sp_wait_for_database_copy_sync** bloqueia o segmento de chamada até a última transação confirmada foi duplicada no banco de dados secundário. O procedimento esperará até que todas as transações na fila tiveram sido reconhecidas pelo banco de dados secundário. **sp_wait_for_database_copy_sync** é delimitado para um link de cópia contínuo específica. Qualquer usuário com os direitos de conexão ao banco de dados principal pode chamar esse procedimento.

>[AZURE.NOTE] O atraso causado por uma chamada de procedimento **sp_wait_for_database_copy_sync** pode ser significativo. O atraso depende do tamanho do comprimento do log de transação no momento e essa chamada não retorna até que o log inteiro seja replicado. Evite chamar esse procedimento, a menos que absolutamente necessário.

## <a name="programmatically-managing-active-geo-replication"></a>Gerenciando programaticamente replicação de localização geográfica ativa

Conforme discutido anteriormente, a replicação geográfica ativos também pode ser gerenciada por programação usando o PowerShell do Azure e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

- **API do Gerenciador de recursos do Azure e segurança baseada em função**: replicação de localização geográfica ativa inclui um conjunto de [APIs do Gerenciador de recursos do Azure]( https://msdn.microsoft.com/library/azure/mt163571.aspx) para gerenciamento, incluindo [cmdlets do PowerShell baseado em Gerenciador de recursos do Azure](sql-database-geo-replication-powershell.md). Essas APIs requerem o uso de grupos de recursos e suportam a segurança baseada em função (RBAC). Para obter mais informações sobre como implementar acesso funções consulte [Controle de acesso de Azure Role-Based](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Muitos recursos novos do Active geográfica replicação só há suporte para usar o [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com base em [API de REST do SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) e [cmdlets do PowerShell de banco de dados do SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). A API REST (clássico)] (https://msdn.microsoft.com/library/azure/dn505719.aspx) e [cmdlets do Azure SQL Database (clássico)](https://msdn.microsoft.com/library/azure/dn546723.aspx) são suportados para fins de compatibilidade para usar as APIs baseado em Gerenciador de recursos do Azure são recomendados. 


### <a name="transact-sql"></a>Transact-SQL

|Comando|Descrição|
|-------|-----------|
|[ALTERAR o banco de dados (banco de dados do SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Usar o argumento de adicionar SECUNDÁRIO Diante servidor para criar um banco de dados secundário de um banco de dados existente e inicia a replicação de dados|
|[ALTERAR o banco de dados (banco de dados do SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Use FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS para alternar de um banco de dados secundário para ser primário para iniciar o failover
|[ALTERAR o banco de dados (banco de dados do SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Use remover SECUNDÁRIO Diante servidor para encerrar uma replicação de dados entre um banco de dados do SQL e o banco de dados secundário especificado.|
|[sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)|Retorna informações sobre todos os vínculos de replicação existentes para cada banco de dados no servidor lógico Azure SQL Database.|
|[sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtém a última vez de replicação, último tempo de latência de replicação e outras informações sobre o link de replicação para um determinado banco de dados do SQL.|
|[sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx)|Mostra o status de todas as operações de banco de dados, incluindo o status dos links de replicação.|
|[sp_wait_for_database_copy_sync (Azure SQL Database)](https://msdn.microsoft.com/library/dn467644.aspx)|faz com que o aplicativo aguardar até que todas as transações confirmadas são replicadas e reconhecidas pelo banco de dados secundário ativo.|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtém um ou mais bancos de dados.|
|[Novo AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados.|
|[Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Alterna a um banco de dados secundário para ser primário para iniciar o failover.|
|[Remover AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Encerra a replicação de dados entre um banco de dados do SQL e o banco de dados secundário especificado.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtém os vínculos de replicação de localização geográfica entre um banco de dados do SQL Azure e um grupo de recursos ou do SQL Server.|
||||

### <a name="rest-api"></a>API REST

|API|Descrição|
|---|-----------|
|[Criar ou atualizar banco de dados (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Cria ou atualiza ou restaura um primário ou um banco de dados secundário.|
|[Obter criar ou atualizar Status de banco de dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retorna o status durante uma operação de criação.|
|[Definir o banco de dados secundário principal (Failover planejado)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promova um banco de dados secundário em uma parceria de replicação geográfica se tornar o novo banco de dados principal.|
|[Definir o banco de dados secundário principal (Failover planejado)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Para forçar um failover para o banco de dados secundário e definir o secundário como principal.|
|[Obtenha Links de replicação](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtém todos os vínculos de replicação para um determinado banco de dados do SQL em uma parceria de replicação geográfica. Ele recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links.|
|[Obter Link de replicação](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtém um link de replicação específico para um determinado banco de dados do SQL em uma parceria de replicação geográfica. Ele recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links.|
||||



## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md).
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md).
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e o banco de dados, consulte [segurança de banco de dados SQL após a recuperação de dados](sql-database-geo-replication-security-config.md).
