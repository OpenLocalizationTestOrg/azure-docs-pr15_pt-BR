<properties
    pageTitle="Como fontes de dados de perfil de dados"
    description="Artigo de instruções realce como incluir os perfis de dados de nível de coluna e tabela ao registrar fontes de dados no catálogo de dados do Azure e como usar os perfis de dados para entender a fontes de dados."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Fontes de dados de perfil de dados

## <a name="introduction"></a>Introdução

**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Em outras palavras, **Catálogo de dados do Azure** trata ajudar as pessoas a descobrir, entender e usar fontes de dados e ajudar as empresas para obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada com o **Catálogo de dados do Azure**, seus metadados é copiado e indexado pelo serviço, mas a história não termina lá.

O recurso de **Criação de perfil de dados** do **Catálogo de dados do Azure** examina os dados de fontes de dados com suporte em seu catálogo e coleta de estatísticas e informações sobre esses dados. É fácil incluir um perfil de seus ativos de dados. Ao registrar um ativo de dados, escolha o **Perfil de dados incluem** na ferramenta de registro de fonte de dados.

## <a name="what-is-data-profiling"></a>O que é a criação de perfil de dados

Criação de perfil de dados examina os dados na fonte de dados sejam registrado e coleta de estatísticas e informações sobre esses dados. Durante a descoberta de fonte de dados, essas estatísticas podem ajudá-lo a determinar a adequação dos dados para resolver o problema de negócios.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes fontes de dados oferecem suporte a criação de perfil de dados:

- Modos de exibição e tabelas do SQL Server (incluindo DB do SQL Azure e depósito de dados do SQL Azure)
- Oracle tabelas e exibições
- Teradata tabelas e exibições
- Tabelas de seção

Incluindo perfis de dados ao registrar ativos de dados ajuda os usuários a responder perguntas sobre fontes de dados, incluindo:

-   Ele pode ser usado para resolver o problema de negócios?
-   Os dados estão em conformidade para padrões específicos ou padrões?
-   Quais são alguns dos problemas da fonte de dados?
-   Quais são os desafios possíveis de integrar esses dados meu aplicativo?

> [AZURE.NOTE] Você também pode adicionar documentação a um ativo para descrever como dados poderiam ser integrados em um aplicativo. Veja [como fontes de dados do documento](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registrar uma fonte de dados

É fácil incluir um perfil da sua fonte de dados. Quando você registra uma fonte de dados, no painel de **objetos para ser registrado** a ferramenta de registro de fonte de dados, escolha **Incluir perfil de dados**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Para saber mais sobre como registrar a fontes de dados, consulte [como registrar a fontes de dados](data-catalog-how-to-register.md) e [começar a usar o catálogo de dados do Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrando ativos de dados que incluem perfis de dados
Para descobrir ativos de dados que incluem um perfil de dados, você pode incluir `has:tableDataProfiles` ou `has:columnsDataProfiles` como um dos seus termos de pesquisa.

> [AZURE.NOTE] Selecionar **Incluir dados perfil** na ferramenta de registro de fonte de dados inclui a tabela e informações de perfil de nível de coluna. Entretanto, a API de catálogo de dados permite ativos de dados sejam registrados com apenas um conjunto de informações de perfil incluídos.

## <a name="viewing-data-profile-information"></a>Exibindo informações de perfil de dados

Depois de encontrar uma fonte de dados adequada com um perfil, você pode exibir os detalhes do perfil de dados. Para exibir o perfil de dados, selecione um ativo de dados e escolha o **Perfil de dados** na janela de portal do catálogo de dados.

![](media\data-catalog-data-profile\data-catalog-view.png)

Um perfil de dados no **Catálogo de dados do Azure** mostra tabela e informações de perfil de coluna incluindo:

### <a name="object-data-profile"></a>Perfil de dados do objeto

-   Número de linhas
-   Tamanho da tabela
-   Quando o objeto foi atualizada pela última vez

### <a name="column-data-profile"></a>Perfil de dados de coluna

- Tipo de dados de coluna
- Número de valores distintos
- Número de linhas com valores nulos
- Mínimo, máximo, média e o desvio padrão para valores de coluna

## <a name="summary"></a>Resumo
Perfil de dados fornecem estatísticas e informações sobre os ativos de dados registrados para ajudá-lo a determinar a adequação dos dados para solucionar problemas de negócios. Juntamente com as anotações e documentando fontes de dados, os perfis de dados podem fornecer aos usuários uma compreensão mais profunda dos seus dados.


## <a name="see-also"></a>Consulte também
-   [Como registrar a fontes de dados](data-catalog-how-to-register.md)
-   [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md)
