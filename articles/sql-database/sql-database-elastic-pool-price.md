<properties
    pageTitle="Desempenho e preço de pool elástica de banco de dados SQL"
    description="Informações sobre preços específicos para pools de elástica banco de dados."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Informações sobre preços e cobrança de pool de banco de dados elástica

Pools de banco de dados elástica são cobrados pelas seguintes características:

- Um pool de Elástico é cobrado após sua criação, mesmo quando não houver nenhum banco de dados no pool.
- Um pool de Elástico é cobrado por hora. Esta é a mesma frequência de medição para níveis de desempenho de bancos de dados único.
- Se um pool Elástico é redimensionado para um novo valor de eDTUs, em seguida, o pool não é cobrado acordo com a nova quantidade de eDTUS até que seja concluída a operação de redimensionamento. Isso segue o mesmo padrão alterando o nível de desempenho de bancos de dados independente.


- O preço de um pool de Elástico baseia-se no número de eDTUs do pool. O preço de um pool de Elástico é independente do número e utilização dos bancos de dados elástica dentro dela.
- Preço é calculado por (número de pool eDTUs) x (preço unitário por eDTU).

O preço unitário eDTU um pool Elástico é maior que o preço unitário DTU um banco de dados autônomo no mesmo nível de serviço. Para obter detalhes, consulte [preços de banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 


Para entender os níveis de serviço e eDTUs, consulte [desempenho e opções de banco de dados SQL](sql-database-service-tiers.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar um pool de banco de dados elástica](sql-database-elastic-pool-create-portal.md)
- [Monitorar, gerenciar e dimensionar um pool de banco de dados elástica](sql-database-elastic-pool-manage-portal.md)
- [Opções de banco de dados SQL e desempenho: entender o que está disponível em cada nível de serviço](sql-database-service-tiers.md)
- [Script do PowerShell para identificar bancos de dados adequados para um pool de banco de dados elástica](sql-database-elastic-pool-database-assessment-powershell.md)
