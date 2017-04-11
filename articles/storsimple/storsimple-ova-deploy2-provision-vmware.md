<properties
   pageTitle="Implantar Array Virtual StorSimple - provisionar em VMware"
   description="Este tutorial segunda série de implantação de Array Virtual StorSimple envolve um dispositivo virtual em VMware de provisionamento."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Implantar Array Virtual StorSimple - provisionar uma matriz Virtual em VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Visão geral 
Este tutorial provisionamento se aplica ao lançamento de disponibilidade geral (GA) de março de 2016 em execução StorSimple matrizes Virtual (também conhecido como dispositivos virtuais do StorSimple locais ou dispositivos virtuais StorSimple). Este tutorial descreve como provisionar e se conecte a uma matriz de Virtual StorSimple em um sistema de host executando VMware ESXi 5.5 e acima. Este artigo se aplica à implantação de matrizes Virtual StorSimple no portal clássico Azure bem como nuvem de governo do Microsoft Azure.

Você precisa privilégios de administrador para provisionar e se conectar a um dispositivo virtual. A configuração de provisionamento e inicial pode levar cerca de 10 minutos para ser concluída.


## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento

Aqui você encontrará os pré-requisitos para provisionar um dispositivo virtual em um sistema de host executando VMware ESXi 5.5 e acima.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gerenciador de StorSimple

Antes de começar, verifique se:

-   Você concluiu todas as etapas em [Prepare o portal para Array Virtual StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Você baixou a imagem de dispositivo virtual para VMware do portal do Azure. Para obter mais informações, consulte [etapa 3: baixar a imagem de dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple 

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host executando Hyper-V (2008 R2 ou posterior) que pode ser usado para um provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para configurar seu dispositivo virtual:

    -   Mínimo de 4 cores.

    -   Pelo menos 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para dados de sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede em data center 

Antes de começar, verifique se:

-   Você tiver revisado os requisitos de rede para implantar um dispositivo virtual StorSimple e configurado a rede de data center de acordo com os requisitos. Para obter mais informações, consulte [requisitos do sistema de Array Virtual StorSimple](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Passo a passo de provisionamento 

Para provisionar e se conectar a um dispositivo virtual, você precisará executar as seguintes etapas:

1.  Certifique-se de que o sistema de host tem recursos suficientes para atender aos requisitos mínimos de dispositivo virtual.

2.  Provisione um dispositivo virtual no seu hipervisor.

3.  Inicie o dispositivo virtual e obter o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Etapa 1: Verificar sistema host atende aos requisitos de dispositivo virtual mínimo

Para criar um dispositivo virtual, será necessário:

-   Acesso a um sistema de host executando VMware ESXi Server 5.5 e acima.

-   VMware vSphere cliente em seu sistema para gerenciar o host ESXi.

    -   Mínimo de 4 cores.

    -   Pelo menos 8 GB de RAM.

    -   Uma interface de rede conectado à rede capaz de rotear o tráfego de Internet. A largura de banda de Internet mínima deve ser 5 Mbps para permitir o trabalho ideal do dispositivo.

    -   Um disco virtual 500 GB para os dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Etapa 2: Configurar um dispositivo virtual no hipervisor

Execute as seguintes etapas para configurar um dispositivo virtual no seu hipervisor.

1.  Copie a imagem de dispositivo virtual no seu sistema. Esta é a imagem que você baixou por meio do portal de clássico Azure. 
    1.  Certifique-se de que este é o mais recente arquivo de imagem que você baixou. Se você baixou anteriormente a imagem, baixe-o novamente para garantir que você tenha a imagem mais recente. A imagem mais recente tem dois arquivos (em vez de um).
    2.  Anote o local onde você copiou a imagem como você usará isso posteriormente no procedimento.

2.  Faça logon no servidor ESXi usando o cliente vSphere. Você precisará ter privilégios de administrador para criar uma máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  No cliente vSphere, na seção de estoque no painel esquerdo, selecione o servidor ESXi.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Primeiro você carregará a VMDK no servidor ESXi. Navegue até a guia **configuração** no painel direito. Em **Hardware**, selecione **armazenamento**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  No painel à direita, em **armazenamentos de dados**, selecione o armazenamento de dados onde você deseja carregar o VMDK. O armazenamento de dados deve ter espaço livre suficiente para o sistema operacional e dados discos.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Clique com botão direito e selecione **Procurar armazenamento de dados**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Uma janela do **Navegador de armazenamento de dados** será exibida.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Na barra de ferramentas, clique em ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) ícone para criar uma nova pasta. Especifique o nome da pasta e tome nota dele. Você usará posteriormente esse nome de pasta ao criar uma máquina virtual (recomendada prática recomendada). Clique em **Okey**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  A nova pasta aparecerá no painel esquerdo do **Navegador de armazenamento de dados**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Clique no ícone de carregamento ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) e selecione **Carregar arquivo**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Agora você deve procurar e apontar para os arquivos VMDK que você baixou. Haverá dois arquivos. Selecione um arquivo para carregar.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Clique em **Abrir**. Isso iniciará agora o carregamento do arquivo VMDK para o armazenamento de dados especificado. Ele pode levar alguns minutos carregar o arquivo.


1.  Após o carregamento for concluído, você verá o arquivo do armazenamento de dados na pasta que você criou. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Agora, você precisará carregar o segundo arquivo VMDK no mesmo armazenamento de dados.

1.  Retornar à janela de cliente vSphere. Com o servidor de ESXi selecionado, clique com botão direito e selecione **Nova Máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Uma janela **Criar nova máquina Virtual** será exibida. Na página **configuração** , selecione a opção **personalizado** . Clique em **Avançar**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Na página **nome e local** , especifique o nome da sua máquina virtual. Esse nome deve corresponder o nome da pasta (prática recomendada) que você especificou anteriormente na etapa 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Na página de **armazenamento** , selecione um armazenamento de dados que você deseja usar para provisionar sua máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Na página da **Versão de máquina Virtual** , selecione **Máquina Virtual versão: 8**. Observe que as versões de 8 a 11 há suporte para.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Na página do **sistema operacional convidado** , selecione o **sistema operacional convidado** como **Windows**. Para **versão**, na lista suspensa, selecione **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Na página **CPUs** , ajuste o **número de sockets virtuais** e o **número de cores por soquete virtual** para que o **Número Total de cores** é 4 (ou mais). Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Na página de **memória** , especifique 8 GB (ou mais) de RAM. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Na página de **rede** , especifique o número de interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Na página **Controlador SCSI** , aceite o padrão **controlador LSI lógica SAS**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Na página **Selecionar um disco** , escolha **usar um disco virtual existente**. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Na página **Selecionar disco existente** , em **Caminho do arquivo de disco**, clique em **Procurar**. Isso abre uma caixa de diálogo **Procurar armazenamentos de dados** . Navegue até o local onde você carregou o VMDK. Agora você verá apenas um arquivo do armazenamento de dados como os dois arquivos que você carregou inicialmente foram mesclados. Selecione o arquivo e clique em **Okey**. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Na página **Opções avançadas** , aceite o padrão e clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Na página **pronto para concluído** , revise todas as configurações associadas a nova máquina virtual. Marque a opção **Editar configurações de máquina virtual antes da conclusão**. Clique em **continuar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Na página **Propriedades de máquinas virtuais** , na guia **Hardware** , localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Isto traz a janela **Adicionar Hardware** . Na página **Tipo de dispositivo** , em **Escolha o tipo de dispositivo que você deseja adicionar**, selecione o **disco rígido** e clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Na página **Selecionar um disco** , escolha **criar um novo disco virtual**. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Na página **criar um disco** , altere o **Tamanho de disco** para 500 GB (ou mais). Em **Disco de provisionamento**, selecione **Provisionar fina**. Clique em **Avançar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Na página **Opções avançadas** , aceite o padrão.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Na página **pronto para concluída** , examine as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Agora, você voltará para a página de propriedades de máquina Virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Com sua máquina virtual selecionada no painel direito, navegue até a guia **Resumo** . Examine as configurações de sua máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Sua máquina virtual agora está provisionada. A próxima etapa é power neste computador e obter o endereço IP.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Etapa 3: Inicie o dispositivo virtual e obter o IP

Execute as seguintes etapas para iniciar o dispositivo virtual e conectá-lo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual. No Gerenciador de configuração, o vSphere no painel esquerdo, selecione seu dispositivo e com o botão direito para exibir o menu de contexto. Selecione **Power** e selecione **ligar**. Isso deve ser ligado sua máquina virtual. Você pode exibir o status no painel de **Tarefas recentes** inferior do cliente vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  As tarefas de configuração levará alguns minutos para ser concluída. Depois que o dispositivo está executando, navegue até a guia **Console** . Envie Ctrl + Alt + Delete para fazer logon no dispositivo. Como alternativa, você pode aponte o cursor na janela do console e pressione Ctrl + Alt + Insert. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Senha1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Por razões de segurança, a senha de administrador do dispositivo expira o log de primeiro em. Você será solicitado a alterar a senha.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Digite uma senha que contém pelo menos 8 caracteres. A senha deve conter 3 dos 4 esses requisitos: caracteres em maiusculas, minúsculas, numérico e especial. Redigite a senha para confirmá-la. Você será notificado de que a senha foi alterado.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Depois que a senha é alterada com êxito, o dispositivo virtual pode reinicializar. Aguarde até que a reinicialização seja concluída. Console do Windows PowerShell do dispositivo pode ser exibido juntamente com uma barra de progresso.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  As etapas 6-8 se aplicam somente ao inicializar o em um ambiente não DHCP. Se você estiver em um ambiente de DHCP, pule essas etapas e vá para a etapa 9. Se você inicializado o seu dispositivo no ambiente de DHCP não, você verá a tela a seguir. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Agora, você precisará configurar a rede.

1.  Use o `Get-HcsIpAddress` comando para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se seu dispositivo tem uma interface de rede ativada, o nome padrão atribuído a esta interface será `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Use o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é mostrado abaixo:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Após a configuração inicial for concluída e o dispositivo foi inicializado para cima, você verá o texto do banner de dispositivo. Tome nota do endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar na interface do usuário do seu dispositivo virtual Web e concluir a configuração de local e o registro.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Opcional) Execute esta etapa somente se você estiver implantando seu dispositivo na nuvem governamentais. Agora, você ativará o modo de Estados Unidos Federal Information Processing Standard (FIPS) em seu dispositivo. O padrão FIPS 140 define algoritmos aprovados para uso pelo Federal nos sistemas de computador do governo para a proteção de dados confidenciais.
    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:
        
        `Enter-HcsFIPSMode`

    2. Reinicie o dispositivo depois que você tiver habilitado o modo FIPS para que as validações de criptografia entrem em vigor.

        > [AZURE.NOTE] Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Alternando o dispositivo entre o modo FIPS e não FIPS não é suportada.


Se seu dispositivo não atender os requisitos mínimos de configuração, você verá um erro no texto do banner (mostrado abaixo). Você precisa modificar a configuração de dispositivo de modo que ele tenha recursos adequados para atender aos requisitos mínimos. Você pode, em seguida, reinicie e conectar ao dispositivo. Consulte os requisitos mínimos de configuração em [etapa 1: Certifique-se de que o sistema host atende aos requisitos de dispositivo virtual mínimos](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Se você enfrentar qualquer outro erro durante a configuração inicial usando o interface do usuário de web local, consulte os seguintes fluxos de trabalho em [Gerenciar seu Array Virtual StorSimple usando o interface do usuário de web local](storsimple-ova-web-ui-admin.md).

-   Execute testes de diagnóstico para [Solucionar problemas de configuração de interface do usuário da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Pacote de log de gerar e exibir os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package)..

## <a name="next-steps"></a>Próximas etapas

-   [Configurar sua matriz Virtual StorSimple como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar sua matriz Virtual StorSimple como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

