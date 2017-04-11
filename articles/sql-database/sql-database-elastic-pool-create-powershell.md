<properties
    pageTitle="Criar um novo pool de banco de dados elástica com PowerShell | Microsoft Azure"
    description="Aprenda a usar o PowerShell para fora de escala Azure SQL Database recursos criando um pool de banco de dados elástica scalable para gerenciar vários bancos de dados."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Criar um novo pool de banco de dados elástica com PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [pool de banco de dados elástica](sql-database-elastic-pool.md) usando cmdlets do PowerShell. 

Para códigos de erro comuns, consulte [códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools Elástico são disponibilidade geral (GA) em todas as regiões Azure exceto Centro Norte dos EUA e Índia Oeste onde ele está no modo de visualização.  GA pools elástica nessas regiões será fornecido assim que possível. Além disso, pools Elástico não suportamos bancos de dados usando [na memória OLTP ou análise na memória](sql-database-in-memory.md).


Você precisa estar executando o Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Criar um novo pool

O cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cria um novo pool. Os valores para eDTU por pool, mínimo e máximo Dtus são restringidos pelo valor de nível de serviço (basic, padrão ou premium). Consulte [limites de armazenamento e eDTU para pools Elástico e Elástico bancos de dados](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Criar um novo banco de dados Elástico em um pool

Use o cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName** para o pool de destino. Para mover um banco de dados existente em um pool, consulte [Mover um banco de dados em um pool Elástico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Criar um pool e preenchê-lo com vários novos bancos de dados 

Criação de um grande número de bancos de dados em um pool pode demorar quando concluído usando o portal ou os cmdlets do PowerShell que criar apenas um banco de dados por vez. Para automatizar a criação em um novo pool, consulte [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exemplo: criar um pool usando o PowerShell 

Este script cria um novo grupo de recursos do Azure e um novo servidor. Quando solicitado, forneça um nome de usuário de administrador e uma senha para o novo servidor (não suas credenciais Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Próximas etapas

- [Gerenciar seu pool](sql-database-elastic-pool-manage-powershell.md)
- [Criar Elástico trabalhos](sql-database-elastic-jobs-overview.md) Trabalhos Elástico permitem executar scripts T-SQL em relação a qualquer número de bancos de dados no pool.
- [Escala com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para fora de escala.

