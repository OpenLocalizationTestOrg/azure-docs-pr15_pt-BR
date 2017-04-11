<properties 
    pageTitle="Copiar um banco de dados do SQL Azure usando o PowerShell | Microsoft Azure" 
    description="Criar cópia de um banco de dados do SQL Azure usando o PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiar um banco de dados do SQL Azure usando o PowerShell


> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Este artigo mostra como copiar um banco de dados do SQL com o PowerShell no mesmo servidor, para um servidor diferente, ou copie um banco de dados para um [pool elástica banco de dados](sql-database-elastic-pool.md). A operação de cópia do banco de dados usa o cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) . 


Para concluir este artigo, você precisa do seguinte:

- Um banco de dados do SQL Azure (um banco de dados para copiar). Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).
- A versão mais recente do PowerShell do Azure. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


Só há suporte para muitos recursos novos do banco de dados SQL quando você estiver usando o [modelo de implantação do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md), portanto exemplos usam os [cmdlets do PowerShell de banco de dados do SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para o Gerenciador de recursos. O modelo clássico de implantação existente [Azure SQL Database (clássico) cmdlets](https://msdn.microsoft.com/library/azure/dn546723.aspx) são suportados para compatibilidade com versões anteriores, mas recomendamos que você usar os cmdlets do Gerenciador de recursos.


>[AZURE.NOTE] Dependendo do tamanho do seu banco de dados, a operação de cópia pode levar algum tempo para ser concluída.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados do SQL no mesmo servidor

Para criar a cópia no mesmo servidor, omita a `-CopyServerName` parâmetro (ou defini-lo no mesmo servidor).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Para criar a cópia em um servidor diferente, inclua o `-CopyServerName` parâmetro e defini-lo para um servidor diferente. O servidor de *cópia* já deve existir. Se ele estiver em um grupo de recurso diferente, você também deve incluir o `-CopyResourceGroupName` parâmetro.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copie um banco de dados SQL para um pool de banco de dados elástica

Para criar uma cópia de um banco de dados do SQL em um pool, defina o `-ElasticPoolName` parâmetro a um pool existente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Resolver logon

Para resolver logon após a conclusão da operação de cópia, consulte [resolver logon](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Script do PowerShell de exemplo

O seguinte script supõe todos os grupos de recursos, servidores, e o pool já existe (substitua os valores de variáveis com recursos existentes). Tudo deve existir, exceto para a cópia do banco de dados.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Próximas etapas

- Consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md) para uma visão geral de cópia de um banco de dados do SQL Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando o portal do Azure](sql-database-copy-portal.md) para copiar um banco de dados usando o portal do Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando T-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados com Transact-SQL.
- Veja [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md) para saber mais sobre gerenciamento de usuários e logon ao copiar um banco de dados para um servidor lógico diferente.


## <a name="additional-resources"></a>Recursos adicionais

- [Novo AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gerenciar logon](sql-database-manage-logins.md)
- [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Referência de cmdlets do PowerShell de banco de dados do SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx)
