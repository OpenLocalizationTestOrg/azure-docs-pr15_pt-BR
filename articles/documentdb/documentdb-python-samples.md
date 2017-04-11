<properties 
    pageTitle="Exemplos de NoSQL Python para DocumentDB | Microsoft Azure" 
    description="Encontre NoSQL Python exemplos no github para tarefas comuns no DocumentDB, incluindo operações de CRUD para documentos JSON em bancos de dados NoSQL." 
    keywords="exemplos de Python"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>Exemplos de DocumentDB Python

> [AZURE.SELECTOR]
- [Exemplos de .NET](documentdb-dotnet-samples.md)
- [Exemplos de Node](documentdb-nodejs-samples.md)
- [Exemplos de Python](documentdb-python-samples.md)
- [Galeria de exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Exemplos de soluções que realizam operações CRUD e outras operações comuns no Azure DocumentDB recursos estão incluídos no repositório GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) . Este artigo fornece:

- Links para as tarefas em cada um dos arquivos de projeto de exemplo Python. 
- Links para a API relacionada referência conteúdo.

**Pré-requisitos**

1. Você precisará de uma conta do Azure para usar esses exemplos de Python:
    - Você pode [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): obtenha créditos que você pode usar para experimentar serviços Azure pagos e até mesmo depois que eles são usados até você pode manter a conta e uso livre Azure serviços, como sites. Seu cartão de crédito nunca será cobrado, a menos que você explicitamente alterar suas configurações e peça para ser cobrados.
   - Você pode [ativar os benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): assinatura o Visual Studio oferece a você créditos todo mês que você pode usar para serviços do Azure pagos.
2. Você também precisa o [SDK do Python](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Cada amostra é independente, ele configura próprio e limpeza sozinho. Assim, as amostras emitem várias chamadas para [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Sempre que isso é feito sua assinatura será cobrado para 1 hora de uso pelo nível de desempenho da coleção que está sendo criada. 

## <a name="database-examples"></a>Exemplos de banco de dados

O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) mostra como executar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Uma conta para um banco de dados da consulta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Ler um banco de dados por Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. IntegraçãoLeitura](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Bancos de dados de lista para uma conta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Excluir um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Exemplos de conjunto 

O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) mostra como executar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar um conjunto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ler uma lista de todos os conjuntos de um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obter um conjunto por Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obter o nível de desempenho de um conjunto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Alterar o nível de desempenho de um conjunto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Excluir um conjunto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
