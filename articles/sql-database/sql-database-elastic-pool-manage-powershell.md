<properties 
    pageTitle="Gerenciar um pool de banco de dados elástica (PowerShell) | Microsoft Azure" 
    description="Aprenda a usar o PowerShell para gerenciar um pool de elástica banco de dados."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Monitorar e gerenciar um pool de banco de dados elástica com PowerShell 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gerencie um [pool de banco de dados elástica](sql-database-elastic-pool.md) usando cmdlets do PowerShell. 

Para códigos de erro comuns, consulte [códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados](sql-database-develop-error-messages.md).

Valores para pools podem ser encontrados em [limites de armazenamento e eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Pré-requisitos

* Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
* Pools de banco de dados elástica só estão disponíveis com servidores V12 de banco de dados do SQL. Se você tiver um servidor de V11 de banco de dados do SQL, [usar o PowerShell para atualizar a V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma etapa.


## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados em um pool Elástico

Você pode mover um banco de dados em ou reduzir um pool com o [Conjunto AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Alterar as configurações de desempenho de um pool

Quando o desempenho é afetado, você pode alterar as configurações do pool para acomodar o crescimento. Use o cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Defina o parâmetro - Dtu para o eDTUs por pool. Consulte [limites de armazenamento e eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) para obter valores possíveis.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Obter o status das operações de pool

Criando um pool pode demorar. Para acompanhar o status das operações de pool, inclusive criação e atualizações, use o cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Obter o status de mover um banco de dados Elástico dentro e fora de um pool

Mover um banco de dados pode demorar. Acompanhe um status de mover usando o cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Obter dados de uso de recursos para um pool

Métricas que podem ser recuperadas como uma porcentagem do limite de pool de recursos:   


| Nome de métrica | Descrição |
| :-- | :-- |
| CPU\_por cento | Utilização de computação média em porcentagem do limite do pool. |
| físico\_dados\_ler\_por cento | Utilização de e/s média no porcentagem com base no limite do pool. |
| log\_escrever\_por cento | Média de gravação utilização de recursos em porcentagem do limite do pool. | 
| DTU\_consumo\_por cento | Utilização de eDTU médio em porcentagem do limite de eDTU para o pool | 
| armazenamento\_por cento | Utilização média do armazenamento na porcentagem do limite de armazenamento do pool. |  
| trabalhadores\_por cento | Máximos simultâneos trabalhadores (solicitações) em porcentagem com base no limite do pool. |  
| sessões\_por cento | Máximo de sessões simultâneas em porcentagem com base no limite do pool. | 
| eDTU_limit | Máximo do pool elástica DTU configuração atual para este pool elástica durante este intervalo. |
| armazenamento\_limite | Limite de armazenamento do atual máximo do pool elástica configuração para este pool elástica em megabytes durante este intervalo. |
| eDTU\_usado | Média eDTUs usada pelo pool de nesse intervalo. |
| armazenamento\_usado | Armazenamento médio usado pelo pool de nesse intervalo em bytes |

**Períodos de detalhamento/retenção métricas:**

* Dados serão retornados ao detalhamento de 5 minutos.  
* Retenção de dados é 35 dias.  

Este cmdlet e API limita o número de linhas que podem ser recuperadas em uma chamada de 1000 linhas (cerca de 3 dias de dados no detalhamento de 5 minutos). Mas este comando pode ser chamado várias vezes com intervalos de tempo inicial/final diferente para recuperar dados mais 

Para recuperar as métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Obter dados de uso de recursos para um banco de dados Elástico

Essas APIs são a mesma que as APIs (V12) atuais usadas para monitorar a utilização de um banco de dados autônomo, exceto para a diferença semântica seguinte recursos. 

Para este API, métricas recuperadas são expresso como uma porcentagem do por eDTUs máx (ou ponta equivalente para a métrica subjacente como CPU, IO etc) definido para esse pool. Por exemplo, 50% de utilização de qualquer uma das seguintes métricas indica que o consumo de recursos específicos em 50% do limite de ponta do banco de dados para o recurso no pool pai por. 

Para recuperar as métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Adicionar um alerta para um recurso de pool

Você pode adicionar regras de alerta para os recursos para enviar notificações por email ou cadeias de caracteres de alertas para [pontos de extremidade de URL](https://msdn.microsoft.com/library/mt718036.aspx) , quando o recurso atinge um limite de utilização que você configurar. Use o cmdlet Add-AzureRmMetricAlertRule. 

> [AZURE.IMPORTANT]Utilização de recursos monitoramento para Pools elástica tem um atraso de pelo menos 20 minutos. Configuração de alertas de menos de 30 minutos para Pools elástica não é suportado atualmente. Conjunto de todos os alertas para Pools elástica com um período (parâmetro chamado "-Tamanho_da_janela" na API do PowerShell) de menos de 30 minutos pode não ser disparada. Certifique-se de que todos os alertas que você definir para Pools elástica usam um período (Tam_janela) de 30 minutos ou mais.

Este exemplo adiciona um alerta para ser notificado quando consumo de eDTU de um pool fica acima determinado limite.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Adicionar alertas para todos os bancos de dados em um pool

Você pode adicionar regras de alerta para todos os banco de dados em um pool de Elástico para enviar notificações por email ou cadeias de alerta para [pontos de extremidade de URL](https://msdn.microsoft.com/library/mt718036.aspx) quando um recurso atinge um limite de utilização configurado pelo alerta. 

> [AZURE.IMPORTANT] Utilização de recursos monitoramento para Pools elástica tem um atraso de pelo menos 20 minutos. Configuração de alertas de menos de 30 minutos para Pools elástica não é suportado atualmente. Conjunto de todos os alertas para Pools elástica com um período (parâmetro chamado "-Tamanho_da_janela" na API do PowerShell) de menos de 30 minutos pode não ser disparada. Certifique-se de que todos os alertas que você definir para Pools elástica usam um período (Tam_janela) de 30 minutos ou mais.

Este exemplo adiciona um alerta para cada banco de dados em um pool para ser notificado quando consumo de DTU do banco de dados fica acima determinado limite.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Coletar e monitorar dados de uso de recursos em vários pools em uma assinatura

Quando você possui um grande número de bancos de dados em uma assinatura, é difícil de monitorar cada pool elástica separadamente. Em vez disso, os cmdlets do PowerShell de banco de dados SQL e consultas T-SQL podem ser combinadas para coletar dados de uso de recursos de vários pools e seus bancos de dados para monitoramento e análise de uso do recurso. Um [exemplo de implementação](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) de tal um conjunto de scripts do powershell pode ser encontrada no repositório GitHub SQL Server amostras juntamente com documentação sobre o que ela faz e como usá-lo.

Para usar este exemplo de implementação siga estas etapas listadas abaixo.


1. Baixe os [scripts e documentação](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifique os scripts para seu ambiente. Especifica um ou mais servidores que pools Elástico são hospedados.
3. Especifique um banco de dados de telemetria onde as métricas coletadas serão armazenados. 
4. Personalize o script para especificar a duração de execução dos scripts.

Em um alto nível, os scripts faça o seguinte:

*   Enumera todos os servidores em uma determinada assinatura Azure (ou uma lista especificada de servidores).
*   Executa um trabalho de plano de fundo para cada servidor. O trabalho é executado em um loop em intervalos regulares e coleta de dados de telemetria para todos os pools no servidor. Ele carrega os dados coletados no banco de dados de telemetria especificado.
*   Enumera uma lista de bancos de dados em cada pool para coletar os dados de uso do recurso de banco de dados. Ele carrega os dados coletados no banco de dados de telemetria.

As métricas coletadas do banco de dados de telemetria podem ser analisadas para monitorar a integridade de pools Elástico e os bancos de dados nele. O script também instala uma função com valor de tabela predefinida (TVF) do banco de dados de telemetria para ajudar a agregação as métricas de uma janela de tempo especificado. Por exemplo, resultados do TVF podem ser usados para mostrar "superior N Elástico pools com a utilização de eDTU máximo em uma janela de tempo determinado." Opcionalmente, use ferramentas analíticas como o Excel ou o Power BI para consultar e analisar os dados coletados.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Exemplo: recuperar métricas de consumo de recursos para um pool e seus bancos de dados

Este exemplo recupera as métricas de consumo para um determinado pool Elástico e todos os seus bancos de dados. Os dados coletados são formatados e gravados em um arquivo formatado. csv. O arquivo pode ser acessado com o Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latência de operações de pool elástica

- Alterar o eDTUs mínimo por banco de dados ou Máx eDTUs por banco de dados geralmente conclui em 5 minutos ou menos.
- Alterando a eDTUs por pool depende da quantidade total de espaço usado por todos os bancos de dados no pool. Média de alterações 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool é 200 GB, então a latência esperada para alterar o eDTU pool por pool é 3 horas ou menos.

## <a name="migrate-from-v11-to-v12-servers"></a>Migrar do V11 para os servidores de V12

Cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorar uma atualização para V12 de banco de dados do SQL Azure de V11 ou qualquer outra versão do pré-V12.

- [Atualização para V12 de banco de dados do SQL usando o PowerShell](sql-database-upgrade-server-powershell.md)

Para documentação de referência sobre esses cmdlets do PowerShell, consulte:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Início-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


O cmdlet parada significa Cancelar, pausar não. Não há nenhuma maneira de retomar uma atualização, além de iniciar novamente desde o começo. O cmdlet parada limpará e libera todos os recursos apropriados.

## <a name="next-steps"></a>Próximas etapas

- [Criar Elástico trabalhos](sql-database-elastic-jobs-overview.md) Trabalhos Elástico permitem executar scripts T-SQL em relação a qualquer número de bancos de dados no pool.
- Consulte [escala check-out com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para fora de escala, mover dados, consultar ou criar transações.
