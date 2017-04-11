<properties
    pageTitle="Replicação de banco de dados global de DocumentDB | Microsoft Azure"
    description="Saiba como gerenciar a duplicação global de sua conta de DocumentDB via portal do Azure."
    services="documentdb"
    keywords="banco de dados global, replicação"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Como realizar replicação de banco de dados global de DocumentDB usando o portal do Azure

Saiba como usar o portal do Azure para duplicar dados em várias regiões para disponibilidade global de dados no Azure DocumentDB.

Para obter informações sobre o banco de dados como global replicação funciona no DocumentDB, consulte [dados de distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md). Para obter informações sobre como executar replicação de banco de dados global programaticamente, consulte [desenvolvimento com contas de DocumentDB de várias regiões](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribuição global de bancos de dados de DocumentDB está disponível em geral e automaticamente habilitado para todas as contas DocumentDB recém-criado. Estamos trabalhando para habilitar a distribuição global em todas as contas existentes, mas enquanto isso, se quiser que a distribuição global habilitada em sua conta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos será habilite-lo para você agora.

## <a id="addregion"></a>Adicionar regiões de banco de dados global

DocumentDB está disponível na maioria das [regiões Azure] [azureregions]. Depois de selecionar o nível de consistência padrão para sua conta de banco de dados, você pode associar uma ou mais regiões (dependendo de sua escolha necessidades de distribuição globais e nível de consistência padrão).

1. No [portal do Azure](https://portal.azure.com/), em Jumpbar, clique em **Contas de DocumentDB**.
2. Na lâmina **DocumentDB conta** , selecione a conta de banco de dados para modificar.
3. Na lâmina conta, clique em **duplicar dados globalmente** no menu.
4. Na lâmina **replicar dados globalmente** , selecione as regiões para adicionar ou remover e clique em **Salvar**. Há um custo à adição de regiões, consulte o artigo de [dados de distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md) para obter mais informações ou a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/) .

    ![Clique nas regiões no mapa para adicionar ou remover][1]

### <a name="selecting-global-database-regions"></a>Selecionando regiões de banco de dados global

Ao configurar regiões de duas ou mais, é recomendável que regiões são selecionadas com base nos pares de região descritos a [desastre e continuidade de negócios recuperação (BCDR): Azure par regiões]  [ bcdr] artigo.

Especificamente, ao configurar para várias regiões, certifique-se selecionar o mesmo número de regiões (+ 1 for par/ímpar) de cada uma das colunas pares de região. Por exemplo, se você quiser implantar em quatro regiões dos EUA, selecione duas regiões de US da coluna à esquerda e dois da direita. Portanto, o seguinte seria um conjunto apropriado: Oeste EUA, Leste EUA, Centro Norte dos EUA e Centro Sul dos EUA.

Este guia é importante para acompanhar quando apenas duas regiões são configurados para cenários de recuperação de desastres. Para mais de duas regiões, seguindo este orientação, é recomendável, mas não críticas, desde que algumas das regiões selecionados adicional esse emparelhamento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Próximas etapas

Saiba como gerenciar a consistência da sua conta globalmente replicada lendo [níveis de consistência em DocumentDB](documentdb-consistency-levels.md).

Para obter informações sobre o banco de dados como global replicação funciona no DocumentDB, consulte [dados de distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md). Para obter informações sobre programaticamente duplicar dados em várias regiões, consulte [desenvolvimento com contas de DocumentDB de várias regiões](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
