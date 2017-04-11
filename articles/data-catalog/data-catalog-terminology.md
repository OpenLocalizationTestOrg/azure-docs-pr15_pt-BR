<properties
   pageTitle="Terminologia de catálogo de dados Azure | Microsoft Azure"
   description="Este artigo fornece uma introdução a conceitos e termos usados na documentação do catálogo de dados do Azure."
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

# <a name="azure-data-catalog-terminology"></a>Azure terminologia do catálogo de dados

## <a name="catalog"></a>Catálogo

O catálogo de dados do Azure é um repositório de metadados baseado em nuvem na qual os dados ativos de dados e fontes podem ser registrados. O catálogo serve como um local de armazenamento central para metadados estruturais extraídos de fontes de dados e metadados descritivos adicionadas pelos usuários.

## <a name="data-source"></a>Fonte de dados

Uma fonte de dados é um sistema ou recipiente que gerencia ativos de dados. Alguns exemplos incluem bancos de dados do SQL Server, bancos de dados Oracle, bancos de dados do SQL Server Analysis Services (multidimensionais ou tabulares) e servidores do SQL Server Reporting Services.

## <a name="data-asset"></a>Dados ativos

Ativos de dados são objetos contidos em fontes de dados que podem ser registrados com o catálogo. Alguns exemplos incluem tabelas do SQL Server e modos de exibição, Oracle tabelas e exibições, SQL Server Analysis Services medidas, dimensões e KPIs e relatórios do SQL Server Reporting Services.

## <a name="data-asset-location"></a>Local de ativos de dados

O catálogo armazena o local de uma fonte de dados ou dados ativos, que podem ser usado para se conectar à fonte de usando um aplicativo cliente. O formato e os detalhes do local variam com base no tipo de fonte de dados. Por exemplo, uma tabela do SQL Server pode ser identificada por seu nome de quatro parte – o nome do servidor, o nome do banco de dados, o nome de esquema, o nome do objeto – enquanto um relatório do SQL Server Reporting Services pode ser identificado por sua URL.

## <a name="structural-metadata"></a>Metadados estruturais

Os metadados estruturais são os metadados extraídos de uma fonte de dados que descreve a estrutura de um ativo de dados. Isso inclui o local de ativos, seu nome do objeto e tipo e características adicionais do tipo específico. Por exemplo, os metadados estruturais para tabelas e modos de exibição incluem os nomes e tipos de dados para as colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou a intenção de um ativo de dados. Normalmente metadados descritivos for adicionado pelos usuários de catálogo usando o portal de catálogo de dados do Azure, mas também podem ser extraído da fonte de dados durante o registro. Por exemplo, a ferramenta de registro do catálogo de dados do Azure irá extrair descrições da propriedade Description no SQL Server Analysis Services e SQL Server Reporting Services e do [ms_description propriedade estendida](https://technet.microsoft.com/library/ms190243.aspx) em bancos de dados do SQL Server, se essas propriedades foram preenchidas com valores.

## <a name="request-access"></a>Solicitar acesso

Metadados descritivos de um ativo de dados podem incluir informações sobre como solicitar acesso à fonte de dados ou dados ativos. Essas informações são apresentadas com o local de ativos de dados e podem incluir uma ou mais das seguintes opções:

- O endereço de email do usuário ou da equipe responsável para conceder acesso à fonte de dados.
- A URL do processo documentada que os usuários devem ser seguidas para obter acesso à fonte de dados.
- A URL de uma ferramenta de gerenciamento identidades e acesso (como o do Gerenciador de identidade da Microsoft) que pode ser usada para obter acesso à fonte de dados.
- Uma entrada de texto livre que descreve como os usuários podem obter acesso à fonte de dados.

## <a name="preview"></a>Visualizar

Uma visualização no catálogo de dados do Azure é um instantâneo de até 20 registros que podem ser extraídos da fonte de dados durante o registro e armazenados no catálogo com os metadados de ativos de dados. A visualização pode ajudar os usuários que descubra um ativo de dados entendam melhor sua função e a finalidade. Em outras palavras, ver dados de exemplo pode ser mais valioso do que vê apenas os nomes de coluna e os tipos de dados.
Visualizações só há suporte para tabelas e exibições e deverá ser selecionadas explicitamente pelo usuário durante o registro.

## <a name="data-profile"></a>Perfil de dados

Um perfil de dados no catálogo de dados do Azure é um instantâneo de nível de coluna e de tabela metadados sobre um ativo de dados registrados que pode ser extraído da fonte de dados durante o registro e armazenado no catálogo com os metadados de ativos de dados. O perfil de dados pode ajudar os usuários que descubra um ativo de dados entendam melhor sua função e a finalidade. Semelhante ao visualizações, os perfis de dados devem ser explicitamente selecionados pelo usuário durante o registro.

> [AZURE.NOTE] Extrair um perfil de dados pode ser uma operação cara para grandes tabelas e exibições e pode aumentar significativamente o tempo necessário para registrar uma fonte de dados.

## <a name="user-perspective"></a>Perspectiva do usuário

No catálogo de dados do Azure, qualquer usuário pode fornecer metadados descritivos para um ativo de dados registrados. Cada usuário tem uma perspectiva distinta sobre os dados e seu uso. Por exemplo, o administrador responsável por um servidor pode fornecer os detalhes do seu contrato de nível de serviço (SLA) ou janelas de backup; o administrador de dados pode fornecer links para documentação para os negócios processa o suporta dados; e um analista poderá fornecer uma descrição nos termos que são mais relevantes para outros analistas e que pode ser mais valiosos aos usuários que precisam para descobrir e entender os dados.

Cada um dessas perspectivas são naturalmente valiosas e com o catálogo de dados do Azure cada usuário pode fornecer as informações que seja significativas neles, embora todos os usuários podem usar essas informações para entender os dados e sua finalidade.

## <a name="expert"></a>Especialista

Especialista é um usuário que foi identificado como tendo uma perspectiva "especialista" informada para um ativo de dados. Qualquer usuário pode adicionar próprios ou outro usuário como um especialista de um ativo. Sendo listado como um especialista não transmita privilégios adicionais no catálogo de dados do Azure; Ele permite aos usuários localizar facilmente essas perspectivas que provavelmente ser útil ao revisar metadados descritivos de um ativo.

## <a name="owner"></a>Proprietário

Um proprietário é um usuário que tem privilégios adicionais para gerenciar um ativo de dados no catálogo de dados do Azure. Os usuários podem obter a propriedade de ativos de dados registrados e proprietários podem adicionar outros usuários como proprietários de colegas. Para obter mais informações, consulte [como gerenciar ativos de dados](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Propriedade e gerenciamento estão disponíveis apenas no Standard Edition do Azure catálogo de dados.

## <a name="registration"></a>Registro

Registro é o ato de extrair metadados de ativos de dados de uma fonte de dados e copiá-la para o serviço de catálogo de dados do Azure. Ativos de dados que tenham sido registrados podem ser anotados e descobertos.

## <a name="see-also"></a>Consulte também

- [O que é o catálogo de dados do Azure?](data-catalog-what-is-data-catalog.md) -Este artigo fornece uma visão geral do serviço de catálogo de dados do Azure, o valor que ele oferece e os cenários que ele oferece suporte.

- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) - este artigo fornece um tutorial de ponta a ponta que mostra como usar o catálogo de dados do Azure para descoberta de fonte de dados.  
