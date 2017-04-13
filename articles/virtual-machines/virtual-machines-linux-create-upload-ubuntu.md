<properties
    pageTitle="Criar e carregar um VHD de Linux Ubuntu no Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional de Ubuntu Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina virtual de Ubuntu do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imagens de nuvem Ubuntu oficiais
Ubuntu agora publica VHDs oficial do Azure para download em [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Se você precisar criar sua própria imagem Ubuntu especializada para Azure, em vez de usar o procedimento manual abaixo é recomendável iniciar com estes conhecidos trabalhando VHDs e personalizar conforme necessário. As versões mais recentes de imagem sempre podem ser encontradas nos seguintes locais:

 - Ubuntu 12.04/precisas: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/confiável: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha instalado um sistema operacional de Ubuntu Linux para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd, por exemplo, uma solução de virtualização como Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Ubuntu**

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.
- Não há suporte para o formato VHDX no Azure, apenas **fixo VHD**.  Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert.
- Ao instalar o sistema Linux é recomendável que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.
- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.
- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.


## <a name="manual-steps"></a>Etapas manuais

> [AZURE.NOTE] Antes de criar sua própria imagem Ubuntu personalizada para Azure, considere usar as imagens do [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) em vez disso.


1. No painel central do Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Substitua os repositórios atuais na imagem a ser usada repos Azure do Ubuntu. As etapas ligeiramente variam dependendo da versão do Ubuntu.

    Antes de editar /etc/apt/sources.list, é recomendável fazer um backup:

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. As imagens do Ubuntu Azure estão seguindo o núcleo de *habilitação de hardware* (HWE). Atualize o sistema operacional para o mais recente núcleo executando os seguintes comandos:

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modificar a linha de inicialização de núcleo para Grub incluir parâmetros de núcleo adicionais do Azure. Para fazer isso abrir "/ padrão/etc/grub" em um editor de texto, encontre a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicioná-lo, se necessário) e editá-lo para incluir os parâmetros a seguir:

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Salvar e fechar esse arquivo e, em seguida, execute '`sudo update-grub`'. Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o suporte técnico do Azure com problemas de depuração.

6.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

7.  Instale o agente de Linux Azure:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Observe que instalar o `walinuxagent` pacote removerá o `NetworkManager` e `NetworkManager-gnome` pacotes, se eles estiverem instalados.

8.  Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar seu disco rígido virtual do Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Referências ##

Ubuntu núcleo de habilitação (HWE) de hardware:

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-Cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
