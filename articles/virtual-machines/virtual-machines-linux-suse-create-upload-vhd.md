<properties
    pageTitle="Criar e carregar um VHD de Linux SUSE no Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional SUSE Linux."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual SLES ou openSUSE do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo pressupõe que você já tenha instalado um SUSE ou openSUSE sistema operacional Linux para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd, por exemplo, uma solução de virtualização como Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / notas de instalação do openSUSE

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.

- Não há suporte para o formato VHDX no Azure, apenas **fixo VHD**.  Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert.

- Ao instalar o sistema Linux é recomendável que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.

- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.


## <a name="use-suse-studio"></a>Usar SUSE Studio
[SUSE Studio](http://www.susestudio.com) pode facilmente criar e gerenciar suas imagens SLES e openSUSE do Azure e Hyper-V. Essa é a abordagem recomendada para personalizar suas próprias imagens SLES e openSUSE.

Como uma alternativa para criar seu próprio VHD, SUSE também publica imagens BYOS (trazer seu próprio assinatura) para SLES em [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar SUSE Linux Enterprise Server 11 SP4 ##

1. No painel central do Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3. Registre seu sistema de Enterprise SUSE Linux para permitir que ele baixe atualizações e instalar pacotes.

4. Atualize o sistema com os patches mais recentes:

        # sudo zypper update

5. Instale o agente de Linux Azure do repositório SLES:

        # sudo zypper install WALinuxAgent

6. Verifique se waagent está definido como "em" no comando chkconfig e se não for, habilitá-lo para iniciar automaticamente:
               
        # sudo chkconfig waagent on

7. Verifique se o serviço de waagent está executando e se não, iniciá-lo: 

        # sudo service waagent start
                
8. Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer este abrir "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas.

9. Confirme que /boot/grub/menu.lst e /etc/fstab ambos referenciam o disco usando seu UUID (por uuid) em vez da identificação de disco (por-id). 

    Obter disco UUID
    
        # ls /dev/disk/by-uuid/

    Se /dev/disk/by-id / é usado, atualizar /etc/fstab /boot/grub/menu.lst tanto/etc/com o valor de PRI por uuid

    Antes de alteração
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Após alteração
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modificar udev regras para evitar a geração estáticas regras para as interfaces Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. É recomendado para editar o arquivo "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

12. Em "/ etc/sudoers", comente ou remova as seguintes linhas se existirem:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

14. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


----------

## <a name="prepare-opensuse-131"></a>Preparar openSUSE 13.1 + ##

1. No painel central do Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3. No shell, execute o comando '`zypper lr`'. Se esse comando retorna saída semelhante à seguinte, os repositórios configurados como esperado – sem ajustes são necessárias (Observe que os números de versão podem variar):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Se o comando retornar "Sem repositórios definidos...", em seguida, use os comandos a seguir para adicionar esses repos:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Em seguida, você pode verificar os repositórios foram adicionados executando o comando '`zypper lr`' novamente. No caso de um dos repositórios atualização relevante não estiver ativado, ative-o com o seguinte comando:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Atualize o núcleo para a versão mais recente disponível:

        # sudo zypper up kernel-default

    Ou atualizar o sistema com todos os patches mais recentes:

        # sudo zypper update

5.  Instale o Azure agente de Linux.

        # sudo zypper install WALinuxAgent

6.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Além disso, remova os seguintes parâmetros na linha de inicialização de núcleo se existirem:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  É recomendado para editar o arquivo "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importante:** Em "/ etc/sudoers", comente ou remova as seguintes linhas se existirem:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

10. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Certifique-se de que o agente de Linux Azure é executado na inicialização:

        # sudo systemctl enable waagent.service

13. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar seu disco rígido virtual do SUSE Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).
