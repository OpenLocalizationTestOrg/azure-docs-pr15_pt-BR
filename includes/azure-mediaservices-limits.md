Recurso|Limite padrão|Limite máximo
---|---|---
Azure contas de serviços de mídia (AMS) em uma única assinatura||25
Ativos por conta AMS||1.000.000<sup>1</sup>
Tarefas encadeadas por trabalho||30
Ativos por tarefa||50
Ativos por trabalho||100
Trabalhos por conta AMS ||50.000<sup>2</sup>
Locators exclusivos associados a um ativo de uma só vez||5<sup>4</sup>
Ao vivo canais por conta AMS </p></td>|5</p></td>|N /<sup>1</sup>
Programas em estado interrompido por canal </p></td>|50</p></td>|N /<sup>1</sup>
Programas em execução estado por canal </p></td>|3</p></td>|3
Pontos de extremidade de streaming em estado por conta AMS de execução</p></td>|2</p></td>|N /<sup>1</sup>
Streaming unidades por ponto de extremidade de streaming </p></td>|10 </p></td>|N /<sup>1</sup>
Unidades de codificação por conta AMS </p></td>|25</p></td>|N /<sup>1</sup>
Contas de armazenamento | |1.000<sup>5</sup>
Políticas || 1.000.000<sup>6</sup>

<sup>1</sup> você pode solicitar para atualizar os limites da cota abrindo um tíquete. Não crie mais contas AMS para aumentar a limites, em vez disso, envie um tíquete.

<sup>2</sup> esse número inclui trabalhos enfileirados, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando **IJob.Delete** ou a solicitação HTTP **Excluir** .

<sup>3</sup> ao fazer uma solicitação para entidades de trabalho de lista, um máximo de 1.000 será retornado por solicitação. Se você precisar controlar todos os trabalhos enviados, você pode usar início/ignorar conforme descrito nas [Opções de consulta de sistema do OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> locators não foram projetados para gerenciar o controle de acesso por usuário. Para dar direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

<sup>5</sup> as contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup> há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para a política de localizador ou ContentKeyAuthorizationPolicy). Você deve usar a mesma identificação de política se você sempre está usando os mesmos dias / permissões de acesso / etc.
