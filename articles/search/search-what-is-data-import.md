<properties
    pageTitle="Para carregar dados na pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Saiba como carregar dados em um índice na pesquisa do Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Carregar dados para pesquisa do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTANTE](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Dados Carregue modelos na pesquisa Azure
Há duas maneiras de preencher o índice de pesquisa do Azure com seus dados. A primeira opção é insistem manualmente seus dados no índice usando a pesquisa do Azure [API REST](search-import-data-rest-api.md) ou [.NET SDK](search-import-data-dotnet.md). A segunda opção é [apontar uma fonte de dados com suporte](search-indexer-overview.md) para o índice de pesquisa do Azure e permitir que o Azure pesquisa retirar automaticamente os dados para o serviço de pesquisa.

Este guia abordará apenas instruções sobre como usar o modelo de envio de carregamento de dados (que é suportado apenas nos [API REST](search-import-data-rest-api.md) e [.NET SDK](search-import-data-dotnet.md)), mas você ainda pode aprender mais sobre o modelo de pull abaixo.

### <a name="push-data-to-an-index"></a>Enviar dados para um índice

Essa abordagem se refere a programaticamente envio de dados para a pesquisa do Azure para disponibilizá-lo para pesquisa. Para aplicativos tendo requisitos de latência muito baixa (por exemplo, se você precisa pesquisar operações para estar sincronizado com bancos de dados de inventário dinâmico), o modelo de envio for sua única opção.

Você pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [.NET SDK](search-import-data-dotnet.md) aos dados de envio para um índice. Atualmente, não há nenhum suporte de ferramenta para colocar dados através do portal.

Essa abordagem é mais flexível que o modelo de pull porque você pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, o limite que vier primeiro). O modelo de envio também permite que você carregar documentos para pesquisa do Azure, independentemente de onde os dados estão.

### <a name="pull-data-into-an-index"></a>Colocar dados em um índice

O modelo de pull rastreia uma fonte de dados com suporte e carrega os dados automaticamente para você o índice de pesquisa do Azure para você. Controlando alterações e exclui a documentos existentes, além de reconhecer novos documentos, indexadores remover a necessidade de gerenciar ativamente os dados no seu índice.

Em pesquisa do Azure, esse recurso implementado por meio de *indexadores*, atualmente disponíveis para [o armazenamento de Blob (visualização)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [banco de dados do SQL Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

A funcionalidade de indexador é exposta no [Portal do Azure](search-import-data-portal.md) , bem como a [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).
