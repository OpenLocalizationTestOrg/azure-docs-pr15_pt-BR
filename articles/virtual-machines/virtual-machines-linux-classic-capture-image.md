<properties
    pageTitle="Capturar uma imagem de uma VM Linux | Microsoft Azure"
    description="Saiba como capturar uma imagem de uma baseados em Linux Azure máquina virtual (VM) criada com o modelo de implantação clássico."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Como capturar clássico computador virtual Linux como uma imagem

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](virtual-machines-linux-capture-image.md).

Este artigo mostra como capturar uma máquina virtual Azure clássica executando Linux como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco de sistema operacional e discos de dados conectados à máquina virtual. Ele não inclui a configuração de rede, então você precisa configurar que quando você cria as outras máquinas virtuais da imagem.

Azure armazena a imagem em **imagens**, juntamente com quaisquer imagens que você carregou. Para obter mais informações sobre imagens, consulte [Sobre imagens de máquinas virtuais no Azure] [].

## <a name="before-you-begin"></a>Antes de começar

Estas etapas pressupõem que você já criou uma máquina virtual Azure usando o modelo de implantação do clássico e configurado o sistema operacional, incluindo como anexar qualquer disco de dados. Se você precisa criar uma máquina virtual, leia [como criar uma máquina Virtual Linux] [].


## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual

1. [Conectar-se à máquina virtual](virtual-machines-linux-mac-create-ssh-keys.md) usando um cliente SSH de sua escolha.

2. Na janela SSH, digite o seguinte comando. A saída do `waagent` pode variar um pouco dependendo da versão deste utilitário:

    `sudo waagent -deprovision+user`

    Esse comando tenta limpar o sistema e torná-lo adequado para reprovisionamento. Essa operação executa as seguintes tarefas:

    - Remove teclas de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
    - Limpa a configuração do servidor de nomes no /etc/resolv.conf
    - Remove o `root` senha de usuário de/etc/sombra (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
    - Remove cache concessões de cliente DHCP
    - Nome de host redefine para localdomain
    - Exclui o último usuário provisionado conta (obtida /var/lib/waagent) **e dados associados**.

    >[AZURE.NOTE] Desprovisionamento exclui arquivos e dados para "generalizar" a imagem. Somente execute este comando em uma máquina virtual que você pretende capturar como um novo modelo de imagem. Ele não garante que a imagem está desmarcada de todas as informações confidenciais ou é adequada para redistribuição para terceiros.


3. Digite **y** para continuar. Você pode adicionar o `-force` parâmetro para evitar essa etapa de confirmação.

4. Digite **Sair** para fechar o cliente SSH.

    >[AZURE.NOTE] As etapas restantes presumem que você já tiver [instalado o CLI Azure](../xplat-cli-install.md) no computador cliente. Todas as seguintes etapas também podem ser feitas no [Azure portal clássico] [].

5. Em seu computador cliente, abra CLI do Azure e faça logon à sua assinatura do Azure. Para obter detalhes, leia [conectar uma assinatura do Azure da CLI Azure](../xplat-cli-connect.md).

6. Verifique se que você está no modo de gerenciamento de serviço:

    `azure config mode asm`

7. Desligue a máquina virtual que já será desprovisionada nas etapas anteriores com:

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Você pode descobrir todas as máquinas virtuais criadas na sua assinatura usando`azure vm list`

8. Quando a máquina virtual é interrompida, capture a imagem com o comando:

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Digite o nome de imagem que você deseja no lugar de _novo nome de imagem_. Esse comando cria uma imagem de SO generalizada. O `-t` subcomando exclui da máquina virtual original.

9.  A nova imagem agora está disponível na lista de imagens que podem ser usadas para configurar quaisquer novas máquinas virtuais. Você pode exibi-lo com o comando:

    `azure vm image list`

    No [portal de clássico Azure] [], ele aparece na lista de **imagens** .

    ![Captura de imagem bem-sucedida](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Próximas etapas
A imagem está pronta para ser usada para criar máquinas virtuais. Você pode usar o comando CLI Azure `azure vm create` e forneça o nome da imagem que você criou. Consulte [utilizando a CLI Azure com o modelo de implantação do clássico](../virtual-machines-command-line-tools.md) para obter detalhes sobre o comando. Como alternativa, use o [Azure portal clássico] [] para criar uma máquina virtual personalizada usando o método **Da Galeria** e selecionando a imagem que você criou. Veja [como criar uma máquina Virtual de personalizada] [] para obter mais detalhes.

**Consulte também:** [Guia de usuário do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)

[Azure portal clássico]: http://manage.windowsazure.com
[Sobre imagens de máquina Virtual no Azure]: virtual-machines-linux-classic-about-images.md
[Como criar uma máquina Virtual personalizada]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Como criar uma máquina Virtual Linux]: virtual-machines-linux-classic-create-custom.md
