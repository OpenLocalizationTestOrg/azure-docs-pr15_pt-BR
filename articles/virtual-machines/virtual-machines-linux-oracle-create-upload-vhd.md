<properties
    pageTitle="Criar e carregar um VHD de Linux Oracle | Microsoft Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional de Oracle Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina virtual de Oracle Linux do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo pressupõe que você já tenha instalado um sistema operacional de Oracle Linux para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd, por exemplo, uma solução de virtualização como Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Notas de instalação do Oracle Linux

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.

- Núcleo compatível do Red Hat da Oracle e suas UEK3 (Unbreakable Enterprise núcleo) são suportados no Hyper-V e Azure. Para obter melhores resultados, certifique-se de atualizar para o mais recente núcleo ao preparar seu VHD de Linux Oracle.

- UEK2 da Oracle não é suportada no Hyper-V e Azure como ele não inclui os drivers necessários.

- Não há suporte para o formato VHDX no Azure, apenas **fixo VHD**.  Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert.

- Ao instalar o sistema Linux é recomendável que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.

- Não há suporte para NUMA máquina virtual com tamanhos de maiores devido a um bug em versões de núcleo Linux abaixo 2.6.37. Esse problema afeta principalmente distribuições usando o upstream núcleo Red Hat 2.6.32. Instalação manual do agente do Azure Linux (waagent) será desativado automaticamente na configuração GRUB para o núcleo Linux. Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.

- Verifique se o `Addons` repositório está ativado. Editar o arquivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Você deve concluir as etapas de configuração específicas no sistema operacional da máquina virtual executar no Azure.

1. No painel central do Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

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

        # chkconfig network on

8. Instale o python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

9.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer este abrir "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um erro no núcleo compatível do Red Hat da Oracle.

    Além dos acima, é recomendável *Remover* parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128MB ou mais, que pode ser problemático em tamanhos de máquina virtual menores.


10. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

11. Instale o agente de Linux Azure executando o comando a seguir. A versão mais recente é 2.0.15.

        # sudo yum install WALinuxAgent

    Observe que instalar o pacote de WALinuxAgent removerá o Gerenciador e pacotes de gnome NetworkManager se já não foram removidos conforme descrito na etapa 2.

12. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Alterações no Linux Oracle 7**

Preparando uma máquina virtual de Oracle Linux 7 para Azure é muito semelhante a Oracle Linux 6, porém, existem várias diferenças importantes vale a pena observar:

 - O núcleo compatível Red Hat e UEK3 da Oracle são suportados no Azure.  Recomenda-se o núcleo UEK3.
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

5.  Modificar udev regras para evitar a geração estáticas regras para as interfaces Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

7. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

8.  Execute o seguinte comando para limpar os metadados de yum atuais e instale todas as atualizações:

        # sudo yum clean all
        # sudo yum -y update

9.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso abrir "/ padrão/etc/grub" em um editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Ela também desativa as novas convenções de nomenclatura OEL 7 para NICs. Além dos acima, é recomendável *Remover* parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128MB ou mais, que pode ser problemático em tamanhos de máquina virtual menores.


10. Depois de ter edição "/ padrão/etc/grub" por acima, execute o seguinte comando para recriar a configuração de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

12. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Clique em **ação -> Desligar para baixo** no Gerenciador Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.


## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar seu VHD Oracle Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).
