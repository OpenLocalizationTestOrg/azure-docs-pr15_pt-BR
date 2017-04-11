<properties 
    pageTitle="Configurar replicação de localização geográfica ativa para o banco de dados do SQL Azure usando o PowerShell | Microsoft Azure" 
    description="Configurar replicação de localização geográfica ativa para o banco de dados do SQL Azure usando o PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurar replicação geográfica para o banco de dados do SQL Azure com o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra como configurar replicação de localização geográfica ativa para banco de dados do SQL com o PowerShell.

Para iniciar o failover usando o PowerShell, consulte [Iniciar um failover planejado ou para o banco de dados do SQL Azure com o PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Localização geográfica-replicação ativa (legíveis secundários) agora está disponível para todos os bancos de dados em todos os níveis de serviço. Em abril de 2017 o tipo de secundário não legível será desativado e bancos de dados não legível existentes serão automaticamente atualizados para secundários legíveis.



Para configurar a replicação de localização geográfica ativa usando o PowerShell, você precisa fazer o seguinte:

- Uma assinatura do Azure. 
- Um banco de dados do SQL Azure - é o principal banco de dados que você deseja duplicar.
- Azure PowerShell 1.0 ou posterior. Você pode baixar e instalar os módulos do PowerShell do Azure pelo seguinte [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configure suas credenciais e selecione sua assinatura

Primeiro você deve estabelece acesso à sua conta do Azure então inicie PowerShell e execute o cmdlet a seguir. Na tela de login, digite o mesmo email e senha que você usa para entrar no portal do Azure.


    Login-AzureRmAccount

Depois de entrar com êxito, você verá algumas informações em tela que inclui a identificação você entrou com e as assinaturas Azure a que tem acesso.


### <a name="select-your-azure-subscription"></a>Selecione a sua assinatura do Azure

Para selecionar a assinatura que você precisa de sua assinatura do ID. Você pode copiar a Id de inscrição das informações exibidas da etapa anterior, ou se você tiver várias assinaturas e precisa de mais detalhes, você pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações de assinatura desejada do conjunto de resultados. O cmdlet a seguir usa a Id de inscrição para definir a assinatura atual:

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar com êxito **Selecione AzureRmSubscription** você voltará para o PowerShell prompt.


## <a name="add-secondary-database"></a>Adicionar banco de dados secundário


As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  
  
Para habilitar um secundário, você deve ser o proprietário da assinatura ou coproprietário. 

Você pode usar o cmdlet **New-AzureRmSqlDatabaseSecondary** para adicionar um banco de dados secundário em um servidor de parceiro para um banco de dados local no servidor ao qual você está conectado (o banco de dados principal). 

Esse cmdlet substitui **AzureSqlDatabaseCopy iniciar** com o parâmetro **– IsContinuous** .  Ela produzirá um objeto de **AzureRmSqlDatabaseSecondary** que pode ser usado por outros cmdlets para identificar claramente um link de replicação específico. Esse cmdlet retornará quando o banco de dados secundário é criado e totalmente propagado. Dependendo do tamanho do banco de dados ele pode levar de minutos a horas.

O banco de dados replicado no servidor secundário terá o mesmo nome do banco de dados no servidor primário e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser lido ou não legível e pode ser um banco de dados único ou elástica. Para obter mais informações, consulte [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Níveis de serviço](sql-database-service-tiers.md).
O secundário for criado e propagado, dados começará replicação de banco de dados principal para o novo banco de dados secundário. As etapas a seguir descrevem como realizar essa tarefa usando o PowerShell para criar secundários não legível e legíveis, com um único banco de dados ou um banco de dados Elástico.

Se o banco de dados de parceiro já existe (por exemplo - como encerrando uma relação de replicação geográfica anterior) o comando falhará.



### <a name="add-a-non-readable-secondary-single-database"></a>Adicionar um secundário não legível (banco de dados único)

O comando a seguir cria um secundário não legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Adicionar legível secundário (banco de dados único)

O comando a seguir cria um secundário legível do banco de dados "mydb" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Adicionar um secundário não legível (Elástico banco de dados)

O comando a seguir cria um secundário não legível do banco de dados "mydb" no pool de banco de dados elástica denominado "ElasticPool1" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Adicionar um secundário legível (Elástico banco de dados)

O comando a seguir cria um secundário legível do banco de dados "mydb" no pool de banco de dados elástica denominado "ElasticPool1" do servidor "srv2" no grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Remover o banco de dados secundário

Use o cmdlet **AzureRmSqlDatabaseSecondary remover** permanentemente encerrar a parceria de replicação entre um banco de dados secundário e seu principal. Após o encerramento de relação, o banco de dados secundário se torna um banco de dados de leitura e gravação. Se a conectividade com o banco de dados secundário foi desfeita o comando é bem sucedida, mas o secundário se tornarão leitura depois conectividade for restaurada. Para obter mais informações, consulte [Remover AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Níveis de serviço](sql-database-service-tiers.md).

Esse cmdlet substitui parada AzureSqlDatabaseCopy para replicação. 

Essa remoção é equivalente de um encerramento forçado que remove o link de replicação e deixa o secundário anterior como um banco de dados autônomo que não é totalmente replicado antes de término. Todos os dados de link serão limpo no primeiro primário e secundário, primeiro se ou quando eles estiverem disponíveis. Esse cmdlet retornará quando o link de replicação é removido. 


Para remover secundária, os usuários devem ter acesso de gravação aos bancos de dados principais e secundários de acordo com a RBAC. Consulte controle de acesso baseado em função para obter detalhes.

Este procedimento remove o link de replicação de banco de dados denominado "mydb" servidor "srv2" do grupo de recursos "rg2". 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Integridade e a configuração de replicação geográfica do monitor

Tarefas de monitoramento incluem monitoramento da configuração de replicação geográfica e integridade de replicação de dados.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) pode ser usado para recuperar as informações sobre os vínculos de replicação encaminhar visíveis no modo de exibição de catálogo sys.geo_replication_links.

O comando a seguir recupera o status do link replicação entre o banco de dados "mydb" primário e secundário no servidor "srv2" do grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Active geográfica replicação, consulte - [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md)
- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)

