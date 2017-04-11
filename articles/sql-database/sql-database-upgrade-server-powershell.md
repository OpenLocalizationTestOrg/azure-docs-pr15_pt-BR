<properties
    pageTitle="Atualização para V12 de banco de dados do SQL Azure usando o PowerShell | Microsoft Azure"
    description="Explica como atualizar para V12 de banco de dados do SQL Azure incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor de V11 migrando seus bancos de dados diretamente em um pool de elástica banco de dados usando o PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Atualização para V12 de banco de dados do SQL Azure usando o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de banco de dados do SQL é a versão mais recente para que a atualização para V12 de banco de dados do SQL é recomendado.
V12 de banco de dados SQL tem várias [vantagens em relação a versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho aprimorado premium e novos níveis de desempenho.
- [Pools elástica banco de dados](sql-database-elastic-pool.md).

Este artigo fornece instruções para atualizar os servidores de V11 de banco de dados do SQL existentes e bancos de dados para V12 de banco de dados do SQL.

Durante o processo de atualização para V12, você atualiza bancos de dados da Web e de negócios para um novo nível de serviço para que instruções para atualizar bancos de dados da Web e Business estão incluídas.

Além disso, migrar para um [pool de banco de dados elástica](sql-database-elastic-pool.md) pode ser mais econômico que a atualização para níveis de desempenho individuais (níveis de preços) para bancos de dados único. Pools também simplificam o gerenciamento de banco de dados porque você só precisa gerenciar as configurações de desempenho para o pool em vez de separadamente Gerenciando os níveis de desempenho de bancos de dados individuais. Se você tiver bancos de dados em vários servidores, considere a possibilidade de movê-los para o mesmo servidor e aproveitando colocá-los em um pool.

Você pode seguir as etapas neste artigo facilmente migrar bancos de dados de servidores V11 diretamente para pools de elástica banco de dados.

Observe que seus bancos de dados permanecerá online e continuar a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário soltar as conexões ao banco de dados pode acontecer por uma duração muito pequena que normalmente é em torno de 90 segundos, mas pode ser tanto quanto 5 minutos. Se seu aplicativo tiver [falhas temporárias tratamento de encerramentos de conexão](sql-database-connectivity-issues.md) é suficiente para proteger contra queda de conexão no final da atualização.

Atualização para o SQL V12 de banco de dados não pode ser desfeita. Após uma atualização, o servidor não pode ser revertido para V11.

Após a atualização para V12, [recomendações de nível de serviço](sql-database-service-tier-advisor.md) e [recomendações de pool elástica](sql-database-elastic-pool-create-portal.md) não imediatamente estará disponíveis até que o serviço tem tempo para avaliar as cargas de trabalho no novo servidor. Histórico de recomendação de servidor de V11 não se aplica aos servidores V12 para que ele não é mantido.  

## <a name="prepare-to-upgrade"></a>Preparar a atualização

- **Atualizar todos os bancos de dados da Web e Business**: usar o portal ou usar o [PowerShell para atualizar os bancos de dados e servidor](sql-database-upgrade-server-powershell.md).
- **Revisão e Suspender replicação geográfica**: se o seu banco de dados do SQL Azure está configurado para replicação de localização geográfica você deve documentar sua configuração atual e [Parar replicação geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Após a conclusão da atualização reconfigure seu banco de dados para replicação de localização geográfica.
- **Abrir essas portas se você tiver clientes em uma máquina virtual do Azure**: se o programa cliente se conecta ao V12 de banco de dados do SQL enquanto seu cliente é executado em uma máquina virtual Azure (máquina virtual), você deve abrir intervalos de porta 11999 11000 e 14000-14999 sobre a máquina virtual. Para obter detalhes, consulte [portas para V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Pré-requisitos

Para atualizar um servidor para V12 com o PowerShell, você precisa ter o PowerShell mais recente do Azure instalado e em execução. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configure suas credenciais e selecione sua assinatura

Para executar cmdlets do PowerShell em relação a sua assinatura do Azure, você deve primeiro estabelecer o acesso à sua conta do Azure. Execute o seguinte e você verá uma tela de entrada para inserir suas credenciais. Use o mesmo email e senha que você usa para entrar no portal do Azure.

    Add-AzureRmAccount

Depois de entrar com êxito, você verá algumas informações em tela que inclui a identificação você entrou com e as assinaturas Azure a que tem acesso.

Para selecionar a assinatura que você deseja trabalhar com você precisa de sua assinatura Id (**-SubscriptionId**) ou assinatura nome (**-Nome de inscrição**). Você pode copiá-lo da etapa anterior, ou se você tiver várias assinaturas, você pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações de assinatura desejada do conjunto de resultados.

Execute o seguinte cmdlet com suas informações de assinatura para definir sua assinatura atual:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Os seguintes comandos serão executados em relação a assinatura que você acabou de selecionar acima.

## <a name="get-recommendations"></a>Obter recomendações

Para obter a recomendação para a atualização de servidor, execute o seguinte cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Para obter mais informações, consulte [criar um pool de banco de dados elástica](sql-database-elastic-pool-create-portal.md) e [recomendações de nível de preços do Azure SQL Database](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Iniciar a atualização

Para iniciar a atualização do servidor que execute o seguinte cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando você executar este processo de atualização de comando começará. Você pode personalizar a saída da recomendação e fornecer a recomendação editada para esse cmdlet.


## <a name="upgrade-a-server"></a>Atualizar um servidor


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mapeamento de atualização personalizada

Se as recomendações não forem apropriadas para seu servidor e caso comercial, você pode escolher como seus bancos de dados são atualizados e mapeá-los a único ou Elástico bancos de dados.

Parâmetros ElasticPoolCollection e DatabaseCollection são opcionais:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorar bancos de dados após a atualização para V12 de banco de dados SQL


Após a atualização, é recomendável monitorar o banco de dados ativamente para garantir que aplicativos estão sendo executados no desempenho desejado e otimizam o uso da conforme necessário.

Além disso, para bancos de dados individuais, você pode monitorar o banco de dados elástica grupos [usando o portal](sql-database-elastic-pool-manage-portal.md) de monitoramento ou com o [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Dados de consumo de recursos:** Basic, Standard e Premium bancos de dados de consumo de recursos estão disponíveis através do [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) departamento de trânsito do banco de dados do usuário. Essa DMV oferece perto informações de consumo de recurso em tempo real no detalhamento de segunda 15 para hora anterior da operação. O consumo de porcentagem DTU um intervalo é calculado como o consumo de porcentagem máxima das dimensões de CPU, IO e log. Aqui está uma consulta para calcular o consumo de porcentagem DTU médio ao longo de última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações adicionais de monitoramento:

- [Orientações sobre desempenho de banco de dados do SQL azure para bancos de dados único](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações de preço e desempenho para um pool de elástica banco de dados](sql-database-elastic-pool-guidance.md).
- [Monitoramento de banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)



**Alertas:** Configure 'Alertas' no portal do Azure para notificá-lo quando o consumo de DTU para um banco de dados atualizado abordagens certos alto nível. Alertas de banco de dados podem ser configuradas no portal do Azure de várias métricas de desempenho como DTU, CPU, IO e Log. Navegue até o banco de dados e selecione **regras de alerta** na lâmina **configurações** .

Por exemplo, você pode configurar um alerta de email em "DTU porcentagem", se o valor médio de porcentagem DTU excede 75% sobre os últimos 5 minutos. Consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) para saber mais sobre como configurar as notificações de alerta.



## <a name="next-steps"></a>Próximas etapas

- [Criar um pool de banco de dados elástica](sql-database-elastic-pool-create-portal.md) e adicione alguns ou todos os bancos de dados para o pool.
- [Alterar o nível de desempenho e nível de serviço do seu banco de dados](sql-database-scale-up.md).



## <a name="related-information"></a>Informações relacionadas

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Início-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
