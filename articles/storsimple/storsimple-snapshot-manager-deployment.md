<properties 
   pageTitle="Implantar o Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Saiba como baixar e instalar o Gerenciador de instantâneo StorSimple, um snap-in MMC de gerenciamento de recursos de backup e proteção de dados de StorSimple."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implantar o snap-in do Gerenciador de instantâneo StorSimple MMC

## <a name="overview"></a>Visão geral

O Gerenciador de instantâneo StorSimple é um snap-in do Console de gerenciamento da Microsoft (MMC) que simplifica a proteção de dados e gerenciamento de backup em um ambiente do Microsoft Azure StorSimple. Com o Gerenciador de instantâneo de StorSimple, você pode gerenciar o Microsoft Azure StorSimple locais e armazenamento em nuvem como se fosse um sistema de armazenamento totalmente integrado, portanto, simplificando processos de backup e restauração e reduzindo custos. 

Este tutorial descreve requisitos de configuração, além de procedimentos para instalar, remover e atualizar o Gerenciador de instantâneo StorSimple.

>[AZURE.NOTE] 
>
>- Você não pode usar o Gerenciador de instantâneo StorSimple para gerenciar o Microsoft Azure StorSimple Virtual matrizes (também conhecido como StorSimple local dispositivos virtuais).
>
>- Se você planeja instalar StorSimple atualização 2 no seu dispositivo StorSimple, certifique-se de baixar a versão mais recente do Gerenciador de instantâneo StorSimple e instalá-lo **antes de instalar StorSimple atualização 2**. A versão mais recente do Gerenciador de instantâneo StorSimple é compatível e funciona com todas as versões do Microsoft Azure StorSimple. Se você estiver usando a versão anterior do Gerenciador de instantâneo StorSimple, você precisará atualizá-lo (você não precisa desinstalar a versão anterior antes de instalar a nova versão).

## <a name="storsimple-snapshot-manager-installation"></a>Gerenciador de instantâneo StorSimple instalação

Gerenciador de instantâneo StorSimple pode ser instalado em computadores que executam o Windows Server 2008 R2 SP1, Windows Server 2012 ou sistema operacional Windows Server 2012 R2. Em servidores executando o Windows 2008 R2, você deve instalar também o Windows Server 2008 SP1 e o Windows Management Framework 3.0. 

Antes de instalar ou atualizar o snap-in do Gerenciador de instantâneo StorSimple Console de gerenciamento Microsoft (MMC), certifique-se de que o servidor de dispositivo e host do Microsoft Azure StorSimple estão configurados corretamente. 

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

As etapas a seguir fornecem uma visão geral das tarefas de configuração que deve ser concluída antes de instalar o Gerenciador de instantâneo StorSimple. Para concluir a configuração StorSimple do Microsoft Azure e informações de instalação, incluindo os requisitos de sistema e instruções passo a passo, consulte [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Antes de começar, examine a [lista de verificação de configuração de implantação](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) e e [pré-requisitos de implantação](storsimple-deployment-walkthrough.md#deployment-prerequisites) em [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar o Gerenciador de instantâneo StorSimple

1. Descompactar, montar e conectar o dispositivo do Microsoft Azure StorSimple conforme descrito em [instalar seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Certifique-se de que o computador host esteja executando um dos seguintes sistemas operacionais:

    - Windows Server 2008 R2 (em servidores que executam o Windows 2008 R2, você também deve instalar o Windows Server 2008 SP1 e Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Para um dispositivo virtual de StorSimple, o host deve ser um Microsoft Azure Virtual Machine. 

3. Certifique-se de que atender a todos os requisitos de configuração do Microsoft Azure StorSimple. Para obter detalhes, acesse [pré-requisitos de implantação](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Conectar o dispositivo para o host e executar a configuração inicial. Para obter detalhes, vá para [as etapas de implantação](storsimple-deployment-walkthrough.md#deployment-steps).

5. Criar volumes no dispositivo, atribuí-los para o host e verifique se o host pode montar e usá-los. Gerenciador de instantâneo StorSimple suporta os seguintes tipos de volumes: 

    - Volumes básicos
    - Volumes simples
    - Volumes dinâmicos
    - Volumes dinâmicos espelhados (RAID 1)
    - Volumes compartilhados de cluster
 
    Para obter informações sobre a criação de volumes no dispositivo StorSimple ou dispositivo virtual StorSimple, acesse [etapa 6: criar um volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume), em [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalar um novo Gerenciador de instantâneo StorSimple

Antes de instalar o Gerenciador de instantâneo StorSimple, certifique-se de que os volumes que você criou no StorSimple dispositivo ou StorSimple dispositivo virtual montado, inicializado e formatado conforme descrito em [Configurar pré-requisitos](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Para um dispositivo virtual de StorSimple, o host deve ser um Microsoft Azure Virtual Machine. 
>
>- O host deve estar executando Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Se o servidor está executando o Windows Server 2008 R2, você também deve instalar o Windows Server 2008 SP1 e Windows Management Framework 3.0.
>
>- Você deve configurar uma conexão iSCSI do host para o dispositivo de StorSimple antes de poder conectar o dispositivo ao Gerenciador de instantâneo StorSimple.

Siga estas etapas para concluir uma nova instalação do Gerenciador de instantâneo StorSimple. Se você estiver instalando uma atualização, vá para [atualizar ou reinstalar o Gerenciador de instantâneo StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Etapa 1: Instalar o Gerenciador de StorSimple instantâneo 
- Etapa 2: Conectar Gerenciador de instantâneo StorSimple para um dispositivo 
- Etapa 3: Confirmar a conexão para o dispositivo 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Etapa 1: Instalar o Gerenciador de StorSimple instantâneo

Use as seguintes etapas para instalar o Gerenciador de instantâneo StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar o Gerenciador de instantâneo StorSimple

1. Baixar o software de Gerenciador de instantâneo StorSimple (vá para [O Gerenciador de instantâneo StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) no Microsoft Download Center) e salvá-lo localmente no host.

2. No Explorador de arquivos, clique com botão direito na pasta compactada e clique em **Extrair tudo**.

3. Na janela de **pastas extrair compactadas (Zipped)** , na caixa **Selecione um destino e extrair arquivos** , digite ou navegue até o caminho onde você gostaria de arquivo a ser extraído. 

       >[AZURE.IMPORTANT] Você deve instalar o Gerenciador de instantâneo StorSimple na unidade c:.
 
4. Marque a caixa de seleção **Mostrar arquivos extraídos quando concluído** e clique em **extrair**.

    ![Caixa de diálogo de arquivos extrair](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Quando a extração estiver concluída, abre a pasta de destino. Clique duas vezes no ícone de instalação do aplicativo que aparece na pasta de destino.

5. Quando aparecer a mensagem **Configuração bem-sucedida** , clique em **Fechar**. Você deve ver o ícone do Gerenciador de instantâneo StorSimple na área de trabalho.

    ![ícone da área de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Etapa 2: Conectar Gerenciador de instantâneo StorSimple para um dispositivo

Use as seguintes etapas para conectar o Gerenciador de instantâneo StorSimple para um dispositivo de StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para conectar o Gerenciador de instantâneo StorSimple para um dispositivo

1. Clique no ícone do Gerenciador de instantâneo StorSimple na área de trabalho. A janela Gerenciador de instantâneo StorSimple aparece. A janela contém um painel de **escopo** , um painel de **resultados** e um painel de **ações** . 

    ![Interface de usuário do Gerenciador de instantâneo StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - O painel de **escopo** (painel esquerdo) contém uma lista de nós organizados em uma estrutura de árvore. Você pode expandir alguns nós para selecionar um modo de exibição ou dados específicos relacionados para esse nó. Clique no ícone de seta para expandir ou recolher um nó. Clique em um item no painel de **escopo** para ver uma lista de ações disponíveis para esse item. 

    - O painel de **resultados** (painel central) contém informações de status detalhadas sobre o nó, exibição ou dados que você selecionou no painel de **escopo** .

    - O painel de **ações** lista as operações que você pode executar no nó, exibição ou dados que você selecionou no painel de **escopo** .

    Para obter uma descrição completa da interface do usuário StorSimple Gerenciador de instantâneo, consulte [Gerenciador de instantâneo StorSimple interface de usuário](storsimple-use-snapshot-manager.md).

2. No painel de **escopo** , o nó de **dispositivos** de atalho e, em seguida, clique em **Configurar um dispositivo**. Caixa de diálogo **Configurar um dispositivo** é exibida.

    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Na caixa de listagem de **dispositivo** , selecione o endereço IP do Microsoft Azure StorSimple dispositivo ou dispositivo virtual. Na caixa de texto **senha** , digite a senha do Gerenciador de instantâneo StorSimple que você criou para o dispositivo no portal de clássico do Azure. Clique em **Okey**.

4. Gerenciador de instantâneo StorSimple procura o dispositivo que você identificou. Se o dispositivo estiver disponível, o Gerenciador de instantâneo StorSimple adiciona uma conexão. Você pode [Verificar a conexão com o dispositivo](#to-verify-the-connection) para confirmar que a conexão foi adicionada com êxito.

    Se o dispositivo não estiver disponível por algum motivo, o Gerenciador de instantâneo StorSimple retornará uma mensagem de erro. Clique **Okey** para fechar a mensagem de erro e clique em **Cancelar** para fechar a caixa de diálogo **Configurar um dispositivo** .

5. Quando ele se conecta para um dispositivo, o Gerenciador de instantâneo StorSimple importa cada grupo de volume configurado para esse dispositivo, desde que o grupo de volume associado backups. Grupos de volume que não têm backups associados não serão importados. Além disso, as políticas de backup que foram criadas para um grupo de volume não serão importadas. Para ver os grupos importados, clique com botão direito no nó de **Grupos de Volume** mais alto no painel de **escopo** e clique em **botão de alternância importados grupos**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Etapa 3: Confirmar a conexão para o dispositivo

Use as etapas a seguir para verificar se o Gerenciador de instantâneo StorSimple está conectado ao dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para verificar a conexão

1. No painel de **escopo** , clique no nó de **dispositivos** .

    ![Status do dispositivo de StorSimple Gerenciador de instantâneo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Verifique o painel de **resultados** : 

   - Se um indicador verde aparece no ícone do dispositivo e **disponível** aparece na coluna **Status** , o dispositivo está conectado. 

   - Se um indicador vermelho aparece no ícone do dispositivo e indisponível aparece na coluna **Status** , o dispositivo não está conectado. 

   - Se **a atualização** for exibida na coluna **Status** , Gerenciador de instantâneo StorSimple está recuperando grupos de volume e backups associados para um dispositivo conectado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Atualizar ou reinstalar StorSimple Gerenciador de instantâneo

Você deve desinstalar o Gerenciador de instantâneo StorSimple completamente antes de atualizar ou reinstalar o software. 

Antes de reinstalar o Gerenciador de instantâneo StorSimple, faça backup do banco de dados existente do Gerenciador de instantâneo StorSimple no computador host. Isso salva as informações de configuração e políticas de backup para que você possa facilmente restaurar esses dados de backup.

Se você estiver atualizando ou reinstalar o Gerenciador de instantâneo StorSimple, siga estas etapas:

- Etapa 1: Desinstalar o Gerenciador de instantâneo StorSimple 
- Etapa 2: Fazer backup de banco de dados StorSimple Gerenciador de instantâneo 
- Etapa 3: Reinstalar o Gerenciador de instantâneo StorSimple e restaurar o banco de dados 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Etapa 1: Desinstalar o Gerenciador de instantâneo StorSimple

Use as seguintes etapas para desinstalar o Gerenciador de instantâneo StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar o Gerenciador de instantâneo StorSimple

1. No computador host, abra o **Painel de controle**, clique em **programas**e, em seguida, clique em **programas e recursos**.

2. No painel esquerdo, clique em **desinstalar ou alterar um programa**.

3. **Gerenciador de instantâneo StorSimple**de atalho e, em seguida, clique em **desinstalar**.

4. Isso inicia o programa de instalação do Gerenciador de instantâneo StorSimple. Clique em **Modificar configuração**e clique em **desinstalar**.

    >[AZURE.NOTE] Se houver quaisquer processos MMC executado em segundo plano, como o Gerenciador de instantâneo StorSimple ou gerenciamento de disco, a desinstalação falhará e você receberá uma mensagem para fechar todas as instâncias do MMC antes de tentar desinstalar o programa. Selecione **Fechar automaticamente aplicativos e tentar reiniciá-los após a instalação**e clique em **Okey**.
 
5. Quando o processo de desinstalação for concluído, aparecerá uma mensagem **Configuração bem-sucedido** . Clique em **Fechar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Etapa 2: Fazer backup de banco de dados StorSimple Gerenciador de instantâneo

Use as etapas a seguir para criar e salvar uma cópia do banco de dados Gerenciador de instantâneo StorSimple.

#### <a name="to-back-up-the-database"></a>Para fazer backup do banco de dados

1. Pare o serviço de gerenciamento de StorSimple da Microsoft:

   1. Inicie o Gerenciador de servidor.

   2. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.

   3. Na página **Serviços** , selecione **Serviço de gerenciamento do Microsoft StorSimple**.

   4. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **Parar o serviço**.

        ![Interromper o serviço Gerenciador de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData é uma pasta oculta.

3. Localize o arquivo XML de catálogo, copie o arquivo e armazenar a cópia em um local seguro ou na nuvem.

    ![Arquivo de backup de catálogo de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Reinicie o serviço de gerenciamento de StorSimple da Microsoft: 

    1. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.

    2. Na página **Serviços** , selecione o **Serviço de gerenciamento do Microsoft StorSimple**e.

    3. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Etapa 3: Reinstale o Gerenciador de instantâneo StorSimple e restaurar o banco de dados

Para reinstalar o Gerenciador de instantâneo StorSimple, siga as etapas em [instalar um novo Gerenciador de instantâneo StorSimple](#install-a-new-storsimple-snapshot-manager). Em seguida, use o procedimento a seguir para restaurar o banco de dados do Gerenciador de instantâneo StorSimple.

#### <a name="to-restore-the-database"></a>Restaurar o banco de dados

1. Pare o serviço de gerenciamento de StorSimple da Microsoft:

    1. Inicie o Gerenciador de servidor.

    2. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.

    3. Na página **Serviços** , selecione **Serviço de gerenciamento do Microsoft StorSimple**.

    4. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **Parar o serviço**.

2. Navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData é uma pasta oculta.

3. Exclua o arquivo XML de catálogo e substituí-lo com a versão salva anteriormente.

4. Reinicie o serviço de gerenciamento de StorSimple da Microsoft: 

    1. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.

    2. Na página **Serviços** , selecione **Serviço de gerenciamento do Microsoft StorSimple**.

    3. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Gerenciador de instantâneo StorSimple, vá para [o que é o Gerenciador de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md).

- Para saber mais sobre a interface de usuário do Gerenciador de instantâneo StorSimple, vá para a [interface de usuário do Gerenciador de instantâneo StorSimple](storsimple-use-snapshot-manager.md).

- Para saber mais sobre como usar o Gerenciador de instantâneo StorSimple, vá para [Usar o Gerenciador de instantâneo de StorSimple para administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
