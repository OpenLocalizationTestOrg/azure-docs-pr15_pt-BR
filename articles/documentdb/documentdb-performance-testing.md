<properties 
    pageTitle="DocumentDB escala e testes de desempenho | Microsoft Azure" 
    description="Saiba como realizar escala e testes de desempenho com DocumentDB do Azure"
    keywords="teste de desempenho"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Desempenho e escala de teste com DocumentDB do Azure
Desempenho e testes de escala é uma etapa importante no desenvolvimento de aplicativos. Para muitos aplicativos, a camada de banco de dados tem um impacto significativo sobre o desempenho e escalabilidade gerais e, portanto, é um componente crítico de teste de desempenho. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) foi desenvolvido para dimensionamento Elástico e desempenho previsível e, portanto, uma excelente opção para aplicativos que precisam de um nível de banco de dados de alto desempenho. 

Este artigo é uma referência para desenvolvedores implementando conjuntos de teste de desempenho para as cargas de trabalho de DocumentDB ou avaliar DocumentDB para cenários de aplicativo de alto desempenho. Ele se concentra principalmente em testes de desempenho isolado do banco de dados, mas também inclui as práticas recomendadas para aplicativos de produção.

Após ler este artigo, você poderá responder às seguintes perguntas:   

- Onde posso encontrar um aplicativo de cliente do .NET de amostra testes de desempenho do Azure DocumentDB? 
- Como obter a níveis de alta produtividade com Azure DocumentDB de meu aplicativo cliente?

Para começar com o código, faça o download do projeto de [Exemplo de teste de desempenho de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] O objetivo deste aplicativo é demonstrar práticas recomendadas para extrair o melhor desempenho do DocumentDB com um pequeno número de computadores cliente. Isso não foi feito para demonstrar a capacidade de pico do serviço, que pode chegar limitlessly.

Se você estiver procurando opções de configuração do lado do cliente melhorar o desempenho de DocumentDB, consulte [dicas de desempenho de DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Executar o aplicativo de teste de desempenho
A maneira mais rápida para começar é compilar e executar a amostra de .NET abaixo, conforme descrito nas etapas abaixo. Você também pode revisar o código-fonte e implementar configurações semelhantes aos seus aplicativos de cliente.

**Etapa 1:** Baixe o projeto de [Amostra de teste de desempenho de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)ou bifurcação repositório Github.

**Etapa 2:** Modificar as configurações para EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) na App.

> [AZURE.NOTE] Antes de coleções com alta taxa de transferência de provisionamento, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/documentdb/) para estimar os custos por conjunto. Armazenamento de faturas de DocumentDB e produtividade independentemente em uma base por hora, para que você possa salvar custos excluindo ou reduzir a produtividade dos seus conjuntos de DocumentDB após o teste.

**Etapa 3:** Compilar e executar o aplicativo de console da linha de comando. Você deve ver o resultado semelhante ao seguinte:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Etapa 4 (se necessário):** A taxa de transferência relatada (RU/s) da ferramenta deve ser igual ou maior do que a taxa de transferência provisionada da coleção. Caso contrário, aumentando a DegreeOfParallelism em pequenos incrementos pode ajudá-lo a atingir o limite. Se a taxa de transferência de seu aplicativo cliente plateaus, iniciar várias instâncias do aplicativo nas máquinas iguais ou diferentes ajudará você atingir o limite provisionado entre as instâncias diferentes. Se você precisar de ajuda com esta etapa, escreva um email para askdocdb@microsoft.com ou preencher uma permissão de suporte.

Quando você tiver o aplicativo em execução, você pode tentar diferentes [políticas de indexação](documentdb-indexing-policies.md) e [níveis de consistência](documentdb-consistency-levels.md) para entender o impacto na produtividade e latência. Você também pode revisar o código-fonte e implementar configurações semelhantes aos seus próprios pacotes de teste ou aplicativos de produção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, analisamos como você pode executar testes com DocumentDB usando um aplicativo de console do .NET de dimensionamento e desempenho. Consulte os links abaixo para obter informações adicionais sobre como trabalhar com DocumentDB.

* [Exemplo de teste de desempenho de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar o desempenho de DocumentDB](documentdb-performance-tips.md)
* [Servidor partição no DocumentDB](documentdb-partition-data.md)
* [Conjuntos de DocumentDB e níveis de desempenho](documentdb-performance-levels.md)
* [Documentação DocumentDB .NET SDK no MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemplos de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB em dicas de desempenho](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
