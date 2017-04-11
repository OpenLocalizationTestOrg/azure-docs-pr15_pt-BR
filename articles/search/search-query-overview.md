<properties
    pageTitle="Índice de pesquisa Azure da consulta | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Criar uma consulta de pesquisa na pesquisa Azure e use os parâmetros de pesquisa para filtrar e classificar resultados de pesquisa."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>Índice de pesquisa do Azure da consulta
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTANTE](search-query-rest-api.md)

Ao enviar solicitações de pesquisa para pesquisar do Azure, há um número de parâmetros que pode ser especificado junto com as palavras reais digitada na caixa de pesquisa do seu aplicativo. Esses parâmetros de consulta permite que você atingir algum controle mais profunda da experiência de pesquisa de texto completo.

Abaixo está uma lista que explica brevemente usos comuns os parâmetros de consulta de pesquisa do Azure. Cobertura completa de parâmetros de consulta e seu comportamento, consulte as páginas detalhadas para a [API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) e o [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Tipos de consultas

Pesquisa Azure oferece várias opções para criar consultas poderosas. Os dois tipos principais de consulta que você usará são `search` e `filter`. A `search` consulta procura um ou mais termos em todos os campos _pesquisável_ no seu índice e funciona da maneira que você poderia esperar um mecanismo de pesquisa como o Bing ou o Google para trabalhar. A `filter` consulta avalia uma expressão booliana sobre todas _filtráveis_ campos em um índice. Ao contrário dos `search` consultas, `filter` consultas correspondem ao conteúdo exato de um campo, o que significa que eles diferenciam maiusculas de minúsculas para campos de cadeia de caracteres.

Você pode usar filtros e pesquisas separadamente ou juntas. Se você usá-los juntos, o filtro é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Filtros, portanto, podem ser uma técnica útil para melhorar o desempenho de consulta desde que eles reduzem o conjunto de documentos que precisa de consulta de pesquisa para processar.

A sintaxe para expressões de filtro é um subconjunto da [linguagem de filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para consultas de pesquisa, você pode usar a [sintaxe simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou a [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) que são discutidos abaixo.

### <a name="simple-query-syntax"></a>Sintaxe de consulta simples
A [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx) é a linguagem de consulta padrão usada na pesquisa do Azure. A sintaxe de consulta simples é compatível com um número de operadores comuns de pesquisa, incluindo e, ou não, frase, sufixo e operadores de precedência.

### <a name="lucene-query-syntax"></a>Sintaxe de consulta Lucene
A [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) permite que você use a linguagem de consulta amplamente adotada e expressivo desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Usar essa sintaxe de consulta permite que você facilmente atingir os seguintes recursos: [consultas de escopo do campo](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [pesquisa confusa](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [pesquisa de proximidade](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [aumentando a termo](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [pesquisa de expressões regulares](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [pesquisa curinga](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [conceitos básicos da sintaxe](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)e [consultas usando operadores booleanos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Ordenação resultados
Ao receber os resultados de uma consulta de pesquisa, você pode solicitar que o Azure pesquisa serve os resultados classificados por valores em um campo específico. Por padrão, o Azure pesquisa ordens os resultados da pesquisa com base na classificação de pontuação de pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se quiser que a pesquisa do Azure para retornar os resultados classificados por um valor diferente a pontuação de pesquisa, você pode usar o `orderby` parâmetro de pesquisa. Você pode especificar o valor da `orderby` parâmetro incluir nomes de campo e chamadas para o [ `geo.distance()` função](https://msdn.microsoft.com/library/azure/dn798921.aspx) para valores de geoespaciais. Cada expressão pode ser seguido por `asc` para indicar que os resultados são solicitados em ordem crescente, e `desc` para indicar que os resultados são solicitados em ordem decrescente. A classificação padrão ordem crescente.

## <a name="paging"></a>Paginação
Pesquisa Azure facilita a implementar paginação dos resultados da pesquisa. Usando o `top` e `skip` parâmetros, você pode emitir tranquilamente solicitações de pesquisa que permitem que você receber o conjunto total de resultados de pesquisa em subconjuntos gerenciáveis, ordenados que permitem facilmente práticas de interface do usuário de pesquisa boa. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto de total de resultados da pesquisa.

Você pode aprender mais sobre os resultados de pesquisa no artigo [como página Pesquisar resultados de pesquisa do Azure](search-pagination-page-layout.md)de paginação.


## <a name="hit-highlighting"></a>Realce de ocorrências
Em pesquisa do Azure, enfatizar a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é feita fácil usando o `highlight`, `highlightPreTag`, e `highlightPostTag` parâmetros. Você pode especificar quais campos _pesquisáveis_ deve seu texto correspondente enfatizado bem como especificando as marcas de cadeia de caracteres exata acrescentar até o início e final do texto correspondente que a pesquisa Azure retorna.
