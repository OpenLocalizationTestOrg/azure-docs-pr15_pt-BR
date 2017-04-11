<properties
    pageTitle="Preparar Debian Linux VHD | Microsoft Azure"
    description="Aprenda a criar Debian 7 e 8 arquivos VHD para implantação no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD Debian do Azure

## <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha instalado um sistema operacional Linux Debian de um arquivo. ISO baixado do [site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como usar o Hyper-V, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Notas de instalação

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.
- Não há suporte para o formato mais recente do VHDX no Azure. Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet **vhd convert** .
- Ao instalar o sistema Linux é recomendável que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.
- Não configure uma partição de troca do disco do sistema operacional. O agente do Azure Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos. Mais informações sobre isso podem ser encontradas nas etapas abaixo.
- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Use a gerenciar o Azure criar VHDs Debian

Existem ferramentas disponíveis para gerar VHDs Debian do Azure, tais como o [azure-gerenciar](https://github.com/credativ/azure-manage) scripts de [credativ](http://www.credativ.com/). Essa é a abordagem recomendada versus criando uma imagem a partir do zero. Por exemplo, para criar um VHD 8 Debian execute os seguintes comandos para baixar azure gerenciar (e dependências) e execute o script azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar um VHD Debian manualmente

1. No Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir uma janela de console da máquina virtual.

3. Comente a linha para **CD-ROM deb** no `/etc/apt/source.list` se você configurar a máquina virtual em relação a um arquivo ISO.

4. Editar o `/etc/default/grub` de arquivo e modifique o parâmetro **GRUB_CMDLINE_LINUX** da seguinte maneira para incluir parâmetros de núcleo adicionais do Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Recriar o grub e execute:

        # sudo update-grub

6. Adicione repositórios Azure do Debian a /etc/apt/sources.list para Debian 7 ou 8:

    **7. x Debian "Wheezy"**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **8. x Debian "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Instale o agente de Linux Azure:

        # sudo apt-get update
        # sudo apt-get install waagent

8. 7 Debian, é necessário para executar o núcleo baseado em 3.16 do repositório wheezy backports. Primeiro, crie um arquivo denominado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    Execute "sudo chance-Obtenha instalação linux-imagem-amd64" para instalar o novo núcleo.

8. Desprovisione na máquina virtual e prepará-lo para provisionamento no Azure e executar:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação** -> Desligar para baixo no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para usar seu disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).
