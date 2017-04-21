<properties
   pageTitle="Criar links nos artigos de redução" description="Explica como referências cruzadas em redução de código." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Vinculação orientação para conteúdo técnico Azure

### <a name="links-from-one-acom-article-to-another"></a>Links de um artigo ACOM para outro

Para criar um link de embutida de um artigo técnico ACOM para outro artigo técnico ACOM, use a seguinte sintaxe de link:  

- Um artigo em um links do diretório de serviço para outro artigo na mesma pasta de serviço:

  `[link text](article-name.md)`

- Links um artigo de uma subpasta de serviço para um artigo no diretório raiz:

  `[link text](../article-name.md)`

- Um artigo nos links de diretório raiz para um artigo em uma subpasta de serviço: 

  `[link text](./service-directory/article-name.md)`

- Um artigo nos links de subpasta serviço para um artigo em outra subpasta de serviço:

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Links para âncoras

Você não precisa criar âncoras - são gerados automaticamente no momento para todos os títulos de H2 de publicação. A única coisa que você precisa fazer é criar links para as seções H2.

- Para vincular um título dentro do mesmo artigo:

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Para vincular a uma âncora em outro artigo no mesmo subdiretórios:

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Para vincular a uma âncora em outra subpasta de serviço:

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Uma maneira de automatizar o processo de criação de links no seu artigos para links de âncora gerado automaticamente é [MarkdownAnchorLinkGenerator - uma ferramenta para gerar links de âncora para ACOM no formato correto](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Links de inclui

Como incluir arquivos estão localizados em outro diretório, você precisará usar mais caminhos relativos, conforme mostrado abaixo. Para vincular a um artigo de um arquivo de incluir, use este formato:

    [link text](../articles/service-folder/article-name.md)
    
Saiba mais sobre como usar um arquivo de inclusão nas [diretrizes de extensões de redução de personalizado](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Links em seletores

Se você tiver seletores que são inseridos em um inclusão, você usaria esse tipo de vinculação: 

    > [AZURE. LISTA de SELETOR (Dropdown1 | Dropdown2)]     -  [(Texto1 | Example1)](../articles/service-folder/article-name1.md)
    - [(Texto1 | Example2)](../articles/service-folder/article-name2.md)
    - [(Texto2 | Exemplo 3)](../articles/service-folder/article-name3.md)
    - [(Texto2 | Exemplo4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Links de estilo de referência

Você pode usar links de estilo de referência para facilitar a leitura do seu conteúdo de origem. Os links de estilo de referência substitua a sintaxe de link embutida sintaxe simplificada que permite mover os URLs longos para o final do artigo. Aqui está o exemplo do Daring bola de fogo:

Texto embutido:

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Referências de link no final do artigo:

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Certifique-se de que incluir o espaço após a vírgula, antes que o link. Quando você vincula a outros artigos técnicos, se você esquecer de incluir o espaço, o link será desfeito no artigo publicado. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Link para páginas ACOM que não fazem parte do conjunto de documentação técnica

Para vincular a uma página de ACOM (como uma página de preços, página de SLA ou qualquer outra coisa que não seja um artigo de documentação), usar uma URL absoluta, mas omitir a localidade. O objetivo aqui é que funcionam de links no GitHub e no site renderizado:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Link para o MSDN ou TechNet

Quando você precisar vincular a MSDN ou TechNet, use o link completo para o tópico e remover a en-us localidade de idioma do link. 

### <a name="use-friendly-link-text-for-all-links"></a>Usar texto de link amigável para todos os links

As palavras que você incluir em um link devem ser amigáveis - em outras palavras, elas devem ser palavras em inglês normais ou o título da página que você está se vinculando. Não use "Clique aqui". É ruim para SEO e não sejam adequados para descrever o destino.

**Corrigi:**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Incorreto:**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>FWLinks

Evite FWLinks (nosso sistema de redirecionamento) no conteúdo de azure.microsoft.com. Eles devem ser usados somente como um último recurso quando você precisa criar um link para uma página cuja URL que você ainda não souber. Eles são quase nunca realmente necessários. Para ACOM, você define o nome do arquivo, para que você possa saber o que ele estará antecedência. Para um tópico de biblioteca que ainda não foi publicado, você pode criar um link que usa o tópico GUID para que você não precisa usar um FWLink.

Se você deve usar um FWLink em uma página da web, inclua o parâmetro P para torná-lo um redirecionamento permanente:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Quando você cola a URL de destino para a ferramenta de FWLink, lembre-se de remover a localidade se o link de destino estiver ACOM, ou o MSDN ou TechNet library.

## <a name="remember-the-azure-library-chrome"></a>Lembre-se cromado da biblioteca Azure!
Se você quiser vincular a um tópico de biblioteca Azure que reside [neste](https://msdn.microsoft.com/library/azure)nó, lembre-se de especificar o Azure chrome no link (/ azure /). O chrome Azure compartilha as opções de navegação ACOM e exibe somente o conteúdo Azure da biblioteca MSDN. Um link corretamente supervisionado tem esta aparência:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Caso contrário, a página será processada no modo de exibição padrão do MSDN, com toda a árvore MSDN exibida.

### <a name="contributors-guide-links"></a>Links de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
