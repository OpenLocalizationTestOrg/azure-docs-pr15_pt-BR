<properties 
   pageTitle="Interface de usuário do Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Descreve a interface de usuário do Gerenciador de instantâneo StorSimple e explica como usá-lo para gerenciar trabalhos de backup e o catálogo de backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="storsimple-snapshot-manager-user-interface"></a>Interface de usuário do Gerenciador de instantâneo StorSimple

## <a name="overview"></a>Visão geral

O Gerenciador de instantâneo StorSimple tem uma interface de usuário intuitiva que você pode usar para fazer e gerenciar backups. Este tutorial fornece uma introdução para a interface de usuário e, em seguida, explica como usar cada um dos componentes. Para obter uma descrição detalhada do Gerenciador de instantâneo StorSimple, consulte [o que é o Gerenciador de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição do console

Para exibir a interface do usuário, clique no ícone do Gerenciador de instantâneo StorSimple na área de trabalho. A janela do console é exibida, conforme mostrado na ilustração a seguir.

![Gerenciador de instantâneo StorSimple painéis](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela de console possui cinco elementos principais. Clique no link apropriado para uma descrição completa de cada elemento.

- [Barra de menus](#menu-bar) 
- [Barra de ferramentas](#tool-bar) 
- [Painel de escopo](#scope-pane) 
- [Painel de resultados](#results-pane) 
- [Painel de ações](#actions-pane) 

Além disso, o Gerenciador de instantâneo StorSimple oferece suporte a [navegação de teclado e um número de atalhos](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade do console

A interface de usuário do Gerenciador de instantâneo StorSimple suporta os recursos de acessibilidade fornecidos pelo sistema operacional Windows e do Console de gerenciamento da Microsoft (MMC), bem como alguns atalhos de teclado do Gerenciador de instantâneo StorSimple – específicos. 

- Para obter uma descrição dos recursos de acessibilidade do Windows, vá para [atalhos de teclado do Windows](https://support.microsoft.com/kb/126449). 

- Para obter uma descrição dos recursos de acessibilidade do MMC, vá para a [acessibilidade do MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)

- Para obter uma descrição dos recursos de acessibilidade Gerenciador de instantâneo StorSimple, vá para [atalhos e navegação de teclado](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menus

A barra de menus na parte superior da janela do console contém o [arquivo](#file-menu), [ação](#action-menu), [Exibir](#view-menu), [Favoritos](#favorites-menu), [janela](#window-menu)e [ajudar a](#help-menu) menus.

Clique em qualquer item na barra de menus para ver uma lista dos comandos disponíveis no menu. O exemplo a seguir mostra o menu de **exibição** selecionado na barra de menus.

![Menu de exibição selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Arquivo

O menu **arquivo** contém comandos padrão do Console de gerenciamento da Microsoft (MMC).

#### <a name="menu-access"></a>Menu acessar

Para exibir o menu **arquivo** , clique em **arquivo** na barra de menus. O menu a seguir é exibida.

![Menu Arquivo do Gerenciador de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir descreve os itens que aparecem no menu **arquivo** .

| Item de menu | Descrição |
|:----------|:-------------|
| Novo       | Clique em **novo** para criar um novo console baseado no Gerenciador de instantâneo StorSimple. |
| Abrir      | Clique em **Abrir** para abrir um console existente. |
| Salvar      | Clique em **Salvar** para salvar o console atual. |
| Salvar como   | Clique em **Salvar como** para criar uma nova instância renomeada do console atual. Use a opção **Salvar como** para personalizar um modo de exibição e salvá-lo para recuperação posterior. Por exemplo, você poderia criar snap-ins do Gerenciador de instantâneo StorSimple que apontam para servidores específicos. |
| Adicionar/Remover Snap-in | Clique **Adicionar/Remover Snap-in** para adicionar ou remover snap-ins e organizar nós no painel de **escopo** . Para obter mais informações, vá para [Adicionar, remover e organizar Snap-ins e extensões no MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções   | Clique em **Opções** para alterar o ícone do console, especificar permissões e modos de acesso do usuário ou excluir arquivos de console para aumentar o espaço disponível em disco. |
| Lista de caminhos de arquivo | Clique em um caminho na lista numerada reabrir um arquivo que você abriu recentemente. |
| Sair      | Clique em **Sair** para fechar o menu **arquivo** . |
 
### <a name="action-menu"></a>Menu de ação

Use o menu de **ação** para selecionar de ações disponíveis. Os itens disponíveis dependem da seleção feita no painel de **escopo** ou painel de **resultados** .

#### <a name="menu-access"></a>Menu acessar

Para exibir o menu de **ação** , siga um destes procedimentos:

- Clique em um item no painel de **escopo** ou painel de **resultados** .

- Selecione um item no painel de **escopo** ou painel de **resultados** e, na barra de menus, clique em **ação** . 

Por exemplo, se você seleciona o nó superior do painel de **escopo** e, em seguida, clique com botão direito ou clique em **ação** na barra de menus, o menu a seguir é exibida.
 
![Menu de ação de StorSimple Gerenciador de instantâneo](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O painel de **ações** (à direita do console) contém a mesma lista de ações de menu de **ação** . Além disso, o painel de **ações** contém as opções de menu **modo de exibição** , que permitem que você criar uma exibição personalizada do painel de **resultados** .

![Abra o painel de ações com o menu Exibir](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir contém uma lista de ações de Gerenciador de instantâneo StorSimple alfabética. 

- Na coluna **ação** lista ações que você pode realizar em nós e os resultados. 

- A coluna de **navegação** explica como exibir o menu de **ação** apropriado para que você possa selecionar a ação. Algumas ações aparecem em vários menus de **ação** . Para essas ações, selecione uma opção de **navegação** de lista com marcadores. 

- A coluna **Descrição** descreve como usar cada ação no menu de **ação** ou painel de ações e explica o que ela faz.

>[AZURE.NOTE] O painel de **ações** e menus de **ação** contêm opções adicionais, como o **modo de exibição**, **Nova janela a partir daqui**, **Atualizar**, **Exportar lista**e **Ajuda**. Essas opções estão disponíveis como parte do MMC e não são específicas ao Gerenciador de instantâneo StorSimple. A tabela inclui descrições das seguintes opções.
 
| Ação  | Navegação  | Descrição  |
|:--------|:------------|:-------------|
| Autenticar | Clique no nó de **dispositivos** e um dispositivo no painel de **resultados** de atalho. | Clique em **autenticar** para inserir a senha que você configurou para o dispositivo. |
| Clonar  | Expanda o **Catálogo de Backup**, expanda **Instantâneos de nuvem**, clique em um backup com data e selecione um volume no painel de **resultados** . | Clique em **clonar** para criar uma cópia de um instantâneo de nuvem e armazená-la em um local que você designar. |
| Configurar um dispositivo | Clique com botão direito o nó **dispositivos** . | Clique em **Configurar um dispositivo** para configurar um único dispositivo ou vários dispositivos para se conectar ao host do Windows. |
| Criar uma política de Backup | Siga um destes procedimentos:<ul><li>Clique com botão direito **políticas de Backup**.</li><li>Clique ou expandir **Grupos de Volume**e clique em um grupo de volume.</li><li>Clique ou expandir o **Catálogo de Backup**e clique em um grupo de volume.</li></ul> | Clique em **Criar diretiva de Backup** para configurar um backup agendado para um grupo de volume. |
| Criar grupo de Volume | Siga um destes procedimentos:<ul><li>Clique no nó **Volumes** e clique em um volume no painel de **resultados** .</li><li>Clique com botão direito no nó **Grupos de Volume** .</li></ul> | Clique em **Criar grupo de Volume** para atribuir volumes a um grupo de volume. |
| Excluir | Clique em um nó ou resultado (esse item aparece em muitos menus de **ação** e painéis de **ações** .) | Clique em **Excluir** para excluir o nó ou o resultado que você selecionou. Quando a caixa de diálogo de confirmação aparece, confirme ou cancelar a exclusão. |
| Detalhes | Clique no nó de **dispositivos** e, em seguida, clique em um dispositivo no painel de **resultados** . | Clique em **detalhes** para ver os detalhes de configuração para um dispositivo. |
| Editar | Clique em **Políticas de Backup**e, em seguida, clique com botão direito uma política no painel de **resultados** . | Clique em **Editar** para alterar o agendamento de backup para um grupo de volume. |
| Lista de exportação | Clique em qualquer nó ou resultado (esse item aparece em todos os menus de **ação** e painéis de **ações** .) | Clique em **Exportar lista** para salvar uma lista em um arquivo de valores separados por vírgula (CSV). Em seguida, você pode importar esse arquivo para um aplicativo de planilha para análise. |
| Ajuda | Clique em qualquer nó ou o resultado. (Esse item aparece em todos os menus de **ação** e painéis de **ações** .) | Clique em **Ajuda** para abrir a Ajuda online em uma janela separada do navegador. |
| Nova janela a partir daqui | Clique em qualquer nó ou resultado (esse item aparece em todos os menus de **ação** e painéis de **ações** .) | Clique em **Nova janela a partir daqui** para abrir uma nova janela do Gerenciador de instantâneo StorSimple.|
| Atualizar | Clique em qualquer nó ou resultado (esse item aparece em todos os menus de **ação** e painéis de **ações** .) | Clique em **Atualizar** para atualizar a janela Gerenciador de instantâneo StorSimple exibido no momento. |
| Atualizar dispositivo | Clique no nó de **dispositivos** e um dispositivo no painel de **resultados** de atalho. | Clique em **Atualizar o dispositivo** para sincronizar um dispositivo conectado específico com o Gerenciador de instantâneo StorSimple. |
| Atualizar dispositivos | Clique com botão direito o nó **dispositivos** . | Clique em **Atualizar dispositivos** para sincronizar com o Gerenciador de instantâneo StorSimple de sua lista de dispositivos conectados. |
| Examinar volumes | O nó de **Volumes** de atalho. | Clique em **Examinar volumes** para atualizar a lista de volumes que aparece no painel de **resultados** . |
| Restaurar | Expanda o **Catálogo de Backup**, expandir um grupo de volume, expanda **Instantâneos locais** ou **Instantâneos de nuvem**e, em seguida, clique com botão direito um backup. | Clique em **Restaurar** para substituir os dados de grupo de volume atual com os dados a partir do backup selecionado. |
| Fazer Backup | Siga um destes procedimentos:<ul><li>Expandir **Grupos de Volume**e clique em um grupo de volume.</li><li>Expanda o **Catálogo de Backup**e clique em um grupo de volume.</li></ul> | Clique em **Fazer Backup** para iniciar um trabalho de backup imediatamente. |
| Exibição de importações de alternância | Clique com botão direito no nó superior no painel de **escopo** (o nó **Gerenciador de instantâneo StorSimple** nos exemplos). | Clique em **Exibição de importações de alternância** para mostrar ou ocultar os grupos de volume e backups associados que foram importados no painel de serviço do Gerenciador de StorSimple. |

### <a name="view-menu"></a>Menu Exibir

Use o menu **Exibir** para criar uma exibição personalizada do conteúdo do painel de **resultados** . No menu **Exibir** contém opções de **Adicionar ou remover colunas** e **Personalizar** .

#### <a name="menu-access"></a>Menu acessar

Você pode acessar o menu de **exibição** na barra de menus ou no painel de **ações** .

![Menu modo de exibição do Gerenciador de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir descreve os itens que aparecem no menu **Exibir** .

| Item de menu  | Descrição |
|:-----------|:-------------|
| Adicionar ou remover colunas | Clique em **Adicionar ou remover colunas** para adicionar ou remover colunas no painel de **resultados** . |
| Personalizar | Clique em **Personalizar** para mostrar ou ocultar itens na janela do console Gerenciador de instantâneo StorSimple. |

### <a name="favorites-menu"></a>Menu Favoritos

Use o menu **Favoritos** para adicionar, remover e organizar modos de exibição de página e tarefas que você usa com frequência. 

#### <a name="menu-access"></a>Menu acessar

Você pode acessar o menu de **Favoritos** na barra de menus.

![Menu Gerenciador de instantâneo StorSimple Favoritos](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir descreve os itens que aparecem no menu **Favoritos** .

| Item de menu |  Descrição |
|:----------|:-------------|
| Adicionar a Favoritos | Clique em **Adicionar a Favoritos** para adicionar o modo de exibição atual à sua lista de favoritos. |
| Organizar Favoritos | Clique em **Organizar Favoritos** para organizar o conteúdo da pasta Favoritos. |

### <a name="window-menu"></a>Menu janela

Use o menu **janela** para adicionar e reorganizar janelas de console do Gerenciador de instantâneo StorSimple.

#### <a name="menu-access"></a>Menu acessar

Você pode acessar o menu **janela** , na barra de menus.

![Menu janela do Gerenciador de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão atualmente abertas. Clique em qualquer janela nessa lista para trazer a janela para o primeiro plano. 

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir descreve os itens que aparecem no menu janela.

| Item de menu  | Descrição |
|:-----------|:-------------|
| Nova janela | Clique em **Nova janela** para abrir uma nova janela de console (além de janela existente). |
| Em cascata   | Clique **em cascata** para exibir as janelas de console aberto em um estilo em cascata. |
| Lado a lado horizontalmente | Clique em **Lado a lado horizontalmente** para exibir as janelas de console aberto em um formato de bloco (ou grade). |
| Organizar ícones | Se você tiver várias janelas de console abertas e disperso sobre sua área de trabalho, minimizá-los e clique em **Organizar ícones** para organizá-las em uma linha horizontal na parte inferior da tela. |

### <a name="help-menu"></a>Menu Ajuda

Use o menu **Ajuda** para exibir a Ajuda online disponível para o Gerenciador de instantâneo StorSimple e o MMC. Você também pode exibir informações sobre as versões de software MMC e Gerenciador de instantâneo StorSimple que atualmente estão instalados no seu sistema. 

Você pode acessar o menu **Ajuda** na barra de menus. Você também pode acessar tópicos de Ajuda do Gerenciador de instantâneo StorSimple do painel de **ações** .

![Menu Ajuda do Gerenciador de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição do menu

A tabela a seguir descreve os itens que aparecem no menu Ajuda.

| Item de menu  | Descrição  |
|:-----------|:-------------|
| Ajuda sobre o Gerenciador de StorSimple instantâneo | Clique em **Ajuda no Gerenciador de instantâneo StorSimple** para abrir a Ajuda do Gerenciador de instantâneo StorSimple em uma janela separada. |
| Tópicos da Ajuda |Clique em **Tópicos da Ajuda** para abrir a Ajuda online do MMC em uma janela separada. |
| Site da Web TechCenter | Clique em **Site da Web TechCenter** para abrir a home page do Microsoft TechNet Tech Center em uma janela separada. |
| Sobre o Console de gerenciamento da Microsoft | Clique em **Sobre o Microsoft Management Console** para ver qual versão do Console de gerenciamento Microsoft está instalada no seu sistema. |
| Sobre o Gerenciador de instantâneo StorSimple | Clique em **Sobre o Gerenciador de instantâneo StorSimple** para ver qual versão do snap-in está instalada no seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas

Barra de ferramentas, localizada abaixo da barra de menu, contém ícones de navegação e tarefas. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições do ícone

A tabela a seguir descreve os ícones que aparecem na barra de ferramentas. 

| Ícone  | Descrição  |
|:------|:-------------| 
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) | Clique no ícone de seta para a esquerda para retornar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) | Clique na seta para a direita para ir para a próxima página (se a seta cinza, a ação não está disponível). |
| ![Ícone para cima](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) | Clique no ícone para cima para subir um nível na árvore do console (painel **escopo** ). |
| ![Mostrar/ocultar árvore de console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) | Clique no ícone de árvore do console Mostrar/ocultar para mostrar ou ocultar o painel de **escopo** . |
| ![Lista de exportação](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) | Clique no ícone de lista de exportação para exportar uma lista para um arquivo CSV que você especificar. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png)  |Clique no ícone de ajuda para abrir um tópico da Ajuda online MMC. |
| ![Mostrar/ocultar painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) | Clique em Mostrar/Ocultar ícone do painel de **ações** para mostrar ou ocultar o painel de **ações** . 
 
## <a name="scope-pane"></a>Painel de escopo

O painel de **escopo** é o painel mais à esquerda na UI Gerenciador de instantâneo StorSimple. Ele contém a árvore do console (ou nó) e é o mecanismo de navegação principal para o Gerenciador de instantâneo StorSimple. 
 
### <a name="scope-pane-structure"></a>Estrutura do painel de escopo

O painel de **escopo** contém uma série de objetos clicáveis (nós) organizada em uma estrutura de árvore. 

![Painel de escopo](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

- Para expandir ou recolher um nó, clique no ícone de seta ao lado do nome de nó.

- Para exibir o status ou o conteúdo de um nó, clique no nome de nó. As informações exibidas no painel de **resultados** . 

O painel de **escopo** contém os nós a seguir: 

- [Nó dispositivos](#devices-node) 
- [Nó volumes](#volumes-node) 
- [Nó de grupos de volume](#volume-groups-node) 
- [Nó de políticas de backup](#backup-policies-node) 
- [Nó do catálogo de backup](#backup-catalog-node) 
- [Nó de trabalhos](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas do painel de escopo

Você pode usar o painel de **escopo** para concluir uma ação em um nó específico. Para selecionar uma tarefa, siga um destes procedimentos:

- O nó de atalho e, em seguida, selecione a tarefa no menu que aparece.

- Clique no nó e, em seguida, clique em **ação** na barra de menus. Selecione a tarefa no menu que aparece.

- Clique no nó e selecione a ação no painel de **ações** .

Quando você seleciona um nó e use qualquer um desses métodos para ver uma lista de tarefas, somente as ações que podem ser executadas nesse nó são mostradas.

### <a name="devices-node"></a>Nó dispositivos

O nó de **dispositivos** representa o StorSimple dispositivos e StorSimple virtual que estão conectadas ao Gerenciador de instantâneo StorSimple. Selecione este nó para conectar e configurar um dispositivo e importar seus volumes associados, grupos de volumes e cópias de backup existentes. Vários dispositivos podem ser conectados a um único host.

- Expanda o nó, clique no ícone de seta ao lado de **dispositivos**.

- Para ver um menu de ações disponíveis, clique com botão direito o nó **dispositivos** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de dispositivos configurados, clique em **dispositivos** no painel de **escopo** . A lista de dispositivos, juntamente com informações sobre cada dispositivo, aparece no painel de **resultados** .

### <a name="volumes-node"></a>Nó volumes

O nó de **Volumes** representa as unidades que correspondem aos volumes montados pelo host, inclusive aqueles descobertos por iSCSI e aqueles descoberta por meio de um dispositivo. Use este nó para exibir a lista de volumes disponíveis e atribuir volumes individuais aos grupos de volume.

- Expanda o nó, clique no ícone de seta ao lado de **Volumes**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó **Volumes** ou qualquer um de nós que aparecem no modo de exibição expandido de atalho.

- Para ver uma lista de volumes, clique em **Volumes** no painel de **escopo** . A lista de volumes, junto com informações sobre cada volume, aparece no painel de **resultados** .

### <a name="volume-groups-node"></a>Nó de grupos de volume

Grupos de volume também conhecido como são grupos de consistência. Cada grupo de volume é um pool de volumes relacionados ao aplicativo que ajuda a garantir a consistência do aplicativo durante operações de backup. Use o nó de **Grupos de Volume** para configurar esses grupos e para fazer backups interativos ou criar agendas de backup. 

- Expanda o nó, clique no ícone de seta ao lado de **Grupos de Volume**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó **Grupos de Volume** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de grupos de volume, clique em **Grupos de Volume** no painel de **escopo** . A lista de grupos de volume, junto com informações sobre cada grupo de volume, é exibida no painel de **resultados** .

### <a name="backup-policies-node"></a>Nó de políticas de backup

Políticas de backup são agendas de trabalho para locais e na nuvem instantâneos. Use o nó de **Políticas de Backup** para especificar com que frequência um backup é criado e quanto tempo um backup deve ser mantido. 

- Expanda o nó, clique no ícone de seta ao lado de **Políticas de Backup**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó **Diretivas de Backup** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de políticas de backup, clique em **Políticas de Backup** no painel de **escopo** . Lista de políticas de backup, junto com informações sobre cada política, é exibida no painel de **resultados** .

>[AZURE.NOTE] Você pode manter um máximo de 64 backups.


### <a name="backup-catalog-node"></a>Nó do catálogo de backup

O nó do **Catálogo de Backup** contém listas de backups locais e fora do Azure StorSimple volumes. Este nó é organizado por grupo de volume e cada contêiner de grupo de volume contém estruturas separadas para instantâneos locais (o nó de s **Instantâneo Local**) e nuvem instantâneos (o nó **Nuvem instantâneos** ). Quando expandida, cada contêiner de grupo de volume lista todos os backups bem-sucedida que foram tirados interativamente ou por uma política de configurado.

- Expanda o nó, clique no ícone de seta ao lado de **Catálogo de Backup**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó do **Catálogo de Backup** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de instantâneos backup, clique em **Catálogo de Backup** no painel de **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel de **resultados** .

### <a name="local-snapshots-node"></a>Nó de instantâneos local

O nó de **Instantâneos locais** lista instantâneos locais para um grupo de volume específico. O nó está localizado sob o nó do **Catálogo de Backup** no painel de **escopo** . Instantâneos locais são cópias de point-in-time dos dados de volume que estão armazenados no dispositivo StorSimple do Azure. Normalmente, esse tipo de backup pode ser criado restaurado e rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.

- Expanda o nó, clique no ícone de seta ao lado de **Instantâneos locais**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó **Instantâneos locais** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de instantâneos locais, clique em **Instantâneos locais** no painel de **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel de **resultados** .

### <a name="cloud-snapshots-node"></a>Nó de instantâneos de nuvem

O nó de **Nuvem instantâneos** lista instantâneos de nuvem para um grupo de volume específico. O nó está localizado sob o nó do **Catálogo de Backup** no painel de **escopo** . Nuvem instantâneos são cópias no momento de dados de volume que são armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento externo diferente. Nuvem instantâneos são particularmente úteis em cenários de recuperação de desastres.

- Expanda o nó, clique no ícone de seta ao lado de **Instantâneos de nuvem**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó **Instantâneos de nuvem** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de instantâneos de nuvem, clique em **Nuvem instantâneos** no painel de **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel de **resultados** .

### <a name="jobs-node"></a>Nó de trabalhos

O nó de **trabalhos** contém informações sobre os trabalhos de backup agendadas, sendo executados e recentemente concluídas. 

- Expanda o nó, clique no ícone de seta ao lado de **trabalhos**.

- Para ver um menu de ações disponíveis, clique com botão direito no nó de **trabalhos** ou clique com botão direito qualquer um de nós que aparecem no modo de exibição expandido.

- Para ver uma lista de trabalhos agendados, expanda o nó de **trabalhos** e clique em **agendada**. A lista de trabalhos configurados anteriormente e informações sobre cada trabalho aparece no painel de **resultados** . 

- Para ver uma lista de trabalhos concluídos recentemente, expanda o nó de **trabalhos** e clique em **últimas 24 horas**. Uma lista de trabalhos que foram concluídas nas últimas 24 horas é exibida no painel de **resultados** . O painel de **resultados** também contém informações sobre cada trabalho concluído.

- Para ver uma lista de trabalhos que estão sendo executados, expanda o nó de **trabalhos** e clique em **Executar**. Na lista de trabalhos e informações sobre cada trabalho em execução no momento é exibida no painel de **resultados** .

## <a name="results-pane"></a>Painel de resultados

O painel de **resultados** é o painel central da UI do Gerenciador de instantâneo StorSimple. Ele contém listas e informações detalhadas de status para o nó selecionado no painel de **escopo** .

### <a name="example"></a>Exemplo

Para ver o exemplo a seguir, clique no nó de **Grupos de Volume** no painel de **escopo** . O painel de **resultados** exibe uma lista de grupos de volume com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Você pode configurar os detalhes mostrados no painel de **resultados** : um nó no painel de **escopo** de atalho, clique em **Exibir**e, em seguida, clique em **Adicionar ou remover colunas**.

## <a name="actions-pane"></a>Painel de ações

O painel de **ações** é o painel direito no Gerenciador de instantâneo StorSimple UI. Ele contém um menu de operações que você pode executar no nó, exibição ou dados selecionados no painel **escopo** ou painel de **resultados** . O painel de **ações** contém os mesmos comandos os menus de **ação** que estão disponíveis para itens no painel de **escopo** e painel de **resultados** . Para obter uma descrição de cada ação, consulte a tabela na seção de menu de **ação** .

### <a name="examples"></a>Exemplos

Para ver o exemplo a seguir, no painel de **escopo** , expanda o nó de **trabalhos** e clique em **agendada**. O painel de **ações** exibe as ações disponíveis para o nó **agendadas** .

![Exemplo de tarefas agendadas do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no painel de **escopo** , expanda o nó de **trabalhos** , clique **agendado**e clique em uma tarefa agendada no painel de **resultados** . O painel de **ações** exibe as ações disponíveis para o trabalho agendado, conforme mostrado no exemplo a seguir.

![Exemplo de ações de trabalho do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Atalhos e navegação de teclado

Gerenciador de instantâneo StorSimple habilita os recursos de acessibilidade do sistema operacional Windows e do Console de gerenciamento da Microsoft (MMC). Ele também inclui alguns recursos de navegação de teclado e atalhos que são específicos para o Gerenciador de instantâneo StorSimple, conforme descrito nas seções a seguir.
 
- [Teclas de navegação do teclado](#keyboard-navigation-keys) 
- [Menu da barra teclas de atalho](#menu-bar-shortcut-keys) 
- [Teclas de atalho do painel de escopo](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Teclas de navegação do teclado

A tabela a seguir descreve as teclas que você pode usar para navegar a interface de usuário do Gerenciador de instantâneo StorSimple. 

| Tecla de navegação  | Ação  |
|:----------------|:--------| 
| Tecla seta para baixo | Use a tecla de seta para baixo para mover verticalmente para o próximo item em um menu ou painel. |
| Insira | Pressione a tecla Enter para concluir uma ação e, em seguida, prossiga para a próxima etapa. Por exemplo, você pode pressionar Enter para selecionar a **próxima**, **Okey**ou **criar**e vá para a próxima etapa no assistente.|
| ESC | Pressione a tecla Esc para fechar um menu ou para cancelar e fechar uma página.|
| F1 | Pressione a tecla F1 para exibir um tópico de ajuda para a janela ativa no momento.|
| F5 | Pressione a tecla F5 para atualizar um nó. |
| F6 | Pressione a tecla F6 para mover-se no painel de **escopo** para o painel de **resultados** .|
| F10 | Pressione a tecla F10 para ir para a barra de menus. |
| Tecla de seta para a esquerda | Use a tecla de seta para a esquerda para mover horizontalmente de uma opção de barra de menus para a opção anterior. Quando você move para o item anterior na barra de menus, aparece no menu de ação (ou contexto) para o item anterior. |
| Tecla de seta para a direita | Use a tecla de seta para a direita para mover horizontalmente de opção de barra de um menu para o próximo. Quando você move para o próximo item na barra de menus, no menu de ação (ou contexto) para o novo item é exibida.
| Tecla TAB | Use a tecla Tab para mover para o próximo painel no console ou para a próxima caixa de seleção ou texto em uma página. |
| Seta para cima | Use a tecla de seta para cima para mover verticalmente para o item anterior em um menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Menu da barra teclas de atalho

A tabela a seguir descreve as combinações de teclas de atalho para a barra de menus. Depois que você pressiona as teclas de atalho e o menu é aberto, você pode usar teclas de atalho do menu (as teclas sublinhadas no menu). Para saber mais sobre a barra de menus, vá para a [barra de menus](#menu-bar).

| Atalho | Resultado                    | Tecla do menu de atalho | Resultado          |
|:---------|:--------------------------|:------------------|:----------------|
| ALT + F    | Abre o menu **arquivo** .  | N | Abre uma nova instância do console.   |
|          |                           | O | Abre a página de **Ferramentas administrativas** . |
|          |                           | S | Salva o console do Gerenciador de instantâneo StorSimple.|
|          |                           | R | Abre a página **Salvar como** . |
|          |                           | M | Abre a página **Adicionar/Remover Snap-in** .|
|          |                           | P | Abre a página de **Opções** . |
|          |                           | H | Abre a Ajuda online.|
| ALT + A    | Abre o menu de **ação** .| Posso | Ativa e desativa a opção de exibição de importação.|
|          |                           | W | Abre um novo console do Gerenciador de instantâneo StorSimple.|
|          |                           | F | Atualiza o console do Gerenciador de instantâneo StorSimple.|
|          |                           | L | Abre a página de **Lista de exportação** . 
|          |                           | H | Abre a Ajuda online.|
| ALT + V    | Abre o menu de **exibição** .  | R | Abre a página **Adicionar/remover colunas** . |
|          |                           | U | Abre a página de **Personalizar modo de exibição** . |
| ALT + O    | Abre o menu de **Favoritos** . | R | Abre a página **Adicionar a Favoritos** . |
|          |                           | O | Abre a página de **Organizar Favoritos** .|
| ALT + W    | Abre o menu de **janela** .| N | Abre outra janela do Gerenciador de instantâneo StorSimple.|
|          |                           | C | Exibe todas as janelas do console aberto em um estilo em cascata.|
|          |                           | T | Exibe todas as janelas do console aberto em um padrão de grade. |
|          |                           | Posso | Organiza os ícones em uma linha horizontal na parte inferior da tela.|
| ALT + H    | Abre o menu **Ajuda** .  | H | Abre a Ajuda online.|
|          |                           | T | Abre a página de web do Microsoft TechNet Tech Center.|
|          |                           | R | Abre a página **Sobre Console de gerenciamento do Microsoft** . |
 
### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho do painel de escopo

As tabelas a seguir mostram o atalho de combinações de teclas para cada nó no painel de **escopo** . 

- [Teclas de atalho de nó de dispositivos](#devices-node-shortcut-keys)
- [Teclas de atalho de nó volumes](#volumes-node-shortcut-keys)
- [Teclas de atalho de nó de grupos de volume](#volume-groups-node-shortcut-keys)
- [Teclas de atalho de nó de políticas de backup](#backup-policies-node-shortcut-keys)
- [Teclas de atalho de nó do catálogo de backup](#backup-catalog-node-shortcut-keys)
- [Teclas de atalho de nó de trabalhos](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho de nó de dispositivos

| Menu de atalho | Resultado                               |
|:--------------|:-------------------------------------|
| C             | Abre a página de **Configurar um dispositivo** . |
| D             | Atualiza a lista de dispositivos e detalhes do dispositivo.|
| V             | Abre o menu de **exibição** . |
| W             | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó de **detalhes** . |
| F             | Atualiza o console do Gerenciador de instantâneo StorSimple. |
| L             | Abre a página de **Lista de exportação** . 
| H             | Abre a Ajuda online.|
 

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho de nó volumes

| Menu de atalho   | Resultado                              |
|:----------------|:------------------------------------|
| V               | Atualiza a lista de volumes.        |
| V (pressione duas vezes) | Abre o menu de **exibição** .            |
| W               | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó de **Volumes** .|
| F               | Atualiza o console do Gerenciador de instantâneo StorSimple.|
| L               | Abre a página de **Lista de exportação** . 
| H               | Abre a Ajuda online.|
 
#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho de nó de grupos de volume

| Menu de atalho   | Resultado                              |
|:----------------|:------------------------------------|
| G               | Abre a página **criar um grupo de Volume** . |
| V               | Abre o menu de **exibição** . |
| W               | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó **Grupos de Volume** .|
| F               | Atualiza o console do Gerenciador de instantâneo StorSimple. |
| L               | Abre a página de **Lista de exportação** . |
| H               | Abre a Ajuda online.|

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho de nó de políticas de backup

| Menu de atalho   | Resultado                              |
|:----------------|:------------------------------------|
| B               | Abre a página **criar uma política** . |
| V               | Abre o menu de **exibição** .            |
| W               | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó **Grupos de Volume** .|
| F               | Atualiza o console do Gerenciador de instantâneo StorSimple.|
| L               | Abre a página de **Lista de exportação **. 
| H               | Abre a Ajuda online.|
 
#### <a name="backup-catalog-node-shortcut-keys"></a>Teclas de atalho de nó do catálogo de backup

| Menu de atalho   | Resultado                              |
|:----------------|:------------------------------------|
| W               | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó **Grupos de Volume** . |
| F               | Atualiza o console do Gerenciador de instantâneo StorSimple. |
| H               | Abre a Ajuda online.|
 
#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho de nó de trabalhos

| Menu de atalho   | Resultado                              |
|:----------------|:------------------------------------|
| V               | Abre o menu de **exibição** .            |
| W               | Abre um novo console do Gerenciador de instantâneo StorSimple voltado para o nó de **trabalhos** .|
| F               | Atualiza o console do Gerenciador de instantâneo StorSimple.|
| L               | Abre a página de **Lista de exportação** .     |
| H               | Abre a Ajuda online                   |
 
## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para se conectar e gerenciar dispositivos](storsimple-snapshot-manager-manage-devices.md).
