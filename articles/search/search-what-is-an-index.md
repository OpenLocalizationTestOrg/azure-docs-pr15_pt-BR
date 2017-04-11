<properties
    pageTitle="Criar um índice de pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="O que é um índice na pesquisa do Azure e como ele é usado?"
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

# <a name="create-an-azure-search-index"></a>Criar um índice de pesquisa do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTANTE](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>O que é um índice?

Um *índice* é um armazenamento persistente de *documentos* e outras construções usadas por um serviço de pesquisa do Azure. Um documento é uma unidade única de dados pesquisáveis no seu índice. Por exemplo, um fornecedor de comércio pode ter um documento para cada item que eles vendem, uma organização de notícias pode ter um documento para cada artigo, etc. Mapeando esses conceitos para familiarizados equivalentes de banco de dados: um *índice* é conceitualmente semelhante a uma *tabela*, e *documentos* são equivalentes ao *linhas* em uma tabela.

Quando você adicionar/carregar documentos e enviar consultas de pesquisa para pesquisar do Azure, você enviar suas solicitações em um índice específico no seu serviço de pesquisa.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipos de campo e atributos em um índice de pesquisa do Azure

Conforme você define seu esquema, especifique o nome, tipo e atributos de cada campo no seu índice. O tipo de campo classifica os dados armazenados no campo. Atributos são definidos em campos individuais para especificar como o campo é usado. As tabelas a seguir enumerar os tipos e atributos que você pode especificar.


### <a name="field-types"></a>Tipos de campo
|Tipo|Descrição|
|------------|-----------|
|*Edm.String*|Texto que pode ser indexado opcionalmente para pesquisa de texto completo (a quebra truncamento, etc).|
|*Collection(EDM.String)*|Uma lista de cadeias de caracteres que pode ser indexado opcionalmente para pesquisa de texto completo. Não há nenhum limite superior teórico no número de itens em uma coleção, mas o limite superior de 16 MB no tamanho da carga se aplica aos conjuntos.|
|*Edm.Boolean*|Contém valores verdadeiro/falso.|
|*Edm.Int32*|valores de número inteiro de 32 bits.|
|*Edm.Int64*|valores de número inteiro de 64 bits.|
|*Edm.Double*|Dados numéricos de dupla precisão.|
|*Edm.DateTimeOffset*|Valores de tempo são representados no formato de OData V4 de data (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Um ponto que representa uma localização geográfica no globo.|

Você pode encontrar informações mais detalhadas sobre [tipos de dados no MSDN com suporte da pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Atributos de campo
|Atributo|Descrição|
|------------|-----------|
|*Chave*|Uma cadeia de caracteres que fornece a identificação exclusiva de cada documento, usado para a aparência do documento para cima. Cada índice deve ter uma chave. Somente um campo pode ser a tecla e seu tipo deve ser definido como Edm.String.|
|*Recuperável*|Especifica se um campo pode ser retornado em um resultado de pesquisa.|
|*Filtráveis*|Permite que o campo a ser usado em consultas de filtro.|
|*Classificável*|Permite que uma consulta para classificar os resultados de pesquisa usando esse campo.|
|*Facetable*|Permite que um campo a ser usado em uma estrutura de [navegação com faceta](search-faceted-navigation.md) para filtragem automática direcionado do usuário. Normalmente, campos contendo valores repetitivos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estejam em uma única marca ou a categoria de serviço) funcionam melhor como aspectos.|
|*Pesquisado*|Marca o campo como texto completo pesquisável.|

Você pode encontrar informações mais detalhadas sobre da pesquisa do Azure [atributos de índice no MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Orientação para definir um esquema de índice

Ao projetar seu índice, reserve um tempo na fase de planejamento de pensar em cada decisão. É importante que você tenha suas necessidades de negócios e experiência de usuário pesquisa em mente ao criar o índice como cada campo deve ser atribuído os [atributos apropriados](https://msdn.microsoft.com/library/azure/dn798941.aspx). Alterar um índice depois de ser implantado envolve a recriação e recarregar os dados.


Se alterar os requisitos de armazenamento de dados ao longo do tempo, você pode aumentar ou diminuir capacidade adicionando ou removendo partições. Para obter detalhes, consulte [Gerenciar o serviço de pesquisa no Azure](search-manage.md) ou [Limites de serviço](search-limits-quotas-capacity.md).
