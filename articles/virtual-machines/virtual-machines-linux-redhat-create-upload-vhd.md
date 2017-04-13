<properties
    pageTitle="Criar e carregar um vermelho é o seu Enterprise Linux VHD para uso no Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional Red Hat Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em Red Hat do Azure

Neste artigo, você aprenderá como preparar uma máquina virtual de Red Hat Enterprise Linux (RHEL) para uso no Azure. Versões do RHEL que estão cobertas neste artigo são 6.7, 7.1 e 7.2. Hipervisores preparação que estão cobertos neste artigo são baseados em núcleo, Hyper-V Máquina Virtual (KVM) e VMware. Para obter mais informações sobre os requisitos de qualificação para participar do programa de acesso à nuvem do Red Hat, consulte o [site de acesso à nuvem do Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [RHEL em execução no Azure](https://access.redhat.com/articles/1989673).

[Preparar uma máquina virtual de RHEL 6.7 do Gerenciador de Hyper-V](#rhel67hyperv)

[Preparar uma máquina virtual de RHEL 7.1/7.2 do Gerenciador de Hyper-V](#rhel7xhyperv)

[Preparar uma máquina virtual de RHEL 6.7 de KVM](#rhel67kvm)

[Preparar uma máquina virtual de RHEL 7.1/7.2 do KVM](#rhel7xkvm)

[Preparar uma máquina virtual de RHEL 6.7 do VMware](#rhel67vmware)

[Preparar uma máquina virtual de RHEL 7.1/7.2 do VMware](#rhel7xvmware)

[Preparar uma máquina virtual de RHEL 7.1/7.2 de um arquivo de início rápido](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual baseada em Red Hat do Gerenciador de Hyper-V
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha instalado uma imagem RHEL (de um arquivo ISO que você obteve do site do Red Hat) em um disco rígido virtual (VHD). Para obter mais detalhes sobre como usar o Gerenciador de Hyper-V para instalar uma imagem de sistema operacional, consulte [instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do RHEL**

- Consulte também [Geral notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar Linux Azure.

- Não há suporte para o formato mais recente do VHDX no Azure. Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet do PowerShell **vhd convert** .

- VHDs devem ser criados como "fixo" – dinâmicos VHDs não têm suporte.

- Quando você estiver instalando o sistema Linux, recomendamos que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. LVM ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados se preferencial.

- Não configure uma partição de troca do disco do sistema operacional. Você pode configurar o agente de Linux para criar um arquivo de troca no disco temporário de recursos. Mais informações sobre isso estão disponíveis nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.

- Quando você usa **qemu img** converter imagens de disco em formato VHD, observe que há um bug conhecido nas versões qemu img 2.2.1 ou posterior. Esse bug resulta em um VHD formatado incorretamente. O problema destina-se a ser corrigido em uma versão futura de qemu img. Por agora, recomendamos que você use qemu-img versão 2.2.0 ou anterior.

### <a id="rhel67hyperv"> </a>Preparar uma máquina virtual de RHEL 6.7 do Gerenciador de Hyper-V###


1.  No Gerenciador de Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir uma janela de console da máquina virtual.

3.  Desinstale NetworkManager executando o seguinte comando:

        # sudo rpm -e --nodeps NetworkManager

    Observe que, se o pacote já não estiver instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

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

6.  Mover (ou remover) udev regras para evitar gerar regras estáticas da interface Ethernet. Essas regras causam problemas quando você clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

8.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um bug na versão de núcleo que é usado pelo RHEL 6.

    Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

11. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão. Modifique /etc/ssh/sshd_config para incluir a seguinte linha:

        ClientAliveInterval 180

12. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Observe que instalar o pacote de WALinuxAgent removerá o Gerenciador e pacotes de gnome NetworkManager se já não foram removidos conforme descrito na etapa 2.

13. Não crie trocar espaço no disco sistema operacional.
O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Cancelar o registro a assinatura (se necessário), execute o seguinte comando:

        # sudo subscription-manager unregister

15. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Clique em **ação > Desligar** no Gerenciador de Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.
 

### <a id="rhel7xhyperv"> </a>Preparar uma máquina virtual de RHEL 7.1/7.2 do Gerenciador de Hyper-V###

1.  No Gerenciador de Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir uma janela de console da máquina virtual.

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

5.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

6.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.
    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

8.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

10. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. Não crie trocar espaço no disco sistema operacional. O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Se você deseja cancelar o registro a assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Clique em **ação > Desligar** no Gerenciador de Hyper-V. Seu VHD Linux está pronto para ser carregado Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina virtual baseada em Red Hat do KVM

### <a id="rhel67kvm"> </a>Preparar uma máquina virtual de RHEL 6.7 de KVM###


1.  Baixe a imagem KVM de RHEL 6.7 do site do Red Hat.

2.  Defina uma senha de raiz.

    Gerar uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha de raiz com guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Alterar o segundo campo do usuário raiz de "!" para a senha criptografada.

3.  Criar uma máquina virtual no KVM da imagem qcow2, defina o tipo de disco para **qcow2**e configurar o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e entre como raiz.

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

6.  Mover (ou remover) udev regras para evitar gerar regras estáticas da interface Ethernet. Essas regras causam problemas quando você clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # chkconfig network on

8.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um bug na versão de núcleo que é usado pelo RHEL 6.

    Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.
    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

10. Adicione módulos Hyper-V em initramfs:  

    Editar `/etc/dracut.conf` e adicionar conteúdo: add_drivers + = "hv_vmbus hv_netvsc hv_storvsc"

    Recriar initramfs: # dracut – f - v

11. Desinstale inicialização de nuvem:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização:

        # chkconfig sshd on

    Modifique /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        # service sshd restart

13. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Instale o agente do Azure Linux executando o seguinte comando:

        # yum install WALinuxAgent
        # chkconfig waagent on

15. O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na **/etc/waagent.conf** adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Cancelar o registro a assinatura (se necessário), execute o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Desligar a máquina virtual do KVM.

19. Converta a imagem de qcow2 formato VHD.
    Primeiro converta a imagem em formato bruto:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Certifique-se de que o tamanho da imagem bruta está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB: # MB = US $((1024*1024)) tamanho # = $(informações img qemu - f bruto - saída json "rhel-6.7.raw" | \ gawk ' corresponder ($0, / "-tamanho virtual": ([0-9] +), /, val) {imprimir val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Converta o disco bruto em um VHD de tamanho fixo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Preparar uma máquina virtual de RHEL 7.1/7.2 do KVM###


1.  Baixe a imagem KVM de RHEL 7.1 (ou 7.2) vermelho é o seu site do. Usaremos RHEL 7.1 exemplo aqui.

2.  Defina uma senha de raiz.

    Gerar uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha de raiz com guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Alterar o segundo campo do usuário raiz de "!" para a senha criptografada.

3.  Criar uma máquina virtual no KVM da imagem qcow2, defina o tipo de disco para **qcow2**e configurar o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e entre como raiz.

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

6.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # chkconfig network on

7.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.
    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

9.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Adicione módulos Hyper-V em initramfs:

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Recrie initramfs:

        # dracut –f -v

11. Desinstale inicialização de nuvem:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização:

        # systemctl enable sshd

    Modifique /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        systemctl restart sshd

13. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Instale o agente do Azure Linux executando o seguinte comando:

        # yum install WALinuxAgent

    Ativar o serviço de waagent:

        # systemctl enable waagent.service

15. Não crie trocar espaço no disco sistema operacional. O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Cancelar o registro a assinatura (se necessário), execute o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Desligar a máquina virtual do KVM.

19. Converta a imagem de qcow2 formato VHD.

    Primeiro converta a imagem em formato bruto:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Certifique-se de que o tamanho da imagem bruta está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Converta o disco bruto em um VHD de tamanho fixo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina virtual baseada em Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha instalado uma máquina virtual RHEL em VMware. Para obter detalhes sobre como instalar um sistema operacional em VMware, consulte o [Guia de instalação do sistema operacional de convidado VMware](http://partnerweb.vmware.com/GOSIG/home.html).

- Quando você instala o sistema operacional Linux, recomendamos que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado máquina virtual outro para solução de problemas. LVM ou RAID pode ser usado em discos de dados se preferencial.

- Não configure uma partição de troca do disco do sistema operacional. Você pode configurar o agente de Linux para criar um arquivo de troca no disco temporário de recursos. Você pode encontrar mais informações sobre isso nas etapas abaixo.

- Quando você cria o disco rígido virtual, selecione **disco virtual do repositório como um único arquivo**.



### <a id="rhel67vmware"> </a>Preparar uma máquina virtual de RHEL 6.7 do VMware###

1.  Desinstale NetworkManager executando o seguinte comando:

         # sudo rpm -e --nodeps NetworkManager

    Observe que, se o pacote já não estiver instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

2.  Crie um arquivo denominado **rede** no/sysconfig/diretório /etc que contém o texto a seguir:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Crie um arquivo denominado **ifcfg-eth0** na /etc/sysconfig/network-scripts. / diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Mover (ou remover) udev regras para evitar gerar regras estáticas da interface Ethernet. Essas regras causam problemas quando você clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

6.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra "/ boot/grub/menu.lst" em um editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Isso desativará NUMA devido a um bug na versão de núcleo que é usado pelo RHEL 6.
    Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.
    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

9.  Adicione módulos Hyper-V em initramfs:

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

11. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. Não crie o espaço de troca no disco sistema operacional:

    O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Cancelar o registro a assinatura (se necessário), execute o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Desligar a máquina virtual e converta o arquivo VMDK em um arquivo. vhd.

    Primeiro converta a imagem em formato bruto:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Certifique-se de que o tamanho da imagem bruta está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Converta o disco bruto em um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Preparar uma máquina virtual de RHEL 7.1/7.2 do VMware###

1.  Crie um arquivo denominado **rede** no/sysconfig/diretório /etc que contém o texto a seguir:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Crie um arquivo denominado **ifcfg-eth0** na /etc/sysconfig/network-scripts. / diretório que contém o texto a seguir:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Certifique-se de que o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

4.  Registre sua assinatura é o seu vermelho para habilitar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Modificar a linha de inicialização de núcleo em sua configuração grub para incluir parâmetros de núcleo adicionais do Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isso garantirá que todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas. Além da ação acima, recomendamos que você remova os parâmetros a seguir:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.
    A opção crashkernel pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128 MB ou mais. Isso pode ser problemático em tamanhos de máquina virtual menores.

6.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de grub:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Adicione módulos Hyper-V em initramfs:

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Recrie initramfs:

        # dracut –f -v

8.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização. Isso geralmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

9.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi pressionado ao repositório Red Hat extras. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Instale o agente do Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. Não crie trocar espaço no disco sistema operacional. O agente de Linux Azure pode configurar o espaço de troca automaticamente usando o disco de recursos local que está anexado à máquina virtual após a máquina virtual está provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Se você deseja cancelar o registro a assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

13. Execute os seguintes comandos desprovisionamento na máquina virtual e prepará-lo para provisionamento no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Desligar a máquina virtual e converta o arquivo VMDK em formato VHD.

    Primeiro converta a imagem em formato bruto:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Certifique-se de que o tamanho da imagem bruta está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Converta o disco bruto em um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina virtual baseada em Red Hat de um arquivo ISO usando um arquivo de início rápido automaticamente


### <a id="rhel7xkickstart"> </a>Preparar uma máquina virtual de RHEL 7.1/7.2 de um arquivo de início rápido###


1.  Criar um arquivo de início rápido com o conteúdo abaixo e salve o arquivo. Para obter detalhes sobre a instalação de início rápido, consulte o [Guia de instalação do início rápido](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Coloque o arquivo de início rápido em um local acessível pelo sistema de instalação.

3.  No Gerenciador de Hyper-V, crie uma nova VM. Na página **Conectar do disco rígido Virtual** , selecione **anexar um disco rígido virtual posteriormente**e conclua o Assistente de máquina Virtual novo.

4.  Abra as configurações de máquina virtual:

    a.  Anexe um novo disco rígido virtual para a máquina virtual. Certifique-se de selecionar **Formato VHD** e **Tamanho fixo**.

    b.  Anexe a instalação ISO a unidade de DVD.

    c.  Defina o BIOS para inicialização de CD.

5.  Inicie a máquina virtual. Quando o guia de instalação for exibida, pressione **Tab** para configurar as opções de inicialização.

6.  ENTER `inst.ks=<the location of the kickstart file>` no final das opções de inicialização e pressione **Enter**.

7.  Aguarde concluir a instalação. Quando ele for concluído, a máquina virtual será encerrada automaticamente. Seu VHD Linux está pronto para ser carregado Azure.

## <a name="known-issues"></a>Problemas conhecidos
Existem problemas conhecidos quando você estiver usando RHEL 7.1 no Hyper-V e Azure.

### <a name="disk-io-freeze"></a>Congelamento de e/s de disco

Esse problema pode ocorrer durante as atividades de e/s de disco de armazenamento frequente com RHEL 7.1 no Hyper-V e Azure.   

Taxa de reprodução:

Este problema é intermitente. No entanto, ele ocorre mais frequentemente durante frequentes operações de e/s de disco no Hyper-V e Azure.   


[AZURE.NOTE] Esse problema conhecido já tratado por Red Hat. Para instalar as correções associadas, execute o seguinte comando:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O driver de Hyper-V não pode ser incluído no disco de RAM inicial ao usar um hipervisor não-Hyper-V

Em alguns casos, Linux instaladores podem não incluir os drivers Hyper-v no disco de RAM inicial (initrd ou initramfs), a menos que ele detecte que ele seja executado em um ambiente de Hyper-V.

Quando você estiver usando um sistema de virtualização diferentes (ou seja, Virtualbox Xen, etc.) para preparar sua imagem de Linux, talvez seja necessário recriar initrd para garantir que pelo menos os módulos de núcleo hv_vmbus e hv_storvsc estão disponíveis no disco RAM inicial. Este é um problema conhecido pelo menos nos sistemas baseados na distribuição Red Hat upstream.

Para resolver esse problema, você precisa adicionar módulos Hyper-V em initramfs e recriá-lo:

Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Recrie initramfs:

        # dracut –f -v

Para obter mais detalhes, consulte as informações sobre como [recriar initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar seu disco rígido virtual do Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Para obter mais detalhes sobre os que são certificados para executar Red Hat Enterprise Linux, veja [o site Red Hat](https://access.redhat.com/certified-hypervisors)hipervisores.
