<properties 
    pageTitle="Alterar o nível de desempenho e nível de serviço de um banco de dados do SQL Azure usando o PowerShell | Microsoft Azure" 
    description="Alterar o nível de serviço e nível de desempenho de um banco de dados do SQL Azure mostra como dimensionar seu banco de dados SQL para cima ou para baixo com o PowerShell. Alterando o nível de preço de um banco de dados do SQL Azure com o PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL com o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Níveis de serviço e níveis de desempenho descrevem os recursos e os recursos disponíveis para o seu banco de dados SQL e podem ser atualizados conforme as necessidades de seu aplicativo mude. Para obter detalhes, consulte [Níveis de serviço](sql-database-service-tiers.md).

Observe que alterar o nível de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e alterna conexões para a réplica. Nenhum dado é perdido durante esse processo, mas durante o momento breve quando podemos alterna para a réplica, conexões ao banco de dados estão desativadas, portanto algumas transações em trânsito podem ser revertidas. Esta janela varia, mas é, em média, em 4 segundos e em mais de 99% de casos é menos de 30 segundos. Raramente, especialmente se houver grandes números de transações em trânsito nas conexões de momento serão desabilitados, esta janela pode ser maior.  

A duração de todo o processo de escala-up depende a camada serviço e tamanho do banco de dados antes e depois da alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou em um nível de serviço padrão, deve ser concluída dentro de 6 horas. Para um banco de dados do mesmo tamanho que está alterando os níveis de desempenho em nível de serviço Premium, ele deverá ser concluída dentro 3 horas.


- Para fazer um downgrade um banco de dados, o banco de dados deve ser menor que o tamanho máximo permitido de nível de serviço de destino. 
- Ao atualizar um banco de dados com [Replicação geográfica](sql-database-geo-replication-portal.md) habilitada, primeiro atualize seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados principal.
- Quando fazer downgrade de um nível de serviço Premium, você deve primeiro encerrar todas as relações de replicação geográfica. Você pode seguir as etapas descritas no tópico [recuperar uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre o principal e os bancos de dados secundários ativos.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade você pode perder a capacidade de restaurar para um ponto no tempo, ou ter um período de retenção de backup inferior. Para obter mais informações, consulte [Backup de banco de dados do SQL Azure e restauração](sql-database-business-continuity.md).
- As novas propriedades do banco de dados não são aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo, você precisa do seguinte:**

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Conta gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Um banco de dados do SQL Azure. Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).
- PowerShell Azure.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Alterar o nível de desempenho e nível de serviço de seu banco de dados do SQL

Executar a [Set-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet e configurar o **-RequestedServiceObjectiveName** para o nível de desempenho da camada preços desejado; por exemplo *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Amostra de script do PowerShell para alterar o nível de desempenho e nível de serviço do banco de dados SQL

Substituir ```{variables}``` com seus valores (não inclua as chaves).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Próximas etapas

- [Dimensionar in e check-out](sql-database-elastic-scale-get-started.md)
- [Conectar-se e consultar um banco de dados do SQL com SSMS](sql-database-connect-query-ssms.md)
- [Exportar um banco de dados do SQL Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlets de banco de dados do SQL azure] (http://msdn.microsoft.com/library/azure/mt574084https :/ / msdn.microsoft.com/biblioteca/azure/mt619433(v=azure.300\).aspx.aspx)