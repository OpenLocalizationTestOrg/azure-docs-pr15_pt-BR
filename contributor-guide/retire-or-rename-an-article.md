# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Etapas a serem seguidas quando você desativa ou alterar o nome de um artigo técnico ACOM

Este guia é para SMEs listados como o autor de um artigo que precisa ser retirada da seção documentação técnica do azure.microsoft.com. As etapas também se aplicam se um arquivo é renomeado.

Se você for um membro da nossa comunidade Azure e você acha que um artigo deve ser desativado por algum motivo, deixe um comentário no fluxo de comentário de Disqus para o artigo informar o autor que algo está errado com o artigo.

Autores de EA precisam seguir várias etapas para retirar normalmente conteúdo para que usuários do site não tenham uma experiência ruim quando podemos desativa o conteúdo do site. Excluir o artigo ou alterar seu nome deve ser a última coisa que acontece!

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Etapa 1: Definir o artigo a seguir não-índice/não e publicá-lo novamente (recomendado)

A primeira coisa que você deve fazer é republicar o artigo como nenhum índice/não-seguir algumas semanas antes de excluí-lo realmente. Isso é considerado o melhor prática "pré-trabalho" para retirada de conteúdo. Isso remove o artigo índices de mecanismo de pesquisa para que as pessoas não encontrará o artigo em pesquisa. [Consulte o wiki interno para obter detalhes.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Etapa 2: Gerenciar links de entrada (obrigatório)

Determine se há qualquer links de entrada não sejam da Microsoft ao seu conteúdo. Frequentemente, blogs, fóruns e outros conteúdos na web aponta para artigos. Frequentemente, você pode trabalhar com os proprietários de blog para alterar esses links e você pode remover ou atualizar os vínculos de postagens no Fórum. Ferramentas de análise da Web podem informar se houver algum vínculos de entrada de alto tráfego que talvez você precise gerenciar dessa forma.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Etapa 3: Remover todas as referências cruzadas para o artigo do repositório de conteúdo técnico (obrigatório)

Não dependem redireciona para cuidam de referências cruzadas de outros artigos. Atualizar ou remover a cruz referências para o artigo que você está excluindo ou renomeando, incluindo em artigos pertencentes a outras pessoas.

1. Certifique-se de que você está trabalhando em uma ramificação local atualizada – executar `git pull upstream master` (ou a variação apropriada sobre este comando.

2.  Varredura da pasta azure-conteúdo-pr/artigos e a pasta azure-conteúdo-pr/inclui qualquer artigos e inclui que o link para o artigo que você deseja retirar e remova as referências cruzadas ou substituí-los por uma referências cruzadas de novo apropriada. Você pode usar uma pesquisa e substituir utilitário para localizar as referências cruzadas se tiver um instalado. Se não estiver, você pode usar o Windows PowerShell gratuitamente! Veja aqui como usar o PowerShell para localizar as referências cruzadas:

 a. Inicie o Windows PowerShell.

 b. No prompt do PowerShell, altere para a pasta do azure-conteúdo-pr\articles:

 `cd azure-content-pr\articles`

 c. Digite este comando, que lista todos os arquivos que contêm uma referência para o artigo que você está excluindo:

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Se você preferir enviar a lista de nomes de arquivo para um arquivo de texto (em psoutput.txt neste caso, denominado), você pode:

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Adicionar e confirmar todas as suas alterações, enviá-los à sua bifurcação e criar uma solicitação de recepção para mover suas alterações de seu bifurcação à ramificação mestre do repositório principal.

## <a name="step-4-update-the-fwlink-tool-required"></a>Etapa 4: Atualizar a ferramenta de FWLink (obrigatória)

Verificar a ferramenta FWLink qualquer FWLinks que pode apontar para o artigo. Aponte qualquer FWLinks para conteúdo de substituição; Se você não estiver usando o alias que possui o link, associá-la. Se os proprietários não atualize o link, um tíquete com MSCOM deseja que o link alterado de arquivo. Mais informações - [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Etapa 5: Remover todas as referências cruzadas para o artigo de outras páginas no azure.microsoft.com e crie um redirecionamento para a página de retirada, se apropriado (obrigatório)

Você terá de trabalhar com a pessoa que mantém e atualiza a página de aterrissagem documentação de seu serviço para essa parte. Contate seu parceiro de equipe conteúdo se você não souber quem essa pessoa. A pessoa que mantém e atualiza a página inicial do documento será necessário fazer duas coisas:

1. No Visual Studio, digitalize **toda** a ACOM web solução para referências ao arquivo para retirar cruzadas. Remover as referências cruzadas ou substituí-las por uma referência cruzada atualizada. Você precisará remover os links HTML, bem como as cadeias de recursos relacionados para os links HTML. Mais informações - consulte o [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Se houver um artigo de substituição, crie um redirecionamento. Mais informações - consulte o [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Verifique as alterações no repositório.

## <a name="step-6-retire-the-article"></a>Etapa 6: Retirar o artigo

Depois de concluir as etapas anteriores e essas alterações são ao vivo, você pode excluir o artigo do repositório. 

**Importante:** Quando você excluir arquivos, você deve usar o `git add --all` comando.

## <a name="step-7-remove-links-from-msdn-required"></a>Etapa 7: Remover links da MSDN (obrigatório)

Examine a ferramenta de p & r conteúda links desfeitos para o tópico descartado ou renomeado e remover/correção os links em todos os tópicos MSDN afetados.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Etapa 8: Remover páginas armazenadas em cache de mecanismos de pesquisa (somente se absolutamente necessário)

Siga este somente se o conteúdo precisa ser removido rapidamente devido a problemas de cliente legais ou grave. As práticas recomendadas do Google, exclusões de página de prioridade normal apenas devem ser tratadas pelo processos de mecanismo de pesquisa natural. Vá para essas páginas da web para remover páginas da web em cache de mecanismos de pesquisa:

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Links de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)
