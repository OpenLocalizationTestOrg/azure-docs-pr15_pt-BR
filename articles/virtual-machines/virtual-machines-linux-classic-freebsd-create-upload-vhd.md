<properties
   pageTitle="Criar e carregar uma imagem de FreeBSD VM | Microsoft Azure"
   description="Aprenda a criar e carregar um disco rígido virtual (VHD) que contém o sistema operacional FreeBSD para criar uma máquina virtual Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD FreeBSD no Azure

Este artigo mostra como criar e carregar um disco rígido virtual (VHD) que contém o sistema operacional FreeBSD. Depois de você carregá-lo, você pode usá-lo como sua própria imagem para criar uma máquina virtual (VM) no Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Assinatura de um Azure**– se você não tiver uma conta, você pode criar uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, consulte [crédito Azure mensal para os assinantes do Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  

- **Ferramentas do PowerShell do azure**– o Azure PowerShell módulo deve ser instalado e configurado para usar sua assinatura do Azure. Para baixar o módulo, consulte [downloads do Azure](https://azure.microsoft.com/downloads/). Um tutorial que descreve como instalar e configurar o módulo está disponível aqui. Use o cmdlet [Downloads do Azure](https://azure.microsoft.com/downloads/) para carregar o VHD.

- **FreeBSD operacional instalado em um arquivo. vhd**-- um suporte FreeBSD sistema operacional deve ser instalado em um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd. Por exemplo, você pode usar uma solução de virtualização como Hyper-V para criar o arquivo. vhd e instalar o sistema operacional. Para obter instruções sobre como instalar e usar Hyper-V, consulte [instalar Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato mais recente do VHDX no Azure. Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet [vhd convert](https://technet.microsoft.com/library/hh848454.aspx). Além disso, há um [tutorial no MSDN sobre como usar FreeBSD com Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Essa tarefa inclui as cinco etapas a seguintes.

## <a name="step-1-prepare-the-image-for-upload"></a>Etapa 1: Preparar a imagem para carregamento

Na máquina virtual onde você instalou o sistema operacional FreeBSD, conclua os procedimentos a seguir:

1. Ativar DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Ativar o SSH.

    SSH está ativado por padrão após a instalação de disco. Se ele não está habilitado por algum motivo, ou se você usar FreeBSD VHD diretamente, digite o seguinte:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Configure um console serial.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Instale sudo.

    A conta raiz está desativada no Azure. Isso significa que você precisa utilizar sudo de um usuário sem privilégios para executar comandos com privilégios elevados.

        # pkg install sudo
;
5. Pré-requisitos para agente Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Instale o agente do Azure.

    A versão mais recente do agente do Azure sempre pode ser encontrada no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente suporta FreeBSD 10 e 10.1 e a versão 2.1.4 suporta oficialmente FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Para 2.0, vamos usar 2.0.16 como exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Para 2.1, vamos usar 2.1.4 como exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Depois de instalar o agente do Azure, é recomendável verificar se ele está funcionando:

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Desprovisione do sistema.

    Desprovisione o sistema para limpar e torná-lo adequado para provisionamento novamente. O comando a seguir também exclui a última conta de usuário provisionado e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora você pode desligar sua máquina virtual.

## <a name="step-2-create-a-storage-account-in-azure"></a>Etapa 2: Criar uma conta de armazenamento no Azure ##

Você precisa de uma conta de armazenamento no Azure carregar um arquivo. vhd que pode ser usado para criar uma máquina virtual. Você pode usar o portal de clássico Azure para criar uma conta de armazenamento.

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com).

2. Na barra de comando, selecione **novo**.

3. Selecione **Serviços de dados** > **armazenamento** > **criação rápida**.

    ![Criar uma conta de armazenamento a rápida](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:

    - No campo **URL** , digite um nome de subdomínio para usar a URL da conta de armazenamento. A entrada pode conter de 3-24 números e letras minúsculas. Esse nome torna-se o nome de host dentro a URL que é usado para tratar de armazenamento de Blob do Azure, armazenamento de fila do Azure ou recursos de armazenamento de tabela do Azure para a assinatura.

    - No menu suspenso **Grupo local/afinidade** , escolha o **local ou grupo de afinidade** para a conta de armazenamento. Um grupo de afinidade permite colocar seus serviços de nuvem e armazenamento no mesmo data center.

    - No campo de **replicação** , decida se quer usar replicação **Geográfica redundante** para a conta de armazenamento. Replicação geográfica fica ativada por padrão. Essa opção replica seus dados para um local secundário, sem nenhum custo para você, para que seu armazenamento não conseguir sobre esse local se ocorrer uma falha principal no local principal. O local secundário é atribuído automaticamente e não pode ser alterado. Se precisar de mais controle sobre o local do seu armazenamento baseado em nuvem devido a requisitos legais ou organizacional política, você pode desativar replicação geográfica. No entanto, lembre-se de que se você ativar mais tarde replicação geográfica, você será cobrado uma taxa de transferência de dados de uso único para duplicar dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Mais detalhes sobre o gerenciamento de replicação geográfica das contas de armazenamento podem ser encontradas aqui: [criar, gerenciar, ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

    ![Insira os detalhes da conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Selecione **Criar conta de armazenamento**. A conta agora aparece em **armazenamento**.

    ![Conta de armazenamento criada com êxito](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Em seguida, crie um contêiner para seus arquivos. vhd carregados. Selecione o nome da conta de armazenamento e selecione **contêineres**.

    ![Detalhes da conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Selecione **criar um contêiner**.

    ![Detalhes da conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. No campo **nome** , digite um nome para seu contêiner. Em seguida, no menu suspenso do **Access** , selecione tipo de política de acesso que você deseja.

    ![Nome de contêiner](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Por padrão, o contêiner é particular e só pode ser acessado pelo proprietário da conta. Para permitir o acesso de leitura público para os blobs no contêiner, mas não para as propriedades de contêiner e metadados, use a opção de **Blob pública** . Para permitir público completa acesso de leitura para o contêiner e blobs, use a opção **Container público** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Etapa 3: Preparar a conexão para o Azure

Antes de você pode carregar um arquivo. vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. Você pode usar o método do Azure Active Directory (AD Azure) ou o método de certificado para fazer isso.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Use o método Azure AD para carregar um arquivo. vhd

1. Abra o console do PowerShell do Azure.

2. Digite o seguinte comando:  
    `Add-AzureAccount`

    Esse comando abre uma janela de entrada onde você pode entrar com sua conta corporativa ou escolar.

    ![Janela do PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure autentica e salva as informações de credenciais. Fecha a janela.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Use o método de certificado para carregar um arquivo. vhd

1. Abra o console do PowerShell do Azure.

2. Tipo:  `Get-AzurePublishSettingsFile`.

3. Uma janela do navegador é aberto e solicitará que você baixar um arquivo. publishsettings. Este arquivo contém informações e um certificado para sua assinatura do Azure.

    ![Página de download do navegador](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Salve o arquivo. publishsettings.

4. Tipo:  `Import-AzurePublishSettingsFile <PathToFile>`, onde `<PathToFile>` é o caminho completo para o arquivo. publishsettings.

   Para obter mais informações, consulte [Introdução ao Azure cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para obter mais informações sobre instalação e configuração do PowerShell, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Etapa 4: Carregar o arquivo. vhd

Quando você carregar o arquivo. vhd, você pode colocá-lo em qualquer lugar dentro seu armazenamento de Blob. A seguir estão alguns termos que você usará quando você carrega o arquivo:
-  **BlobStorageURL** é a URL para a conta de armazenamento que você criou na etapa 2.
-  **YourImagesFolder** é o recipiente em armazenamento de Blob onde você deseja armazenar as imagens.
- **VHDName** é o rótulo que aparece no portal de clássico do Azure para identificar o disco rígido virtual.
- **PathToVHDFile** é o caminho completo e o nome do arquivo. vhd.


Na janela do PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Etapa 5: Criar uma máquina virtual com o arquivo. vhd carregados
Depois de carregar o arquivo. vhd, você pode adicioná-lo como uma imagem à lista de imagens personalizadas que estão associados a sua assinatura e criar uma máquina virtual com esta imagem personalizada.

1. Na janela do PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Use Linux como o tipo de sistema operacional. A versão atual do Azure PowerShell aceita somente "Linux" ou "Windows" como um parâmetro.

2. Depois de concluir as etapas anteriores, a nova imagem está listada ao escolher na guia **imagens** no portal de clássico do Azure.  

    ![Escolher uma imagem](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Crie uma máquina virtual da Galeria. A nova imagem agora está disponível em **Minhas imagens**.
4. Selecione a nova imagem. Em seguida, percorrer os avisos para configurar um nome de host, senha, chave SSH e assim por diante.

    ![Imagem personalizada](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Depois de concluir o processo de provisionamento, você verá sua FreeBSD VM em execução no Azure.

    ![Imagem de FreeBSD no azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
