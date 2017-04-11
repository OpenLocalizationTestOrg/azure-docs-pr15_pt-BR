<properties 
    pageTitle="Classificando dados DocumentDB usando Order By | Microsoft Azure" 
    description="Saiba como usar ORDER BY em consultas de DocumentDB no SQL e LINQ e como especificar uma política de indexação para ORDER BY consultas." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>DocumentDB dados usando a ordem de classificação
Microsoft Azure DocumentDB suporta consultar documentos usando SQL sobre documentos JSON. Resultados da consulta podem ser solicitados usando a cláusula ORDER BY em instruções de consulta SQL.

Após ler este artigo, você poderá responder às seguintes perguntas: 

- Como consultar com Order By?
- Como configurar uma política de indexação para Order By?
- O que é o próximo passo?

[Exemplos](#samples) e uma [perguntas Frequentes](#faq) também são fornecidos.

Para obter uma referência completa sobre consultas SQL, consulte o [tutorial de consulta de DocumentDB](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Como consultar com Order By
Como em ANSI-SQL, você agora pode incluir opcional cláusula Order By em instruções SQL ao consultar DocumentDB. A cláusula pode incluir um argumento Crescente/Decrescente opcional para especificar a ordem na qual os resultados devem ser recuperados. 

### <a name="ordering-using-sql"></a>Ordenação usando SQL
Por exemplo, aqui está uma consulta para recuperar os livros de 10 primeiros em ordem decrescente de seus títulos. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Ordenação usando SQL com filtragem
Você pode solicitar usando qualquer propriedade aninhada dentro de documentos, como Books.ShippingDetails.Weight e você pode especificar filtros adicionais na cláusula WHERE em combinação com Order By, como neste exemplo:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordenação usando o provedor LINQ para .NET
Usando o SDK do .NET versão 1.2.0 e superior, você também pode usar a cláusula OrderBy() ou OrderByDescending() em consultas LINQ como neste exemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB suporta os pedidos com um único numérico, a cadeia de caracteres ou a propriedade booleana por consulta, com tipos de consulta adicionais em breve. Consulte [o que é o próximo passo](#Whats_coming_next) para obter mais detalhes.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurar uma política de indexação para Order By

Lembre-se de que DocumentDB suporta dois tipos de índices (Hash e intervalo), que podem ser definidos para caminhos/propriedades específicas, tipos de dados (cadeias de caracteres/números) e valores de precisão diferente (precisão máxima ou um valor de precisão fixa). Como DocumentDB usa Hash indexação como padrão, você deve criar um novo conjunto com uma diretiva de indexação personalizada com intervalo em números, strings ou ambas, para poder usar Order By. 

>[AZURE.NOTE] Índices de intervalo de cadeia de caracteres foram introduzidos em 7 de julho de 2015 com a API REST versão 2015-06-03. Para criar políticas para Order By contra cadeias de caracteres, você deve usar o SDK versão 1.2.0 do .NET SDK ou versão 1.1.0 do Python, Node ou Java SDK.
>
>Antes da API REST versão 2015-06-03, a política padrão de indexação conjunto foi Hash cadeias de caracteres e números. Isso é alterado para Hash cadeias de caracteres e o intervalo de números. 

Para obter mais detalhes, consulte [DocumentDB políticas de indexação](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indexação de Order By contra todas as propriedades
Veja como você pode criar um conjunto com "Todos os intervalo" indexação para Order By contra todos numéricos ou propriedades de cadeia de caracteres que aparecem em documentos JSON dentro dela. Aqui, estamos substituir o tipo de índice padrão para valores de cadeia de caracteres em intervalo e na precisão máxima (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Observe que Order By somente retornará os resultados dos tipos de dados (cadeia de caracteres e número) que estão indexados com um RangeIndex. Por exemplo, se você tiver o padrão de indexação política que só tem RangeIndex em números, Order By em relação a um caminho com valores de cadeia de caracteres não retornará nenhum documento.

### <a name="indexing-for-order-by-for-a-single-property"></a>Indexação de Order By para uma única propriedade
Veja aqui como você pode criar um conjunto com indexação para Order By contra apenas a propriedade título, que é uma cadeia de caracteres. Há dois caminhos, um para a propriedade de título ("/ título /?") com indexação de intervalo e outro para todas as outra propriedade com o esquema de indexação padrão, que é Hash cadeias de caracteres e intervalo de números.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Amostras
Dê uma olhada este [projeto de exemplos Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) que demonstra como usar Order By, incluindo indexação políticas criando e usando a ordem de paginação. Os exemplos são Abrir origem e recomendamos que você envie solicitações de recepção com contribuições que poderiam se beneficiar outros desenvolvedores DocumentDB. Consulte as [diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter orientações sobre como Contribuir.  

## <a name="faq"></a>Perguntas Freqüentes

**O que é o consumo de unidade solicitar (RU) esperado de Order By consultas?**

Como Order By utiliza o índice de DocumentDB de pesquisas, o número de unidades de solicitação consumida por Order By consultas será semelhante às consultas equivalentes sem Order By. Como qualquer outra operação em DocumentDB, o número de unidades de solicitação depende de tamanhos/formas de documentos, bem como a complexidade da consulta. 


**O que é o esperado indexação sobrecarga para Order By?**

A indexação sobrecarga de armazenamento será proporcional ao número de propriedades. O pior caso, a sobrecarga de índice será 100% dos dados. Não há nenhuma diferença de sobrecarga de produtividade (unidades solicitar) entre intervalo/Order By indexação e a indexação de Hash padrão.

**Como faço para consultar meus dados existentes em DocumentDB usando ordem?**

Para classificar os resultados da consulta usando Order By, você deve modificar a política de indexação da coleção para usar um tipo de índice de intervalo em relação a propriedade usado para classificar. Consulte [modificando a política de indexação](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quais são as limitações atuais de Order By?**

Order By pode ser especificado somente contra uma propriedade, qualquer numérico ou cadeia de caracteres quando ele é o intervalo indexada com a precisão máxima (-1).

Você não pode executar o seguinte:
 
- Order By com propriedades de cadeia de caracteres internos como id, _rid e Self (em breve).
- Order By com propriedades deriva o resultado de uma junção de dentro do documento (em breve).
- Classificado por várias propriedades (em breve).
- Order By com consultas em bancos de dados, conjuntos, usuários, as permissões ou anexos (em breve).
- Order By com propriedades computadas por exemplo, o resultado de uma expressão ou uma função UDF/incorporada-in.

Order By não há atualmente suporte para consultas de partição de cruz ao usar o Explorador de consulta no portal do Azure.

## <a name="troubleshooting"></a>Solução de problemas

Se você receber um erro que Order By não é suportado, verifique se que você está usando uma versão do [SDK](documentdb-sdk-dotnet.md) que suporta Order By. 

## <a name="next-steps"></a>Próximas etapas

Bifurcação o [projeto de exemplos Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) e comece a ordenação seus dados! 

## <a name="references"></a>Referências
* [Referência de consulta de DocumentDB](documentdb-sql-query.md)
* [Referência de política de indexação DocumentDB](documentdb-indexing-policies.md)
* [Referência SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Ordem de DocumentDB por amostras](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

