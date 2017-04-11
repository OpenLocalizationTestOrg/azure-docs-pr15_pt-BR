<properties
    pageTitle="Como modelar tipos de dados complexos na pesquisa do Azure | Pesquisa do Microsoft Azure"
    description="Aninhada ou estruturas hierárquicas de dados podem ser modeladas em um índice de pesquisa do Azure usando o conjunto de linhas bidimensional e tipo de dados de coleções."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como os tipos de dados complexos de modelo na pesquisa do Azure

Conjuntos de dados externos usados para preencher um índice de pesquisa do Azure às vezes incluem subestruturas hierárquicas ou aninhadas que não divida organizado em um conjunto de linhas tabular. Exemplos de tais estruturas podem incluir vários locais e números de telefone para um único cliente, várias cores e tamanhos para um único SKU, vários autores de um único livro, etc. Em termos de modelagem, você poderá ver essas estruturas conhecidas como *tipos de dados complexos*, *compostos de tipos de dados*, *tipos de dados compostos*ou *agregar os tipos de dados*, para mencionar alguns.

Tipos de dados complexos não são suportados nativamente na pesquisa do Azure, mas alternativa comprovada inclui um processo de duas etapas de nivelamento a estrutura e usando um tipo de dados da **coleção** para reconstituir a estrutura interior. Seguir a técnica descrita neste artigo permite que o conteúdo a ser pesquisado, Facetado, filtrados e classificados.

## <a name="example-of-a-complex-data-structure"></a>Exemplo de uma estrutura de dados complexos

Normalmente, os dados em questão residem como um conjunto de documentos JSON ou XML ou itens em uma loja de NoSQL como DocumentDB. Estruturalmente, o desafio deriva tendo vários itens filho que precisam ser pesquisados e filtrados.  Como ponto de partida para ilustrar a solução alternativa, faça o seguinte documento JSON que um conjunto de contatos como um exemplo de lista:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Embora os campos nomeado 'id', 'nome' e 'empresa' podem facilmente ser mapeados individualmente como campos em um índice de pesquisa do Azure, o campo 'locais' contém uma matriz de locais, tendo os dois um conjunto de IDs de local, bem como descrições de local. Considerando que pesquisa Azure não tem um tipo de dados que ofereça suporte a isso, precisamos de maneira diferente para modelar isso na pesquisa do Azure. 

> [AZURE.NOTE] Essa técnica também é descrita por Kirk nascimento em uma postagem de blog [Indexação DocumentDB com a pesquisa do Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que mostra uma técnica chamada "nivelamento os dados", por meio da qual você teria um campo chamado `locationsID` e `locationsDescription` que sejam [conjuntos](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Nivelar a matriz em campos individuais

Para criar um índice de pesquisa do Azure que comporte este conjunto de dados, criar campos individuais para o estruturados aninhado: `locationsID` e `locationsDescription` com um tipo de dados de [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres). Nesses campos que você deseja indexar os valores '1' e '2' a `locationsID` campo para John Smith e os valores '3' e '4' para as `locationsID` campo para Jen Campbell.  

Seus dados dentro de pesquisa do Azure teria esta aparência: 

![dados de exemplo, 2 linhas](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Adicionar um campo de conjunto na definição de índice

No esquema de índice, as definições de campo podem ser semelhantes neste exemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validar comportamentos de pesquisa e, opcionalmente, estender o índice

Considerando que você criou o índice e carregou os dados, agora você pode testar a solução para verificar a execução da consulta de pesquisa contra o dataset. Cada campo **conjunto** deve ser **pesquisado**, **filtráveis** e **facetable**. Você deve ser capaz de executar consultas como:

* Localize todas as pessoas que trabalham na 'Adventureworks sede'.
* Obter uma contagem do número de pessoas que trabalham na 'Home Office'.  
* Das pessoas que trabalham na 'Home Office', mostre quais outros escritórios funcionam juntamente com uma contagem das pessoas em cada local.  

Onde essa técnica cai separadas por é quando você precisa realizar uma pesquisa que combina a identificação de local, bem como a descrição da localização. Por exemplo:

* Localizar todas as pessoas em que eles tenham um Office Home e tem uma identificação de local de 4.  

Se você cancelar o conteúdo original parecida com isso:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

No entanto, agora que podemos ter separados os dados em campos separados, não temos nenhuma maneira de saber se o Office Home para Jen Campbell se relaciona a `locationsID 3` ou `locationsID 4`.  

Para lidar com esse caso, defina outro campo no índice que combina todos os dados em uma única coleção.  Para nosso exemplo, podemos chamará este campo `locationsCombined` e podemos separar o conteúdo com um `||` Embora você pode escolher qualquer separador que você acha que seria um conjunto exclusivo de caracteres para o seu conteúdo. Por exemplo: 

![dados de exemplo, 2 linhas com separador](./media/search-howto-complex-data-types/sample-data-2.png)

Usando essa `locationsCombined` campo, podemos pode agora acomodar ainda mais consultas, como:

* Mostre uma contagem de pessoas que trabalham no 'Home escritório' com a Id de localização de '4'.  
* Procurar por pessoas que trabalham em um 'Home Office' com Id '4' local. 

## <a name="limitations"></a>Limitações

Essa técnica é útil para um número de cenários, mas ela não é aplicável em todos os casos.  Por exemplo:

1. Se você não tem um conjunto estático de campos no seu tipo de dados complexos e não havia nenhuma maneira de mapear todos os tipos de possíveis para um único campo. 
2. Atualizar os objetos aninhados requer algum trabalho extra para determinar exatamente o que precisa ser atualizado no índice de pesquisa do Azure

## <a name="sample-code"></a>Código de exemplo

Você pode ver um exemplo sobre como indexar um conjunto de dados JSON complexo pesquisa do Azure e realizar um número de consultas sobre este dataset a este [GitHub repo](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Próxima etapa

[Voto para suporte nativo aos tipos de dados complexos](https://feedback.azure.com/forums/263029-azure-search) em UserVoice de pesquisa do Azure página e forneça qualquer entrada adicional que você gostaria que nós a serem consideradas sobre implementação de recurso. Você pode também comunique-se para mim diretamente no Twitter em @liamca.


 