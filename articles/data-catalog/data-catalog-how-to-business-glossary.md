<properties
    pageTitle="Como configurar o Glossário de negócios para regulamentadas marcação | Microsoft Azure"
    description="Artigo de instruções realçando o Glossário de negócios no catálogo de dados do Azure para definir e usar um vocabulário de negócios comuns a marca registrada ativos de dados."
    services="data-catalog"
    documentationCenter=""
    authors="steelanddata"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/21/2016"
    ms.author="maroche"/>

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Como configurar o Glossário de negócios para regulamentadas marcação

## <a name="introduction"></a>Introdução

Catálogo de dados do Azure oferece recursos de descoberta de fonte de dados, permitindo que os usuários descobrir facilmente e compreender as fontes de dados que precisam para executar análises e tomar decisões. Esses recursos de descoberta tornam o maior impacto quando os usuários podem encontrar e entender a maior variedade de fontes de dados disponíveis.

Um recurso de catálogo de dados que promove maior compreensão dos dados de ativos é marcação. Marcação permite que os usuários associar palavras-chave um ativo ou uma coluna, que torna mais fácil de descobrir o ativo via procurando, e permite que os usuários entendam mais facilmente o contexto e a finalidade do ativo.

No entanto, a marcação às vezes pode causar problemas de sua própria. Alguns exemplos de problemas que podem ser introduzidos marcando são:

1.  Usuários usando abreviações alguns ativos e texto expandido em outros durante a marcação. Essa inconsistência impede a descoberta de ativos, embora a intenção foi marcar os ativos com a mesma marca.
2.  Marcas que significam coisas diferentes em contextos diferentes. Por exemplo, uma marca chamada "Receita" em um conjunto de dados do cliente pode significam receita por cliente, mas a mesma marca em um conjunto de dados de venda trimestral poderia significa receita trimestral para a empresa.  

Para ajudar a resolver esses e outros desafios semelhantes, catálogo de dados inclui um glossário de negócios.

O Glossário de negócios do catálogo de dados permite que as organizações termos de negócios principais de documento e suas definições para criar um vocabulário de negócios comuns. Este controle permite consistência no uso de dados em toda a organização. Depois de termos são definidos no glossário comerciais, eles podem ser atribuídos a ativos de dados no catálogo, usando a mesma abordagem como marcação, permitindo _regulamentadas marcação_.

> [AZURE.NOTE] A funcionalidade descrita neste artigo estão disponíveis apenas no Standard Edition do Azure catálogo de dados. A edição livre não fornecem recursos para marcação controlada ou um glossário de negócios.

## <a name="glossary-availability-and-privileges"></a>Privilégios e disponibilidade de glossário

*Glossário do business está disponível no Standard Edition do Azure catálogo de dados. Livre Edition do catálogo de dados não inclui um glossário.*

Glossário do business pode ser acessado por meio da opção de "Glossário" no menu de navegação do portal de catálogo de dados.  

![Acessando o Glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Administradores de catálogo de dados e os membros da função glossário administradores podem criar, editar e excluir termos de Glossário no glossário business. Todos os usuários do catálogo de dados podem exibir as definições de termos e podem marcar ativos com termos do glossário.

![Adicionando um novo termo de glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Criação de termos do glossário

Os administradores de catálogo de dados e glossário pode criar novos termos glossário clicando em novo termo ' botão Criar termos do glossário com os seguintes campos:

* Uma definição de negócios para o termo
* Uma descrição que captura a finalidade ou regras de negócios para a ativo/coluna
* Uma lista de participantes que saber mais sobre o termo
* O termo pai, que define a hierarquia na qual o termo é organizado


## <a name="glossary-term-hierarchies"></a>Hierarquias de termos do glossário

Glossário de negócios do catálogo de dados fornece a capacidade para descrever o seu vocabulário de negócios como uma hierarquia de termos. Isso permite que as organizações criem uma classificação de termos que melhor representa taxonomia seus negócios.

O nome de um termo deve ser exclusivo em um determinado nível da hierarquia - nomes duplicados não são permitidos. Não há nenhum limite para o número de níveis em uma hierarquia, mas uma hierarquia geralmente é mais fácil compreensão quando há três níveis ou menos.

O uso de hierarquias no glossário business é opcional. Deixar o pai do termo campo em branco para termos de glossário criará uma lista plana (não hierárquicos) de termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcação ativos com termos do glossário

Depois de termos do glossário foram definidos dentro do catálogo, a experiência de ativos de marcação é otimizada para pesquisar o Glossário que o usuário digita sua marca. O portal de catálogo de dados exibe uma lista de correspondência termos do glossário do usuário à sua escolha. Se o usuário seleciona um termo de glossário na lista ela é adicionada ao ativo como uma marca (também conhecido como marca de Glossário). O usuário também pode optar por criar uma nova marca digitando um termo que não está no glossário (também conhecido como marca de usuário).

![Dados ativos marcado com marca de um usuário e duas marcas de glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Marcas de usuário são o único tipo de marca com suporte no livre Edition do catálogo de dados.

### <a name="hover-behavior-on-tags"></a>Comportamento de foco em marcas
No portal do catálogo de dados os dois tipos de marcas são visualmente distintos, com comportamentos de focalização diferentes. Quando o usuário passa o mouse sobre uma marca de usuário eles poderão ver o texto de marca e o usuário ou usuários que adicionou a marca. Quando o usuário passa o mouse sobre uma marca de glossário, eles também ver a definição do termo de glossário e um link para abrir o Glossário de negócios para exibir a definição completa do termo.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa de marcas
Marcas de glossário e marcas de usuário são pesquisáveis e podem ser aplicadas como filtros em uma pesquisa.

## <a name="summary"></a>Resumo
Glossário do business no catálogo de dados do Azure e a marcação controlada possibilita, permitir ativos de dados ser identificado, gerenciada e descoberta de maneira consistente. Glossário do business pode promover a aprendizagem do vocabulário empresas entre os usuários de uma organização e suporta metadados significativos para ser capturado, tornando descoberta de ativos e Compreendendo moleza.

## <a name="see-also"></a>Consulte também

- [Documentação da API REST glossário para operações de negócios](https://msdn.microsoft.com/library/mt708855.aspx)
