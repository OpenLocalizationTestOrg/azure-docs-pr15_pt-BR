<properties 
   pageTitle="Menu Ações do Gerenciador de instantâneo StorSimple MMC | Microsoft Azure"
   description="Descreve como usar as menu ações padrão do Console de gerenciamento da Microsoft (MMC) no Gerenciador de instantâneo StorSimple."
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

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Use as menu Ações do MMC no Gerenciador de instantâneo StorSimple

## <a name="overview"></a>Visão geral

No Gerenciador de instantâneo de StorSimple, você verá as seguintes ações listadas em todos os menus de ação e todas as variações do painel de **ações** . 

- Modo de exibição
- Nova janela a partir daqui 
- Atualizar 
- Lista de exportação 
- Ajuda 

Estas ações fazem parte do Console de gerenciamento Microsoft (MMC) e não são específicas ao Gerenciador de instantâneo StorSimple. Este tutorial descreve estas ações e explica como usar cada um no Gerenciador de instantâneo StorSimple.

## <a name="view"></a>Modo de exibição

Você pode usar a opção de **exibição** para alterar o modo de exibição do painel de **resultados** e alterar o modo de exibição de janela do console. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar o modo de exibição do painel de resultados

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique com botão direito qualquer nó ou expanda o nó um item no painel de **resultados** de atalho e, em seguida, clique na opção de **exibição** . 

3. Para adicionar ou remover as colunas que aparecem no painel de **resultados** , clique em **Adicionar ou remover colunas**. Caixa de diálogo **Adicionar ou remover colunas** é exibida.

    ![Adicionar ou remover colunas do painel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Preencha o formulário da seguinte maneira:

    - Selecione os itens da lista de colunas **disponíveis** e clique em **Adicionar** para adicioná-los à lista de **colunas exibidas** . 

    - Clique em itens na lista de **colunas exibidas** e clique em **Remover** para removê-las na lista. 

    - Selecione um item na lista de colunas **exibidas** e clique em **Mover para cima** ou **Mover para baixo** para mover o item para cima ou para baixo na lista. 

    - Clique em **Restaurar padrões** para retornar para a configuração padrão do painel de **resultados** . 

5. Quando tiver terminado com suas seleções, clique em **Okey**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar o modo de exibição de janela do console

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , qualquer nó de atalho, clique em **Exibir**e, em seguida, clique em **Personalizar**. A caixa de diálogo **Personalizar** é exibida.

    ![Personalizar a janela do console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Marque ou desmarque as caixas de seleção para mostrar ou ocultar itens na janela do console. Quando tiver terminado com suas seleções, clique em **Okey**.

## <a name="new-window-from-here"></a>Nova janela a partir daqui

Você pode usar a opção de **Nova janela a partir daqui** para abrir uma nova janela de console.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela de console

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , qualquer nó de atalho e, em seguida, clique em **Nova janela a partir daqui**. 

    Uma nova janela é exibida, mostrando apenas o escopo que você selecionou. Por exemplo, se você com o botão direito no nó **Diretivas de Backup** , a nova janela mostrará somente o nó de **Políticas de Backup** no painel de **escopo** e uma lista de políticas de backup definidas no painel de **resultados** . Consulte o exemplo a seguir.

    ![Nova janela a partir daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Atualizar

Você pode usar a ação **Atualizar** para atualizar a janela do console.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela de console

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique com botão direito qualquer nó ou expanda o nó um item no painel de **resultados** de atalho e, em seguida, clique em **Atualizar**. 

## <a name="export-list"></a>Lista de exportação

Você pode usar a ação de **Exportar lista** para salvar uma lista em um arquivo de valores separados por vírgula (CSV). Por exemplo, você pode exportar a lista de políticas de backup ou o catálogo de backup. Em seguida, você pode importar o arquivo CSV em um aplicativo de planilha para análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para salvar uma lista em um arquivo de valores separados por vírgula (CSV)

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique com botão direito qualquer nó ou expanda o nó um item no painel de **resultados** de atalho e, em seguida, clique em **Exportar lista**. 

3. A caixa de diálogo **Exportar lista** é exibida. Preencha o formulário da seguinte maneira: 

    1. Na caixa **nome do arquivo** , digite um nome para o arquivo CSV ou clique na seta para selecionar na lista suspensa.

    2. Na caixa **Salvar como tipo** , clique na seta e selecione um tipo de arquivo na lista suspensa.

    3. Para salvar somente os itens selecionados, selecione as linhas e clique em caixa de seleção **Salvar apenas linhas selecionadas** . Para salvar todos os exportado listas, desmarque a caixa de seleção **Salvar apenas linhas selecionadas** .

    4. Clique em **Salvar**.

    ![Exportar a lista como um arquivo de valores separados por vírgula](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Ajuda

Você pode usar o menu **Ajuda** para exibir a Ajuda online disponível para o Gerenciador de instantâneo StorSimple e o MMC.

#### <a name="to-view-available-online-help"></a>Para exibir a Ajuda online disponível

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique com botão direito qualquer nó ou expanda o nó um item no painel de **resultados** de atalho e, em seguida, clique em **Ajuda**. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [interface de usuário do Gerenciador de instantâneo StorSimple](storsimple-use-snapshot-manager.md).
- Saiba mais sobre como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
