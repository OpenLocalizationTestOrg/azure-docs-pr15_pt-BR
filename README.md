# <a name="azure-technical-documentation-contributor-guide"></a>Guia de Colaborador Azure documentação técnica

Encontrar o repositório de GitHub que hospeda a fonte para a documentação técnica que será publicada para o Centro de documentação do Azure em [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Esse repositório também contém orientações para ajudá-lo a contribuir para a nossa documentação técnica.  Para obter uma lista dos artigos guia dos colaboradores, consulte [o índice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuir para a documentação do Azure

Agradecemos seu interesse na documentação Azure!

* [Maneiras de contribuição](#ways-to-contribute)
* [Código de conduzir](#code-of-conduct)
* [Sobre as suas contribuições ao conteúdo do Azure](#about-your-contributions-to-azure-content)
* [Organização de repositório](#repository-organization)
* [Usar o GitHub, gito e este repositório](#use-github-git-and-this-repository)
* [Como usar redução para formatar seu tópico](#how-to-use-markdown-to-format-your-topic)
* [Comentários, comentários e suporte](./contributor-guide/feedback-and-comments.md)
* [Mais recursos](#more-resources)
* [Índice de artigos da guia os colaboradores tudo](./contributor-guide/contributor-guide-index.md) (abre a nova página)

## <a name="ways-to-contribute"></a>Maneiras de contribuição 

Você pode contribuir para a [documentação do Azure](http://azure.microsoft.com/documentation/) de algumas maneiras diferentes:

* Contribui para uma [discussão de fórum](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Envie comentários Disqus na parte inferior dos artigos.
* Você pode facilmente contribuir para artigos técnicos na interface do usuário GitHub. Localizar o artigo neste repositório, ou consulte o artigo [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e clique no link no artigo que vai para a fonte GitHub para o artigo.
* Se você estiver fazendo alterações substanciais para um artigo existente, adicionar ou alterar imagens ou colaborador um novo artigo, você precisa bifurcação este repositório, instale gito Bash, redução Pad e aprenda alguns comandos gito.

##<a name="code-of-conduct"></a>Código de conduzir

Este projeto tem adotaram o [Microsoft Abrir fonte de código de conduzir](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte o [Código de conduzir perguntas Frequentes](https://opensource.microsoft.com/codeofconduct/faq/) ou contato [opencode@microsoft.com](mailto:opencode@microsoft.com) com quaisquer outras perguntas ou comentários.

##<a name="about-your-contributions-to-azure-content"></a>Sobre as suas contribuições ao conteúdo Azure

###<a name="minor-corrections"></a>Correções secundárias

Correções secundárias ou esclarecimentos que enviar para exemplos de código e documentação este repo estão cobertos pelo [Azure site termos de uso (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Envios maiores

Se você enviar uma solicitação de recepção com alterações novas ou significativas a documentação e exemplos de código, enviaremos um comentário no GitHub solicitando que você envie um contrato de licença de contribuição online (CLA) se você estiver em um desses grupos:

* Membros do grupo de tecnologias abertas da Microsoft.
* Colaboradores que não funcionam para Microsoft.

Precisamos que você preencha o formulário online antes de nós pode aceitar a solicitação de recepção.

Detalhes completos estão disponíveis em [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organização de repositório

O conteúdo do repositório de conteúdo do azure segue a organização da documentação no [Azure.Microsoft.com](http://azure.microsoft.com). Este repositório contém duas pastas de raiz:

### <a name="articles"></a>\articles

A pasta *\articles* contém os artigos de documentação formatados como arquivos de redução com uma extensão de *.md* .

Artigos no diretório raiz são publicados em Azure.Microsoft.com no caminho *http://azure.microsoft.com/documentation/articles/ {artigo-nome-sem-md} /*.

* **Nomes de arquivo do artigo:** Consulte [nosso arquivo orientações de nomenclatura](./contributor-guide/file-names-and-locations.md).

Artigos dentro de sua própria pasta de serviço são publicados em Azure.Microsoft.com no caminho *http://azure.microsoft.com/documentation/articles/service-folder/ {artigo-nome-sem-md} /*

* **Subpastas de mídia:** A pasta de *\articles* contém a pasta *\media.* para arquivos de mídia do artigo do diretório raiz, dentro do que são subpastas com as imagens para cada artigo.  As pastas de serviço contêm uma pasta de mídia separadas para os artigos dentro de cada pasta de serviço. As pastas de imagem do artigo são nomeadas idêntico ao arquivo artigo, menos a extensão de arquivo *.md* .

### <a name="includes"></a>\includes

Você pode criar seções de conteúdo reutilizáveis a serem incluídos em um ou mais artigos. Consulte [extensões personalizados usadas em nosso conteúdo técnico](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>modelos de \markdown

Esta pasta contém nosso modelo de redução padrão com a formatação de redução básica que você precisa para um artigo.

### <a name="contributor-guide"></a>\contributor-Guide

Esta pasta contém artigos que fazem parte da guia dos nossos colaboradores.  

## <a name="use-github-git-and-this-repository"></a>Usar o GitHub, gito e este repositório

Para obter informações sobre como Contribuir, como usar o GitHub UI para contribuir pequenas alterações e como bifurcação e clonar o repositório de contribuições mais significativas, consulte [instalar e configurar ferramentas de autoria em GitHub](./contributor-guide/tools-and-setup.md).

Se você instalar GitBash e escolha funcionem localmente, as etapas para criar uma nova ramificação local de trabalho, fazer alterações e enviar que as alterações de volta à ramificação principal estão listadas na [gito comandos para criar um novo artigo ou atualizar um artigo existente](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Ramificações

Recomendamos que você crie local ramificações de trabalho que um escopo específico de alteração de destino. As ramificações devem ser limitada a um único conceito/artigo tanto para simplificar o fluxo de trabalho e reduzir a possibilidade de conflitos de mesclagem.  Os seguintes esforços são do escopo apropriado para uma nova ramificação:

* Um novo artigo (e imagens associadas)
* Edições de ortografia e gramática em um artigo.
* Aplicando uma única alteração de formatação em um conjunto grande de artigos (por exemplo, novos direitos autoral rodapé).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Como usar redução para formatar seu tópico

Todos os artigos neste repositório usam GitHub flavored redução.  Aqui está uma lista de recursos.

- [Noções básicas de redução](https://help.github.com/articles/markdown-basics/)

- [Redução de imprimíveis cheatsheet](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Para nossa lista de editores de redução, consulte o [tópico de configuração e ferramentas](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Metadados do artigo

Artigo metadados permitem certas funcionalidades no site azure.microsoft.com, como atribuição de autor, atribuição colaboradores, trilhas, descrições de artigo e SEO otimizações bem como Microsoft relatório usa para avaliar o desempenho do conteúdo. Portanto, os metadados são importante! [Veja a seguir as orientações para garantir que os metadados é feita à direita](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Rótulos

Rótulos automatizados são atribuídos a retirar solicitações para ajudar a gerenciar o fluxo de trabalho de solicitação de recepção e para ajudar a informar o que está acontecendo com a sua solicitação de recepção:

* Contrato de licença de contribuição relacionados
    * Cla não necessária: A alteração é relativamente secundária e não exige que você entre um CLA.
    * obrigatório Cla: O escopo da alteração é relativamente grande e exige que você entre um CLA.
    * Cla assinados: O colaborador assinado CLA, para que a solicitação de recepção pode agora Avançar para revisão.
* Pilar rótulos: rótulos como PnP, aplicativos modernos e TDC ajudam a categorizar as solicitações de pull pela organização interna que precisa revisar a solicitação de recepção.
* Alterar enviada ao autor: autor foi notificado da solicitação pull pendente.

## <a name="more-resources"></a>Mais recursos

Consulte o [índice da guia do nosso Colaborador](./contributor-guide/contributor-guide-index.md) para todos os nossos tópicos de orientação.
