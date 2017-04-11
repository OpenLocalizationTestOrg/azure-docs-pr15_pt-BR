<properties
    pageTitle="Criar um índice de pesquisa do Azure usando o Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Crie um índice usando o Portal do Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Criar um índice de pesquisa do Azure usando o Portal do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTANTE](search-create-index-rest-api.md)

Este artigo o orientará durante o processo de criação de um [índice](search-what-is-an-index.md) de pesquisa do Azure usando o Portal do Azure.

Antes de seguir este guia e criar um índice, você deve ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>Eu. Acesse seu blade de pesquisa do Azure
1. Clique em "Todos os recursos" no menu no lado esquerdo do [Portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o serviço de pesquisa do Azure

## <a name="ii-add-and-name-your-index"></a>II. Adicione e nomeie seu índice
1. Clique no botão "Adicionar índice"
2. O nome de índice de pesquisa do Azure. Já que estamos criando um índice para procurar hotéis neste guia, têm nomeamos nosso índice "hotéis".
  * O nome do índice deve começar com uma letra e conter apenas letras minúsculas, dígitos ou traços ("-").
  * Semelhante ao seu nome de serviço, o nome do índice que você escolha também fará parte da URL do ponto de extremidade onde você enviará as solicitações de HTTP para a API de pesquisa do Azure
3. Clique na entrada "Campos" para abrir um novo blade

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Crie e defina os campos do índice
1. Selecionando a entrada "Campos", um novo blade será aberto com um formulário para inserir sua definição de índice.
2. Adicione campos ao índice usando o formulário.

  * Um campo de *chave* do tipo Edm.String é obrigatório para cada índice de pesquisa do Azure. Este campo de chave é criado por padrão com o campo nome "id". Alteramos-lo para "hotelId" em nosso índice.
  * Determinadas propriedades de seu esquema de índice podem ser definidas apenas uma vez e não podem ser atualizadas no futuro. Por isso, todas as atualizações de esquema que exijam a reindexação como alterar tipos de campo não são possíveis atualmente após a configuração inicial.
  * Cuidadosamente escolhemos os valores de propriedade para cada campo com base em como podemos acha que serão usados em um aplicativo. Tenha suas necessidades de negócios e experiência de usuário pesquisa em mente ao criar o índice como cada campo deve ser atribuído as [propriedades adequadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estas propriedades de controle que pesquisar recursos (filtragem, faceting, classificação, pesquisa de texto completo, etc.) se aplicam a quais campos. Por exemplo, é provável que as pessoas pesquisando hotéis será interessadas em correspondências de palavra-chave no campo "Descrição", portanto podemos habilitar a pesquisa de texto completo para esse campo definindo a propriedade "Pesquisável".
    * Você também pode definir o [Analisador de idioma](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) para cada campo clicando na guia "Analisador" na parte superior da lâmina. Você pode ver abaixo que selecionamos um analisador de francês para um campo em nosso índice destinado para texto em francês.

3. Clique em **Okey** na lâmina "Campos" para confirmar as definições de campo
4. Clique em **Okey** na lâmina "Adicionar índice" para salvar e criar o índice que você acabou de definir.

Nas capturas de tela abaixo, você pode ver como temos nomeados e definiu os campos para nosso índice "hotéis".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Próximo
Depois de criar um índice de pesquisa do Azure, você estará pronto para [carregar seu conteúdo no índice](search-what-is-data-import.md) para poder começar a pesquisar seus dados.
