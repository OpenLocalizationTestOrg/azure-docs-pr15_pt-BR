# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Retirar a etiqueta de solicitação e práticas recomendadas para colaboradores do Microsoft Azure documentação

Para publicar alterações a documentação, você pode enviar solicitações de recepção de seu bifurcação. Cada solicitação de recepção tem a ser revisado antes sendo mesclados. Leia este artigo para entender como você deve trabalhar com revisores de solicitação de recepção e como você pode criar solicitações de recepção que são mais fácil e rápido revisar para que a fila de solicitação de recepção funciona melhor para todos.

## <a name="working-with-pull-request-reviewers"></a>Trabalhando com revisores de solicitação de recepção

Veja aqui as Noções básicas que você precisa saber sobre como trabalhar com os revisores de solicitação de recepção. 

- <b>Compreenda a função do revisor de solicitação de recepção. O revisor:</b>
  - Garante a qualidade básica do conteúdo
  - Impede perdas no repositório
  - Fornece comentários antes de mesclar

  Revisores de solicitação de recepção estão em uma função de gestão de conteúdo. O objetivo principal não é simplesmente mesclar tudo o que é enviado assim que possível. Espere feedback que exige que você fazer atualizações, especialmente para artigos novos e muito revisadas.

- <b>Planejar com o revisor de solicitação de recepção:</b>
  - Para solicitações de recepção de alta prioridade
  - Solicitações de recepção para lançamentos sincronizou/com data
  - Solicitações de recepção que alterar ou adicionar muitos arquivos

- <b>SLA para pegar solicitar revisão</b>

  No repositório particular, sempre que sua solicitação de recepção insere a fila de solicitação de recepção com o rótulo pronto para mesclar, a equipe tenta revisar a solicitação de recepção dentro de 12 horas de negócios (M-F, para 5: 00) e fornecer comentários ou mesclar se nenhum comentários são necessário. Este SLA aplica-se ao ato de revisão do PR, não a mesclagem. PRs serão mesclados quando eles atendem aos [critérios para mesclagem](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Verifique a fila de solicitação de recepção funcionam melhor para todos

Há duas realidades básicas na fila PR:

- Solicitações de recepção que são pequenos no escopo e que contêm alterações muito semelhantes levam menos tempo a ser revisado. 
- Solicitações de recepção que são grandes no escopo ou que contenham mistos, diferentes tipos de alterações levar mais tempo a ser revisado.

Você pode ajudar a tornar a fila de solicitação de recepção funcionam melhor seguindo estas práticas recomendadas:

- Separada atualizações secundárias para artigos existentes de novos artigos ou revisões principais. Trabalhe nessas alterações em ramificações de trabalho separadas. 

- Quando você exclui artigos ou imagens, não combinar as exclusões com novas adições de conteúdo ou atualizações. Manipule o alterações/novo conteúdo em uma ramificação de trabalho separada.

- Para versões ou refatoração de conteúdo, planeje com revisor PR. Talvez você precise sua ajuda para criar uma ramificação de lançamento ou para coordenar horários de mesclagem com tempos de publicação para que seu conteúdo é publicado no momento certo.

- Se você estiver tentando coordenar as atualizações feitas no repo a ACOM (isto é, as alterações de navegação esquerdo, inicial páginas, redirecionamentos ou mapas de aprendizagem) com as alterações que você está fazendo no repositório do azure-conteúdo-pr, você deve coordenar esse trabalho com antecedência com revisor PR. Caso contrário, o risco de ter muitos links desfeitos.

## <a name="criteria-for-expedited-pull-requests"></a>Critérios para solicitações de recepção emitidos

- Entre em contato azdocprs para agilizar PRs somente quando absolutamente necessário. Você pode solicitar acelerada PR tratamento de zona de vermelho, privacidade, legal e problemas de segurança; para experiências de cliente realmente quebrado; e para encaminhamentos executivos. 
- Conteúdo para lançamentos de recurso não está qualificada para manipulação emitida - o conteúdo de lançamento do recurso requer planejamento prévio ou ele deve ser tratado por meio de fila de prioridade padrão.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>Com pressa? Enviar PRs que podem ser aceitos automaticamente

Use as regras de automação PRMerger para obter mais de seu PRs diárias mescladas automaticamente.

PRMerger pode aceitar sua PR automaticamente, se:
* Ela afeta 10 arquivos ou menos.
* Ele contém 15 confirmações ou menos.
* Menos de 20% das alterações de texto.
* Seletores/switchers não estão atualizadas.
* Não há arquivos são excluídos ou adicionados.
* Não há imagens são novas, alterados ou excluídos.

Se sua solicitação de recepção não atender a esses critérios, o rótulo "PRmerger não é possível mesclar" será atribuído automaticamente para que você saiba requer revisão por um revisor PR humano.

### <a name="need-to-make-a-lot-of-little-changes"></a>Precisa fazer muitas pequenas alterações?

Faça sua sinalização das regras de automação do PRMerger acima e faça o seguinte:
* Envie artigos com alterações light juntos em um PR com arquivos de 10 ou menos.
* Crie um PR separada para artigos no qual a alteração imagens ou seletores. Isso requer revisão humano.
* Crie um PR separada para artigos novos ou excluídos. Isso requer revisão humano.

## <a name="related"></a>Relacionados

- [Critérios de qualidade de solicitação de recepção revisar](contributor-guide-pr-criteria.md)

- [Retirar automação de comentário de solicitação](contributor-guide-pull-request-comments.md)
