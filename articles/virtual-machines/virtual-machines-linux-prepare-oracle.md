<properties
pageTitle="Preparar um computador de Virtual Linux Oracle para Azure | Microsoft Azure"
description="Configuração de passo a passo de uma máquina virtual do Oracle executando Linux no Microsoft Azure."
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina virtual de Oracle Linux do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Preparar uma máquina virtual de Oracle Linux 6.4 + do Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [Preparar uma máquina virtual de Oracle Linux 7.0 + do Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha instalado um sistema operacional de Oracle Linux para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd, por exemplo, uma solução de virtualização como Hyper-V. Para obter instruções, consulte [instalar Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Oracle Linux**

- Núcleo compatível do Red Hat da Oracle e seu UEK3 (Unbreakable Enterprise núcleo) são suportados no Hyper-V e Azure. Para obter melhores resultados, certifique-se de atualizar para o mais recente núcleo ao preparar seu VHD de Linux Oracle.

- UEK2 da Oracle não é suportada no Hyper-V e Azure como ele não inclui os drivers necessários.

- Não há suporte para o formato mais recente do VHDX no Azure. Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert.

- Quando você estiver instalando o sistema Linux, recomendamos que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. LVM ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.

- Não há suporte para NUMA máquina virtual com tamanhos de maiores devido a um bug em versões de núcleo Linux abaixo 2.6.37. Esse problema afeta principalmente distribuições que usam o upstream núcleo vermelho é o seu 2.6.32. Instalação manual do agente do Azure Linux (waagent) será desativado automaticamente na configuração GRUB para o núcleo Linux. Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos. Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.

- Verifique se o `Addons` repositório está ativado. Escolha Editar o arquivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Você deve concluir as etapas de configuração específicas no sistema operacional da máquina virtual executar no Azure.

1. No painel central do Gerenciador de Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3. Desinstale NetworkManager executando o seguinte comando:

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE] Se o pacote já não estiver instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4. Crie um arquivo denominado **rede** no/sysconfig/diretório /etc que contém o texto a seguir:

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  Crie um arquivo denominado **ifcfg-eth0** na /etc/sysconfig/network-scripts. / diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  Mover (ou remover) udev regras para evitar gerar regras estáticas da interface Ethernet. Essas regras causam problemas quando você estiver clonagem uma máquina virtual em Azure ou Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # chkconfig network on

8.  Instale o python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

9.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um erro no núcleo compatível do Red Hat da Oracle.

    Além dos acima, recomendamos que você *Remova* os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

10.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão.

11.  Instale o agente do Azure Linux executando o seguinte comando:

        # <a name="sudo-yum-install-walinuxagent"></a>instalação de yum sudo WALinuxAgent

    Observe que instalar o pacote de WALinuxAgent removerá o Gerenciador e pacotes de gnome NetworkManager se já não foram removidos conforme descrito na etapa 2.

12.  Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é *temporário* e pode ser esvaziada quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 # # Observação: definir essa opção como tudo o que você precisa disponibilizá-lo.

13.  Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - deprovision
        # <a name="export-histsize0"></a>Exportar HISTSIZE = 0
        # <a name="logout"></a>Logout

14.  Clique em **ação -\> desligar** no Gerenciador de Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.

## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Alterações no Linux Oracle 7**

Preparando uma máquina virtual de Oracle Linux 7 para Azure é muito semelhante ao processo para Oracle Linux 6. No entanto, há várias diferenças importantes vale a pena observar:

-   O núcleo compatível Red Hat e UEK3 da Oracle são suportados no Azure. Recomendamos o núcleo UEK3.

-   O pacote de Gerenciador já não está em conflito com o agente do Azure Linux. Este pacote é instalado por padrão e recomendamos que ele não será removido.

-   GRUB2 agora é usado como o carregador de inicialização do padrão, para que o procedimento para edição de parâmetros de núcleo mudou (veja abaixo).

-   XFS agora é o sistema de arquivo padrão. O sistema de arquivos ext4 ainda pode ser usado se desejado.

**Etapas de configuração**

1.  No Gerenciador de Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir uma janela de console da máquina virtual.

3.  Crie um arquivo denominado **rede** no/sysconfig/diretório /etc que contém o texto a seguir:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Crie um arquivo denominado **ifcfg-eth0** na /etc/sysconfig/network-scripts. / diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  Mover (ou remover) udev regras para evitar gerar regras estáticas da interface Ethernet. Essas regras causam problemas quando você estiver clonagem uma máquina virtual no Microsoft Azure ou Hyper-V.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

7.  Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

8.  Execute o seguinte comando para limpar os metadados de yum atuais e instale todas as atualizações:

        # sudo yum clean all
        # sudo yum -y update

9.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abrir "/ padrão/etc/grub" em um editor de texto e editar o GRUB\_CMDLINE\_parâmetro LINUX, por exemplo:

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Além dos acima, recomendamos que você *Remova* os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

10.  Depois de ter edição "/ padrão/etc/grub", execute o seguinte comando para recriar a configuração de grub:

        # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2-mkconfig - o /boot/grub2/grub.cfg

11.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão.

12.  Instale o agente do Azure Linux executando o seguinte comando:

        # <a name="sudo-yum-install-walinuxagent"></a>instalação de yum sudo WALinuxAgent

13.  Não crie trocar espaço no disco sistema operacional.

    O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é *temporário* e pode ser esvaziada quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 # # Observação: definir essa opção como tudo o que você precisa disponibilizá-lo.

14.  Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - deprovision
        # <a name="export-histsize0"></a>Exportar HISTSIZE = 0
        # <a name="logout"></a>Logout

15.  Clique em **ação -\> desligar** no Gerenciador de Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.
