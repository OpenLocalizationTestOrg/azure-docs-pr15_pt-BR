<properties
   pageTitle="Como anotar fontes de dados | Microsoft Azure"
   description="Artigo de instruções realce como anotar ativos de dados no catálogo de dados do Azure, incluindo especialistas, marcas, descrições e nomes amigáveis."
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


# <a name="how-to-annotate-data-sources"></a>Como anotar fontes de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Em outras palavras, catálogo de dados é tudo sobre ajudar as pessoas a descobrir, entender e usar fontes de dados e ajudar as empresas para obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada com o catálogo de dados, seus metadados é copiado e indexado pelo serviço, mas a história não termina lá. Catálogo de dados permite aos usuários fornecer seus próprios metadados descritivos – como descrições e marcas – para complementar os metadados extraídos da fonte de dados e fazer com que a fonte de dados mais compreendido para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos tem uma opinião. E esta é uma boa coisa.
Catálogo de dados reconhece que diferentes usuários têm perspectivas diferentes em fontes de dados da empresa, e que cada um dessas perspectivas pode ser valiosa. Considere o seguinte cenário:

* O administrador do sistema sabe o contrato de nível de serviço para os servidores ou serviços que hospedam a fonte de dados.
* O administrador de banco de dados sabe o agendamento de backup para cada banco de dados e as janelas de processamento de ETL permitido.
* O proprietário do sistema sabe o processo para os usuários devem solicitar acesso à fonte de dados.
* Os administradores de dados sabe como os atributos da fonte de dados e ativos mapeiam para o modelo de dados corporativos.
* O analista sabe como os dados são usados no contexto dos processos de negócios que ele oferece suporte.

Cada um dessas perspectivas é valiosa e catálogo de dados usa uma abordagem de crowdsourcing aos metadados que permite que cada uma delas ser capturado e usado para fornecer uma imagem completa de fontes de dados registrados. Usando o portal de catálogo de dados, cada usuário pode adicionar e editar seus próprio anotações, e ser capaz de exibir anotações fornecidas por outros usuários.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
Catálogo de dados compatível com os seguintes tipos de anotações:

| Anotação     | Anotações                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome amigável  | Nomes amigáveis podem ser fornecidos no nível de ativos de dados, para tornar mais fácil compreendidos de ativos de dados. Nomes amigáveis são mais úteis quando o nome de objeto subjacente é clara para você, abreviado ou caso contrário, não significativo aos usuários.                                                                                                                            |
| Descrição    | Descrições podem ser fornecidas na dados ativos e o atributo / níveis de coluna. Descrições são anotações de texto curto de forma livre que descrevem o usuário perspectiva nos ativos de dados ou seu uso.                                                                                                                                                              |
| Marcas (marcas de usuário)          | Marcas podem ser fornecidas na dados ativos e o atributo / níveis de coluna. Marcas de usuário são rótulos definidos pelo usuário que podem ser usados para categorizar ativos de dados ou atributos.                                                                                                                                                                                                    |
| Marcas (marcas de Glossário)          | Marcas podem ser fornecidas na dados ativos e o atributo / níveis de coluna. Glossário marcas são termos do glossário definidas centralmente que podem ser usados para categorizar ativos de dados ou atributos usando uma taxonomia de negócios comuns. Para obter mais informações, consulte [como configurar o Glossário de negócios para regulamentadas marcação](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Especialistas        | Especialistas podem ser fornecidos no nível de ativos de dados. Especialistas identificar usuários ou grupos com perspectivas especialistas nos dados e podem servir como pontos de contato para usuários que descobrir as fontes de dados registrados e tiver perguntas que não são respondidas pelas anotações existentes.  |
| Solicitar acesso | Solicitar acesso informações podem ser fornecidas no nível de ativos de dados. Essas informações são para os usuários que Descubra uma fonte de dados que eles ainda não tem permissões para acessar. Os usuários podem digitar o endereço de email do usuário ou grupo que concede acesso, a URL do processo ou ferramenta que os usuários precisam ter acesso, ou podem inserir o próprio processo como texto. |
| Documentação | Documentação pode ser fornecida no nível de ativos de dados. Documentação de ativos é RTF informações que podem incluir links e imagens e que pode fornecer qualquer informação não escondida pelas marcas e descrições. |


## <a name="annotating-multiple-assets"></a>Fazendo anotações vários ativos
Ao selecionar vários ativos de dados no portal do catálogo de dados, os usuários podem anotar todos os ativos selecionados em uma única operação. Anotações serão aplicadas a todos os ativos selecionados, tornando mais fácil selecionar e fornecer uma descrição consistente e conjuntos de marcas e especialistas para ativos de dados relacionados.

> [AZURE.NOTE] Marcas e especialistas também podem ser fornecidos quando a ferramenta de registro de fonte de registro ativos de dados usando os dados de catálogo de dados.

Quando a seleção de várias tabelas e exibições, somente colunas todas selecionadas dados ativos têm em comum será exibida no portal do catálogo de dados. Isso permite aos usuários fornecer marcas e descrições de todas as colunas com o mesmo nome para todos os ativos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e descoberta
Assim como os metadados extraídos da fonte de dados durante o registro é adicionado ao índice de pesquisa de catálogo de dados, metadados fornecidos pelo usuário também é indexado. Isso significa que não apenas anotações tornam mais fácil para os usuários a entender os dados que eles descobrir, anotações também tornam mais fácil para os usuários descobrir ativos de dados com anotações ao pesquisar usando os termos que fazem sentido para eles.

## <a name="summary"></a>Resumo
Registrar uma fonte de dados com o catálogo de dados garante que os dados detectáveis copiando metadados estruturais e descritivo da fonte de dados para o serviço de catálogo. Depois que uma fonte de dados tiver sido registrada, os usuários podem fornecer as anotações para facilitar a descobrir e entender de dentro do portal de catálogo de dados.

## <a name="see-also"></a>Consulte também
- Tutorial de [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como anotar fontes de dados.
