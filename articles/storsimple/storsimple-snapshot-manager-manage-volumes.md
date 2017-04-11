<properties 
   pageTitle="Gerenciador de instantâneo StorSimple e volumes | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para exibir e gerenciar volumes e configurar backups."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Usar o Gerenciador de instantâneo StorSimple para exibir e gerenciar volumes

## <a name="overview"></a>Visão geral

Você pode usar o nó Gerenciador de instantâneo StorSimple **Volumes** (no painel de **escopo** ) para selecionar volumes e exibir informações sobre eles. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo host. O nó de **Volumes** mostra volumes locais e tipos de volume que são suportados pelo StorSimple, incluindo volumes detectados por meio do uso iSCSI e um dispositivo. 

Para obter mais informações sobre volumes suportados, vá para o [suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó de **Volumes** também permite examinar ou excluir volumes depois Gerenciador de instantâneo StorSimple descobre-los. 

Este tutorial explica como você pode montar, inicializar, formatar volumes e, em seguida, use o Gerenciador de instantâneo de StorSimple para:

- Exibir informações sobre volumes 
- Excluir volumes
- Examinar volumes 
- Configurar um volume básico e faça backup
- Configurar um volume espelhado dinâmico e faça backup

>[AZURE.NOTE] Todas as ações de nó de **Volume** também estão disponíveis no painel de **ações** .
 
## <a name="mount-volumes"></a>Montar volumes

Use o procedimento a seguir para montar, inicializar e formatar volumes de StorSimple. Este procedimento usa o gerenciamento de disco, um utilitário do sistema de gerenciamento de discos e os volumes de correspondente ou partições. Para obter mais informações sobre o gerenciamento de disco, vá para [Gerenciamento de disco](https://technet.microsoft.com/library/cc770943.aspx) no site do Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para montar volumes

1. No computador host, inicie o iniciador Microsoft iSCSI.

2. Fornecer um dos endereços IP da interface como o portal de destino ou endereço IP de descoberta e conecte-se ao dispositivo. Quando o dispositivo estiver conectado, os volumes estarão acessíveis ao seu sistema do Windows. Para obter mais informações sobre como usar o iniciador Microsoft iSCSI, vá para a seção "Conexão com um dispositivo de destino iSCSI" no [iniciador de iSCSI instalando e Configurando Microsoft][1].

3. Use qualquer uma das seguintes opções para iniciar o gerenciamento de disco:

    - Digite diskmgmt. msc na caixa **Executar** .

    - Iniciar o Gerenciador de servidor, expanda o nó de **armazenamento** e selecione **Gerenciamento de disco**.

    - Iniciar **Ferramentas administrativas**, expanda o nó de **Gerenciamento do computador** e, em seguida, selecione **Gerenciamento de disco**. 

    >[AZURE.NOTE] Você deve usar privilégios de administrador para executar o gerenciamento de disco.
 
4. Colocar os volumes online:

   1. Em gerenciamento de disco, clique com botão direito qualquer volume marcada como **Offline**.

   2. Clique em **Reativar disco**. O disco deve ser marcado **Online** após o disco está reativado.

5. Inicialize os volumes:

   1. Clique com botão direito os volumes detectados.

   2. No menu, selecione **Inicializar disco**.

   3. Na caixa de diálogo **Inicializar disco** , selecione os discos que você deseja inicializar e clique em **Okey**.

6. Formatar volumes simples:

   1. Clique com botão direito um volume que você deseja formatar.

   2. No menu, selecione **Novo Volume simples**.

   3. Use o Assistente de novo Volume simples para formatar o volume:

      - Especifique o tamanho do volume.
      - Fornece uma letra de unidade.
      - Selecione o sistema de arquivos NTFS.
      - Especifique um tamanho de unidade de alocação de 64 KB.
      - Execute uma formatação rápida.

7. Formatar partição vários volumes. Para obter instruções, vá para a seção, "Partições e Volumes" em [Implementando o gerenciamento de disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Exibir informações sobre os volumes

Use o procedimento a seguir para exibir informações sobre local e volumes de StorSimple do Azure.

#### <a name="to-view-volume-information"></a>Para exibir informações de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique no nó **Volumes** . Uma lista de locais e montados volumes, incluindo todos os volumes de StorSimple do Azure, aparece no painel de **resultados** . As colunas no painel de **resultados** são configuráveis. (O nó **Volumes** de atalho, selecione **Exibir**e selecione **Adicionar/remover colunas**).

    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Coluna de resultados | Descrição 
    :--------------|:-------------
    Nome           | A coluna **nome** contém a letra atribuída a cada volume descoberto.
    Dispositivo         | A coluna de **dispositivo** contém o endereço IP do dispositivo conectado ao computador host.
    Nome de Volume do dispositivo | A coluna de **Nome de Volume do dispositivo** contém o nome do volume dispositivo ao qual pertence o volume selecionado. Este é o nome do volume definido no portal de clássico do Azure para aquele volume específico.
    Caminhos de acesso   | A coluna de **Caminhos de acesso** exibe o caminho de acesso ao volume. Este é o ponto de letra ou montagem de unidade em que o volume está acessível no computador host.
 
## <a name="delete-a-volume"></a>Excluir um volume

Use o procedimento a seguir para excluir um volume do Gerenciador de instantâneo StorSimple.

>[AZURE.NOTE] Você não pode excluir um volume se ele for parte de qualquer grupo de volume. (A opção Excluir não está disponível para volumes que são membros de um grupo de volume). Você deve excluir o grupo de todo o volume para excluir o volume.


#### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique no nó **Volumes** . 

3. No painel de **resultados** , clique com botão direito no volume que você deseja excluir.

4. No menu, clique em **Excluir**. 

    ![Excluir um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. Caixa de diálogo **Excluir Volume** é exibida. Digite **Confirmar** na caixa de texto e clique em **Okey**.

6. Por padrão, o Gerenciador de instantâneo StorSimple backup um volume antes de excluí-lo. Essa precaução pode proteger você contra perda de dados se a exclusão foi não intencional. Gerenciador de instantâneo StorSimple exibe uma mensagem de andamento de **Instantâneo automático** enquanto faz backup o volume. 

    ![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Examinar volumes

Use o procedimento a seguir para examinar os volumes conectados ao Gerenciador de instantâneo StorSimple.

#### <a name="to-rescan-the-volumes"></a>Para examinar os volumes

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , **Volumes**de atalho e, em seguida, clique em **Examinar volumes**.

    ![Examinar volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Esse procedimento sincroniza a lista de volume com o Gerenciador de instantâneo StorSimple. Alterações, como volumes novos ou excluídas, serão refletidas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e fazer backup de um volume básico

Use o procedimento a seguir para configurar um backup de um volume básico e iniciar um backup imediatamente ou criar uma política para backups agendados.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que o computador de dispositivo e host StorSimple estão configurados corretamente. Para obter mais informações, vá para [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Instalar e configurar o Gerenciador de instantâneo StorSimple. Para obter mais informações, vá para [Implantar o Gerenciador de instantâneo StorSimple](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar o backup de um volume básico

1. Crie um volume básico do dispositivo StorSimple.

2. Montar, inicializar e formatar o volume, conforme descrito em [montar volumes](#mount-volumes). 

3. Clique no ícone do Gerenciador de instantâneo StorSimple na área de trabalho. A janela Gerenciador de instantâneo StorSimple aparece. 

4. No painel de **escopo** , o nó de **Volumes** de atalho e selecione **Examinar volumes**. Quando a verificação for concluída, uma lista de volumes deverão aparecer no painel de **resultados** . 

5. No painel de **resultados** , clique com botão direito no volume e selecione **Criar grupo de Volume**. 

    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Na caixa de diálogo **Criar grupo de Volume** , digite um nome para o grupo de volume, atribuir volumes a ele e clique em **Okey**.

7. No painel de **escopo** , expanda o nó de **Grupos de Volume** . O novo grupo de volume deverá aparecer sob o nó **Grupos de Volume** . 

8. Clique com botão direito no nome do grupo de volume.

    - Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 

    - Para agendar um backup automático, clique em **Criar diretiva de Backup**. Na página **Geral** , selecione um grupo de volume na lista. Na página **cronograma** , insira os detalhes do cronograma. Quando tiver terminado, clique em **Okey**. 

9. Para confirmar que iniciou o trabalho de backup, expanda o nó de **trabalhos** no painel de **escopo** e, em seguida, clique no nó **em execução** . A lista de em execução trabalhos aparece no painel de **resultados** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e fazer backup de um volume espelhado dinâmico

Conclua estas etapas para configurar o backup de um volume espelhado dinâmico:

- Etapa 1: Usar o gerenciamento de disco para criar um volume espelhado dinâmico. 

- Etapa 2: Use StorSimple instantâneo Manager configurar backup.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que o computador de dispositivo e host StorSimple estão configurados corretamente. Para obter mais informações, vá para [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Instalar e configurar o Gerenciador de instantâneo StorSimple. Para obter mais informações, vá para [Implantar o Gerenciador de instantâneo StorSimple](storsimple-snapshot-manager-deployment.md).

- Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **2 de disco**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Etapa 1: Usar o gerenciamento de disco para criar um volume espelhado dinâmico

Gerenciamento de disco é um utilitário de sistema de gerenciamento de discos e os volumes ou partições que eles contêm. Para obter mais informações sobre o gerenciamento de disco, vá para [Gerenciamento de disco](https://technet.microsoft.com/library/cc770943.aspx) no site do Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico

1. Use qualquer uma das seguintes opções para iniciar o gerenciamento de disco: 

   - Abrir a caixa **Executar** , digite **diskmgmt. msc**e pressione Enter.

   - Iniciar o Gerenciador de servidor, expanda o nó de **armazenamento** e selecione **Gerenciamento de disco**. 

   - Iniciar **Ferramentas administrativas**, expanda o nó de **Gerenciamento do computador** e, em seguida, selecione **Gerenciamento de disco**. 

2. Certifique-se de que você tenha dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **2 de disco**.) 

3. Na janela Gerenciamento de disco, na coluna à direita do painel inferior, clique com botão direito **disco 1** e selecione **Novo Volume espelhado**. 

    ![Novo Volume espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Na página do Assistente de **Novo Volume espelhado** , clique em **Avançar**.

5. Na página **Selecionar discos** , selecione **disco 2** no painel **selecionado** , clique em **Adicionar**e clique em **Avançar**. 

6. Na página **atribuir uma letra de unidade ou caminho** , aceite os padrões e clique em **Avançar**. 

7. Na página **Formatar Volume** , na caixa **Tamanho da unidade de alocação** , selecione **64K**. Marque a caixa de seleção **executar uma formatação rápida** e clique em **Avançar**. 

8. Na página **Concluindo o novo Volume espelhado** , examine suas configurações e clique em **Concluir**. 

9. Aparece uma mensagem para indicar que o disco básico será convertido em um disco dinâmico. Clique em **Sim**.

    ![Mensagem de conversão de disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. Em gerenciamento de disco, verifique se que o disco 1 e 2 de disco são mostrados como volumes espelhados dinâmicos. (**Dinâmico** deve aparecer na coluna status, e a cor da barra de capacidade deve mudar para vermelho, indicando um volume espelhado.) 

    ![Discos dinâmicos do disco gerenciamento espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Etapa 2: Usar StorSimple instantâneo Manager para configurar backup

Use o procedimento a seguir para configurar um volume espelhado dinâmico e, em seguida, iniciar um backup imediatamente ou criar uma política para backups agendados.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar o backup de um volume espelhado dinâmico

1. Clique no ícone do Gerenciador de instantâneo StorSimple na área de trabalho. A janela Gerenciador de instantâneo StorSimple aparece. 

2. No painel de **escopo** , o nó de **Volumes** de atalho e selecione **Examinar volumes**. Quando a verificação for concluída, uma lista de volumes deverão aparecer no painel de **resultados** . O volume espelhado dinâmico está listado como um único volume. 

3. No painel de **resultados** , clique com botão direito no volume espelhado dinâmico e, em seguida, clique em **Criar grupo de Volume**. 

4. Na caixa de diálogo **Criar grupo de Volume** , digite um nome para o grupo de volume, atribua o volume espelhado dinâmico a esse grupo e clique em **Okey**. 

5. No painel de **escopo** , expanda o nó de **Grupos de Volume** . O novo grupo de volume deverá aparecer sob o nó **Grupos de Volume** . 

6. Clique com botão direito no nome do grupo de volume. 

    - Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 

    - Para agendar um backup automático, clique em **Criar diretiva de Backup**. Na página **Geral** , selecione o grupo de volume na lista. Na página **cronograma** , insira os detalhes do cronograma. Quando tiver terminado, clique em **Okey**. 

7. Você pode monitorar o trabalho de backup que seja executado. No painel de **escopo** , expanda o nó de **trabalhos** e depois clique **em execução**, os detalhes de trabalho são exibidos no painel de **resultados** . Quando o trabalho de backup for concluído, os detalhes são transferidos para a lista de trabalhos **últimas 24** horas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para criar e gerenciar grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
