<properties
    pageTitle="Importar dados para pesquisa de Azure usando indexadores no Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Use o Assistente de dados de importação do Azure pesquisa no Portal do Azure para dados de rastreamento de Azure Blob storage, armazenamento de tabela, banco de dados SQL e SQL Server em VMs do Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importar dados para pesquisa de Azure usando o portal

O portal do Azure fornece um Assistente de **Importação de dados** no painel de pesquisa do Azure para carregar dados em um índice. 

  ![Importar dados na barra de comando][1]

Internamente, o assistente configura e invoca um *indexador*, automatizar várias etapas do processo de indexação: 

- Conectar a uma fonte de dados externos na assinatura atual do Azure
- Gerar um esquema de índice baseado na estrutura de dados de origem
- Criar documentos com base em um conjunto de registros recuperado da fonte de dados
- Carregar documentos para o índice no seu serviço de pesquisa

Você pode experimentar este fluxo de trabalho usando os dados de exemplo em DocumentDB. Visite [Introdução ao Azure pesquisa no Portal do Azure](search-get-started-portal.md) para obter instruções.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Fontes de dados compatíveis com o Assistente para importação de dados

O Assistente de importação de dados compatível com as fontes de dados a seguir: 

- Banco de dados do SQL Azure
- Dados relacionais do SQL Server em uma máquina virtual do Azure
- DocumentDB Azure
- Armazenamento de Blob Azure (na visualização)
- Azure armazenamento de tabela (na visualização)

Um conjunto de dados bidimensional é uma entrada necessária. Você só pode importar de uma única tabela, modo de exibição de banco de dados ou estrutura de dados equivalentes. Você deve criar essa estrutura de dados antes de executar o assistente.

Observe que algumas de indexadores ainda estão no preview, o que significa que a definição de indexador é feita com a versão de visualização da API. Consulte [Visão geral do indexador](search-indexer-overview.md) para mais informações e links.

## <a name="connect-to-your-data"></a>Conectar aos dados

1. Acesse o [Portal do Azure](https://portal.azure.com) e o painel de serviço abertas. Você pode clicar em **Serviços de pesquisa** na barra de salto para mostrar os serviços existentes na assinatura atual. 

2. Clique em **Importar dados** na barra de comandos para abrir slide a lâmina de importar dados.  

3. Clique em **conectar-se aos seus dados** para especificar uma definição de fonte de dados usada por um indexador. Para fontes de dados de dentro de assinatura, o assistente geralmente pode detectar e ler as informações de conexão, minimizando requisitos gerais de configuração.

| | |
|--------|------------|
|**Fonte de dados existente** | Se você já tiver indexadores definidos no seu serviço de pesquisa, você pode selecionar uma definição de fonte de dados existente para importar de outro.|
|**Banco de dados do SQL Azure** | Nome de serviço, credenciais para um usuário de banco de dados com permissão de leitura e um nome de banco de dados podem ser especificado na página ou por meio de uma cadeia de caracteres de conexão de ADO.NET. Escolha a opção de cadeia de conexão para exibir ou personalizar propriedades. <br/><br/>A tabela ou modo de exibição que fornece o conjunto de linhas deve ser especificado na página. Essa opção aparece depois que a conexão for estabelecida com sucesso, dando uma lista suspensa para que você possa fazer uma seleção.|
|**SQL Server na máquina virtual Azure** | Especifique um nome totalmente qualificado serviço, ID de usuário e senha e banco de dados como uma cadeia de conexão. Para usar esta fonte de dados, você deve que você tiver instalado anteriormente um certificado no armazenamento local que criptografa a conexão. <br/><br/>A tabela ou modo de exibição que fornece o conjunto de linhas deve ser especificado na página. Essa opção aparece depois que a conexão for estabelecida com sucesso, dando uma lista suspensa para que você possa fazer uma seleção.
|**DocumentDB** |Requisitos de incluem a conta, banco de dados e conjunto. Todos os documentos na coleção serão incluídos no índice. Você pode definir uma consulta para nivelar ou filtrar o conjunto de linhas, ou para detectar documentos alterados para operações de atualização de dados subsequentes.|
|**Armazenamento de blob do Microsoft Azure** | Requisitos de incluem a conta de armazenamento e um contêiner. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, você pode especificar a parte do diretório virtual do nome como uma pasta em contêiner. Consulte o [Armazenamento de Blob indexação (visualização)](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
|**Armazenamento de tabela do Microsoft Azure** | Requisitos de incluem a conta de armazenamento e um nome de tabela. Opcionalmente, você pode especificar uma consulta para recuperar um subconjunto das tabelas. Consulte o [Armazenamento de tabela de indexação (visualização)](search-howto-indexing-azure-tables.md) para obter mais informações. |

## <a name="customize-target-index"></a>Personalizar o índice de destino

Um índice preliminar normalmente é inferido do dataset. Adicionar, editar ou excluir campos para concluir o esquema. Além disso, definir atributos no nível de campo para determinar os comportamentos de pesquisa subsequentes.

1. Em **Personalizar índice de destino**, especifique o nome e uma **tecla** usado para identificar exclusivamente cada documento. A chave deve ser uma cadeia de caracteres. Se os valores de campo incluem espaços ou traços Certifique-se de definir opções avançadas em **importar seus dados** para suprimir a seleção de validação para esses caracteres.

2. Revisar e revisar os campos restantes. Tipo e o nome de campo geralmente são preenchidas para você. Você pode alterar o tipo de dados.

3. Definir atributos de índice para cada campo:

 - Recuperável retorna o campo nos resultados da pesquisa.
 - Filtráveis permite que o campo a ser referenciada em expressões de filtro.
 - Classificável permite que o campo a ser usado em uma classificação.
 - Facetable permite que o campo de navegação com faceta.
 - Pesquisável permite que a pesquisa de texto completo.
  
4. Clique na guia de **Analisador** se você quiser especificar um analisador de idioma no nível de campo. Analisadores de idioma só podem ser especificadas neste momento. Usando um analisador personalizado ou um analisador de idioma não como palavra-chave, o padrão e assim por diante, exigirão código.

 - Clique em **pesquisável** para designar pesquisa de texto completo no campo e habilitar a lista suspensa de analisador.
 - Escolha o analisador desejado. Consulte [criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.

5. Clique na **Suggester** para permitir que as sugestões de consulta com previsão de escrita nos campos selecionados.


## <a name="import-your-data"></a>Importar seus dados

1. Em **importar seus dados**, forneça um nome para o indexador. Lembre-se de que o produto do Assistente Importar dados é um indexador. Posteriormente, se desejar exibir ou editá-lo, você selecionará-la a partir do portal em vez de por reexecutando o assistente. 

2. Especifique a agenda, o que é baseada no fuso horário regional na qual o serviço está provisionado.

3. Definir opções avançadas para especificar limites em se indexação possa continuar se um documento é interrompido. Além disso, você pode especificar se os campos de **chave** podem conter espaços e barras.  

## <a name="edit-an-existing-indexer"></a>Editar um indexador existente

No painel de serviço, clique duas vezes no bloco indexador para o slide uma lista de todos os indexadores criado para sua assinatura. Clique duas vezes em uma de indexadores para executar, editar ou excluí-la. Você pode substituir o índice por outro existente, alterar a fonte de dados e definir opções para os limites de erro durante a indexação.

## <a name="edit-an-existing-index"></a>Editar um índice existente

Em pesquisa do Azure, atualizações estruturais para um índice exigirá uma recriação de índice, que consiste de exclusão do índice, recriar o índice e recarregar dados. Atualizações de estruturais incluem alterar um tipo de dados e renomear ou excluir um campo.

Edições que não exijam uma recriação incluem a adição de um novo campo, alterando perfis de pontuação, alterem suggesters ou alterando analisadores de idioma. Consulte [Atualizar índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) para obter mais informações.

## <a name="next-step"></a>Próxima etapa

Revise estes links para saber mais sobre indexadores:

- [Indexação banco de dados do SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexação DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Armazenamento de Blob indexação (prévia)](search-howto-indexing-azure-blob-storage.md)
- [Armazenamento de tabela indexação (prévia)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

