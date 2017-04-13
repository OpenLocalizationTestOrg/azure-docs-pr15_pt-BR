<properties
    pageTitle="Criar e carregar um baseado em CentOS Linux VHD no Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional baseado em CentOS Linux."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS do Azure

- [Preparar um computador de virtual CentOS 6. x do Azure](#centos-6x)
- [Preparar uma máquina virtual de CentOS 7.0 + do Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo pressupõe que você já tenha instalado um CentOS (ou derivada semelhante) sistema operacional Linux um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd, por exemplo, uma solução de virtualização como Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


**Notas de instalação do centOS**

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.

- Não há suporte para o formato VHDX no Azure, apenas **fixo VHD**.  Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert.

- Ao instalar o sistema Linux é recomendável que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas.  [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.

- Não há suporte para NUMA máquina virtual com tamanhos de maiores devido a um bug em versões de núcleo Linux abaixo 2.6.37. Esse problema afeta principalmente distribuições usando o upstream núcleo Red Hat 2.6.32. Instalação manual do agente do Azure Linux (waagent) será desativado automaticamente na configuração GRUB para o núcleo Linux. Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.


## <a name="centos-6x"></a>CentOS 6. x ##

1. No Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir uma janela de console da máquina virtual.

3. Desinstale NetworkManager executando o seguinte comando:

        # sudo rpm -e --nodeps NetworkManager

    **Observação:** Se o pacote já não estiver instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4.  Crie um arquivo denominado **rede** na `/etc/sysconfig/` diretório que contém o texto a seguir:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Crie um arquivo denominado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev regras para evitar a geração estáticas regras para as interfaces Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on


8. **CentOS 6.3 somente**: Instale os drivers de lista para os serviços de integração de Linux (com).

    **Importante: A etapa só é válido para CentOS 6.3 e anteriores.**  No CentOS 6.4 + os serviços de integração do Linux são *já disponíveis no núcleo padrão*.

    - Siga as instruções de instalação na [página de download de lista com](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e instale o RPM em sua imagem.  


9. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

10. Se você gostaria de usar os espelhamentos OpenLogic hospedadas dentro do Azure dos data centers, em seguida, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo seguintes repositórios.  Isso também adicionará o repositório de **[openlogic]** que inclui pacotes para o agente do Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Observação:** O restante deste guia assumirá que você está usando pelo menos o repo [openlogic], que será usado para instalar o agente do Azure Linux abaixo.


11. Adicione a seguinte linha para /etc/yum.conf:

        http_caching=packages

    E **no CentOS 6.3 somente** adicione a seguinte linha:

        exclude=kernel*

12. Desabilitar o módulo yum "fastestmirror" editando o arquivo "/ etc/yum/pluginconf.d/fastestmirror.conf" e, em `[main]`, digite o seguinte:

        set enabled=0

13. Execute o seguinte comando para limpar os metadados de yum atuais:

        # yum clean all

14. **Em CentOS 6.3 somente**, atualize o núcleo usando o seguinte comando:

        # sudo yum --disableexcludes=all install kernel

15. Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um bug na versão de núcleo usado pelo CentOS 6.

    Além dos acima, é recomendável *Remover* parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128MB ou mais, que pode ser problemático em tamanhos de máquina virtual menores.


16. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

17. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent

    Observe que instalar o pacote de WALinuxAgent removerá o Gerenciador e pacotes de gnome NetworkManager se já não foram removidos conforme descrito na etapa 2.

18. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Alterações no CentOS 7 (e derivados semelhantes)**

Preparando uma máquina virtual de CentOS 7 para Azure é muito semelhante a 6 CentOS, porém, existem várias diferenças importantes vale a pena observar:

 - O pacote de Gerenciador já não está em conflito com o agente do Azure Linux. Este pacote é instalado por padrão e recomendamos que ele não será removido.
 - GRUB2 agora é usado como o carregador de inicialização do padrão, para que o procedimento para edição de parâmetros de núcleo mudou (veja abaixo).
 - XFS agora é o sistema de arquivo padrão. O sistema de arquivos ext4 ainda pode ser usado se desejado.


**Etapas de configuração**

1. No Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir uma janela de console da máquina virtual.

3.  Crie um arquivo denominado **rede** na `/etc/sysconfig/` diretório que contém o texto a seguir:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Crie um arquivo denominado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev regras para evitar a geração estáticas regras para as interfaces Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

7. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

8. Se você gostaria de usar os espelhamentos OpenLogic hospedadas dentro do Azure dos data centers, em seguida, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo seguintes repositórios.  Isso também adicionará o repositório de **[openlogic]** que inclui pacotes para o agente do Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Observação:** O restante deste guia assumirá que você está usando pelo menos o repo [openlogic], que será usado para instalar o agente do Azure Linux abaixo.

9.  Execute o seguinte comando para limpar os metadados de yum atuais e instale todas as atualizações:

        # sudo yum clean all
        # sudo yum -y update

10. Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abrir "/ padrão/etc/grub" em um editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Ela também desativa as novas convenções de nomenclatura CentOS 7 para NICs. Além dos acima, é recomendável *Remover* parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128MB ou mais, que pode ser problemático em tamanhos de máquina virtual menores.

11. Depois de ter edição "/ padrão/etc/grub" por acima, execute o seguinte comando para recriar a configuração de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

13. **Somente se criando a imagem do VMWare, VirtualBox ou KVM:** Adicione módulos Hyper-V em initramfs:

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Recrie o initramfs:

        # dracut –f -v

14. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar seu disco rígido virtual do CentOS Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).
