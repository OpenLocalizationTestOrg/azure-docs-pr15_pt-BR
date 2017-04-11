<properties
   pageTitle="Implantar Array Virtual StorSimple - provisionar no Hyper-V"
   description="Este tutorial segundo na implantação de Array Virtual StorSimple envolve provisionamento de um dispositivo virtual no Hyper-V."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Implantar Array Virtual StorSimple - provisionar uma matriz Virtual no Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Visão geral

Este tutorial provisionamento se aplica à versão do Microsoft Azure StorSimple Virtual matrizes (também conhecido como dispositivos virtuais do StorSimple locais ou dispositivos virtuais StorSimple) em execução março de 2016 disponibilidade geral (GA). Este tutorial descreve como configurar uma matriz de Virtual StorSimple em um sistema de host executando Hyper-V no Windows Server 2012 R2, Windows Server 2008 R2 ou no Windows Server 2012. Este artigo se aplica à implantação de matrizes Virtual StorSimple no portal clássico Azure bem como nuvem de governo do Microsoft Azure.

Você precisa privilégios de administrador para provisionar e configurar um dispositivo virtual. A configuração de provisionamento e inicial pode levar cerca de 10 minutos para ser concluída.


## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento

Aqui você encontrará os pré-requisitos para provisionar um dispositivo virtual em um sistema de host executando Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gerenciador de StorSimple

Antes de começar, verifique se:

-   Você concluiu todas as etapas em [Prepare o portal para Array Virtual StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Você baixou a imagem de dispositivo virtual Hyper-v do portal do Azure. Para obter mais informações, consulte [etapa 3: baixar a imagem de dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] O software em execução no Array Virtual StorSimple só pode ser usado em conjunto com o serviço do Gerenciador de Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host executando Hyper-V no Windows Server 2008 R2 ou posterior que pode ser usado para um provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para configurar seu dispositivo virtual:

    -   Mínimo de 4 cores.

    -   Pelo menos 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para dados de sistema.

### <a name="for-the-network-in-the-datacenter"></a>Rede no data center

Antes de começar, examine os requisitos de rede para implantar um dispositivo virtual StorSimple e configurar a rede de data center apropriadamente. Para obter mais informações, consulte [requisitos de rede Array Virtual StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Passo a passo de provisionamento

Para provisionar e se conectar a um dispositivo virtual, você precisará executar as seguintes etapas:

1.  Certifique-se de que o sistema de host tem recursos suficientes para atender aos requisitos mínimos de dispositivo virtual.

2.  Provisione um dispositivo virtual no seu hipervisor.

3.  Inicie o dispositivo virtual e obter o endereço IP.

Cada uma dessas etapas será explicada nas seções a seguir.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Etapa 1: Certifique-se de que o sistema host atende aos requisitos de dispositivo virtual mínimo

Para criar um dispositivo virtual, será necessário:

-   A função Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.

-   Gerenciador de Hyper-V da Microsoft em um cliente Microsoft Windows conectado ao host.

Você deve se certificar de que o hardware subjacente (sistema de host) em que você está criando o dispositivo virtual é capaz de dedicar os seguintes recursos para o seu dispositivo virtual:

- Mínimo de 4 cores.
- Pelo menos 8 GB de RAM.
- Uma interface de rede.
- Um disco virtual 500 GB para dados de sistema.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Etapa 2: Configurar um dispositivo virtual no hipervisor

Execute as seguintes etapas para configurar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-device"></a>Configurar um dispositivo virtual

1.  Em seu host do Windows Server, copie a imagem de dispositivo virtual para uma unidade local. Esta é a imagem (VHD ou VHDX) que você baixou por meio do portal Azure. Anote o local onde você copiou a imagem como você usará isso posteriormente no procedimento.

2.  Abra o **Gerenciador de servidor**. No canto superior direito, clique em **Ferramentas** e selecione **Gerenciador Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Se você estiver executando o Windows Server 2008 R2, abra o Gerenciador de Hyper-V. No Gerenciador do servidor, clique em **funções > Hyper-V > Gerenciador Hyper-V**.

1.  No **Gerenciador Hyper-V**, no painel de escopo, o nó do seu sistema para abrir o menu de contexto de atalho e, em seguida, clique em **novo** > **Máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Na página **antes de começar** do Assistente de nova máquina Virtual, clique em **Avançar**.

1.  Na página **local e especificar o nome** , forneça um **nome** para seu dispositivo virtual. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Na página **especificar geração** , escolha o tipo de imagem do dispositivo e clique em **Avançar**. Esta página não aparece se você estiver usando o Windows Server 2008 R2.

    * Escolha **geração 2** se você baixou uma imagem de .vhdx para o Windows Server 2012 ou posterior.
    * Escolha **geração 1** se você baixou uma imagem de VHD para o Windows Server 2008 R2 ou posterior.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Na página **atribuir memória** , especifique uma **memória de inicialização** pelo menos **MB 8192**, não habilitar memória dinâmica e clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Na página **Configurar rede** , especifique a opção virtual que está conectada à Internet e clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Na página **Conectar do disco rígido virtual** , escolha **usar um disco rígido virtual existente**, especifique o local da imagem do dispositivo virtual (.vhdx ou. vhd) e clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Revise o **Resumo** e clique em **Concluir** para criar a máquina virtual. Mas não pular ainda - você ainda precisa adicionar algumas cores de CPU e uma segunda unidade. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Para atender aos requisitos mínimos, você precisará 4 cores. Para adicionar processadores virtuais, com seu sistema de host selecionado na janela **Gerenciador Hyper-V** , no painel direito em lista de **máquinas virtuais**, localize a máquina virtual que você acabou de criar. Selecione o nome da máquina de atalho e selecione **configurações**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Na página **configurações** , no painel esquerdo, clique em **processador**. No painel à direita, defina o **número de processadores virtuais** 4 (ou mais). Clique em **Aplicar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Para atender aos requisitos mínimos, você também precisa adicionar um disco de dados virtual de 500 GB. Na página de **configurações** :

    1.  No painel esquerdo, selecione **Controlador SCSI**.
    2.  No painel direito, selecione a **Unidade de disco** e clique em **Adicionar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Na página do **disco rígido** , selecione a opção **Virtual disco rígido** e clique em **novo**. Isso inicia o **Assistente de disco rígido novo Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Na página **antes de começar** do novo assistente disco rígido Virtual, clique em **Avançar**.

1.  Na **página Escolher o formato de disco**, aceite a opção padrão de formato **VHDX** . Clique em **Avançar**. Se você estiver executando o Windows Server 2012 R2 ou Windows Server 2008 R2, você não verá essa tela.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Na **página Escolher tipo de disco**, definir o tipo de disco rígido virtual como **expansível dinamicamente** (recomendado). Se você escolher o disco de **tamanho fixo** , ele também funcionará, mas você talvez precise esperar muito tempo. Recomendamos que você não use a opção **Differencing** . Clique em **Avançar**. Observe que **expansível dinamicamente** é o padrão no Windows Server 2012 R2 e Windows Server 2012. No Windows Server 2008 R2, o padrão é de **tamanho fixo**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Na página **Especificar nome e local** , forneça um **nome** , bem como **local** (você pode navegar para um) para o disco de dados. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Na página **Configurar disco** , selecione a opção **criar um novo disco de rígido virtual em branco** e especifique o tamanho como **500 GB** (ou mais). Enquanto 500 GB é o requisito mínimo, você sempre pode provisionar um disco maior. Observe que você não pode expandir ou reduzir o disco provisionado uma vez. Para obter mais informações sobre o tamanho de disco para provisionar, examine a seção de dimensionamento no documento [práticas recomendadas](storsimple-ova-best-practices.md#configuration-best-practices) . Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Na página **Resumo** , examine os detalhes do seu disco de dados virtual e se satisfeito, clique em **Concluir** para criar o disco. O assistente será fechado e um disco rígido virtual será adicionado à sua máquina.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Você vai retornar para a página de **configurações** . Clique em **Okey** para fechar a página de **configurações** e retornar à janela do Gerenciador de Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Etapa 3: Inicie o dispositivo virtual e obter o IP

Execute as seguintes etapas para iniciar o dispositivo virtual e conectá-lo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Depois que o dispositivo está executando, selecione o dispositivo, clique com botão direito e selecione **Conectar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Você pode ter que esperar 5 a 10 minutos para que o dispositivo esteja pronto. Será exibida uma mensagem de status no console para indicar o andamento. Quando o dispositivo está pronto, vá para **ação**. Pressione `Ctrl + Alt + Delete` de login o dispositivo virtual. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Senha1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Por razões de segurança, a senha de administrador do dispositivo expira o log de primeiro em. Você será solicitado a alterar a senha.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Digite uma senha que contém pelo menos 8 caracteres. A senha deve atender a pelo menos 3 fora os seguintes 4 requisitos: caracteres em maiusculas, minúsculas, numérico e especial. Redigite a senha para confirmá-la. Você será notificado de que a senha foi alterado.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Após a senha é alterada com êxito, o dispositivo virtual poderá reiniciar. Aguarde até que o dispositivo para iniciar.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    Console do Windows PowerShell do dispositivo será exibido juntamente com uma barra de progresso.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  As etapas 6-8 se aplicam somente ao inicializar o em um ambiente não DHCP. Se você estiver em um ambiente de DHCP, pule essas etapas e vá para a etapa 9. Se você inicializado o seu dispositivo no ambiente de DHCP não, você verá a tela a seguir.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Agora, você precisará configurar a rede.

1.  Use o `Get-HcsIpAddress` comando para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se seu dispositivo tem uma interface de rede ativada, o nome padrão atribuído a esta interface é `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Use o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é mostrado abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Após a configuração inicial for concluída e o dispositivo foi inicializado para cima, você verá o texto do banner de dispositivo. Tome nota do endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar na interface do usuário do seu dispositivo virtual Web e concluir a configuração de local e o registro.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Opcional) Execute esta etapa somente se você estiver implantando seu dispositivo na nuvem governamentais. Agora, você ativará o modo de Estados Unidos Federal Information Processing Standard (FIPS) em seu dispositivo. O padrão FIPS 140 define algoritmos aprovados para uso pelo Federal nos sistemas de computador do governo para a proteção de dados confidenciais.
    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:

        `Enter-HcsFIPSMode`

    2. Reinicie o dispositivo depois que você tiver habilitado o modo FIPS para que as validações de criptografia entrem em vigor.

        > [AZURE.NOTE] Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Alternando o dispositivo entre o modo FIPS e não FIPS não é suportada.

Se seu dispositivo não atender os requisitos mínimos de configuração, você verá um erro no texto do banner (mostrado abaixo). Você precisa modificar a configuração de dispositivo de modo que ele tenha recursos adequados para atender aos requisitos mínimos. Você pode, em seguida, reinicie e conectar ao dispositivo. Consulte os requisitos mínimos de configuração em [etapa 1: Certifique-se de que o sistema host atende aos requisitos de dispositivo virtual mínimos](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Se você enfrentar qualquer outro erro durante a configuração inicial usando o interface do usuário de web local, consulte os seguintes fluxos de trabalho em [Gerenciar seu Array Virtual StorSimple usando o interface do usuário de web local](storsimple-ova-web-ui-admin.md).

-   Execute testes de diagnóstico para [Solucionar problemas de configuração de interface do usuário da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Pacote de log de gerar e exibir os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![ícone de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **vídeo disponível**

Assista ao vídeo para ver como você pode provisionar uma matriz Virtual StorSimple no Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Próximas etapas

-   [Configurar sua matriz Virtual StorSimple como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar sua matriz Virtual StorSimple como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
