<properties
    pageTitle="Como fontes de dados de documentos | Microsoft Azure"
    description="Artigo de instruções realce como documentar ativos de dados no catálogo de dados do Azure."
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

# <a name="document-data-sources"></a>Fontes de dados de documento

## <a name="introduction"></a>Introdução

**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Em outras palavras, **Catálogo de dados do Azure** é tudo sobre pessoas ajudando descobrir, *entender*e usar fontes de dados e ajudar as empresas para obter mais valor de seus dados existentes.

Quando uma fonte de dados é registrada com o **Catálogo de dados do Azure**, seus metadados é copiado e indexado pelo serviço, mas a história não termina lá. **Catálogo de dados do Azure** também permite aos usuários fornecer seu próprios documentação completa pode descrever o uso e cenários comuns para a fonte de dados.

[Como anotar fontes de dados](data-catalog-how-to-annotate.md), você vai aprender que especialistas que saibam a fonte de dados podem anotar com marcas e uma descrição. O portal de **Catálogo de dados do Azure** inclui um editor de rich text para que os usuários podem totalmente contêineres e documentos ativos de dados. O editor inclui formatação de parágrafo, como títulos, formatação de texto, listas com marcadores, listas numeradas e tabelas.

Marcas e descrições são ótimas para anotações simples. Entretanto, para ajudar os consumidores de dados para entender melhor o uso de uma fonte de dados e cenários de negócios para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil a uma fonte de dados de documentos. Selecionar um ativo de dados ou o contêiner e escolha **documentação**.

![](media\data-catalog-documentation\data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentando ativos de dados

A vantagem de **Catálogo de dados do Azure** documentação permite que você usar o catálogo de dados como um repositório de conteúdo para criar uma narrativa completa de seus ativos de dados. Você pode explorar conteúdo detalhado que descreve contêineres e tabelas. Se você já tiver conteúdo em outro repositório de conteúdo, como SharePoint ou um compartilhamento de arquivo, você pode adicionar os links de documentação de ativos para fazer referência a esse conteúdo existente. Este recurso faz com que os documentos existentes sejam mais detectáveis.

> [AZURE.NOTE] Documentação não está incluída no índice de pesquisa.

![](media\data-catalog-documentation\data-catalog-documentation2.png)

O nível de documentação pode variar de descrevendo as características e o valor de um contêiner de ativos de dados para uma descrição detalhada do esquema de tabela em um contêiner. O nível de documentação fornecida deve ser controlado por suas necessidades de negócios. Mas em geral, aqui estão alguns prós e contras da documentando ativos de dados:

-   Apenas um contêiner de documentos: todo o conteúdo está em um só lugar, mas talvez não têm detalhes necessários para os usuários a tomar uma decisão informada.
-   Apenas as tabelas de documentos: conteúdo é específico ao objeto, mas seus usuários tiverem vários lugares para documentos.
-   Contêineres e tabelas do documento: uma abordagem mais abrangente, mas pode apresentar mais de manutenção dos documentos.

## <a name="summary"></a>Resumo

Documentar fontes de dados com o **Catálogo de dados do Azure** pode criar uma narrativa sobre seus ativos de dados no tantos detalhes conforme necessário.  Usando links, você pode vincular ao conteúdo armazenado em um repositório de conteúdo existente, que reúne seus ativos de dados e documentos de existentes. Depois que seus usuários descobrir ativos de dados apropriado, ele podem ter um conjunto completo de documentação.
