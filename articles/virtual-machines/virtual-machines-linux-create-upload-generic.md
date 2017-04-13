<properties
    pageTitle="Criar e carregar um VHD Linux no Azure"
    description="Aprenda a criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operacional Linux."
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
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Informações para as distribuições não aprovado #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: O Azure plataforma SLA aplica-se a máquinas virtuais executando o sistema operacional Linux somente quando um do [aprovado distribuições](virtual-machines-linux-endorsed-distros.md) é usado. Todas as distribuições Linux que são fornecidas na Galeria de imagem Azure são endossadas distribuições com a configuração necessária.

- [Linux no Azure - aprovado distribuições](virtual-machines-linux-endorsed-distros.md)
- [Suporte para Linux imagens no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure precisa atender a um número de pré-requisitos para ter a chance de executar corretamente na plataforma.  Este artigo não é completa como cada distribuição é diferente; e é bem possível que mesmo se atender a todos os critérios abaixo você ainda precisará ajustar significativamente o seu sistema de Linux para garantir que ele executa corretamente na plataforma.

É por isso que recomendamos que você começa com um dos nossos [Linux no Azure aprovado distribuições](virtual-machines-linux-endorsed-distros.md) quando possível. Os artigos a seguir o guiará como preparar as várias distribuições Linux endossadas que têm suporte no Azure:

- **[Distribuições baseadas em centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

O restante deste artigo se concentrará na orientação geral para executar sua distribuição do Linux no Azure.


## <a name="general-linux-installation-notes"></a>Notas de instalação do Linux gerais ##

- Não há suporte para o formato VHDX no Azure, apenas **fixo VHD**.  Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o cmdlet vhd convert. Se você estiver usando VirtualBox isso significa que selecionando **tamanho fixo** em vez do padrão alocado dinamicamente ao criar o disco.

- Ao instalar o sistema Linux é *recomendável* que você use partições padrão em vez de LVM (geralmente, o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado para outra máquina virtual idêntico para solução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser usado em discos de dados.

- É necessário suporte de núcleo para montar sistemas de arquivos UDF. Na primeira inicialização no Azure a configuração de provisionamento é passada para o Linux VM por meio de mídia UDF formatado que está anexada ao convidado. O agente do Azure Linux deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a máquina virtual.

- Versões de núcleo de Linux abaixo 2.6.37 não suportam no Hyper-V com tamanhos maiores de máquina virtual. Esse problema principalmente impactos distribuições mais antigas usando o upstream núcleo Red Hat 2.6.32 e foi corrigido no RHEL 6.6 (núcleo-2.6.32-504). Sistemas personalizados kernels anteriores a 2.6.37 ou baseada em RHEL kernels mais antigos de 2.6.32-504 deve definir o parâmetro de inicialização `numa=off` do núcleo de linha de comando no grub.conf. Para obter mais informações, consulte Red Hat [436883 KB](https://access.redhat.com/solutions/436883).

- Não configure uma partição de troca do disco do sistema operacional. O agente de Linux pode ser configurado para criar um arquivo de troca no disco temporário de recursos.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.

- Todos os VHDs devem ter tamanhos de múltiplos de 1 MB.


### <a name="installing-linux-without-hyper-v"></a>Instalação Linux sem Hyper-V ###

Em alguns casos, Linux instaladores podem não incluir os drivers Hyper-v no ramdisk inicial (initrd ou initramfs), a menos que ele detecte que ele está executando uma um ambiente de Hyper-V.  Ao usar um sistema de virtualização diferentes (ou seja, Virtualbox KVM, etc.) para preparar sua imagem de Linux, talvez seja necessário recriar o initrd para garantir que pelo menos o `hv_vmbus` e `hv_storvsc` módulos de núcleo estão disponíveis no ramdisk inicial.  Este é um problema conhecido pelo menos nos sistemas baseados na distribuição Red Hat upstream.

O mecanismo para recriar a imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação da sua distribuição ou suporte para o procedimento correto.  Aqui está um exemplo de como recriar o initrd usando o `mkinitrd` utilitário:

Primeiro, fazer backup da imagem initrd existente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Em seguida, recriar o initrd com a `hv_vmbus` e `hv_storvsc` módulos de núcleo:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Redimensionando VHDs ###

Imagens VHD no Azure devem ter um tamanho virtual alinhado a 1MB.  Normalmente, VHDs criados usando o Hyper-V já devem alinhadas corretamente.  Se o VHD não estiver alinhado corretamente, em seguida, você receberá uma mensagem de erro semelhante à seguinte quando você tentar criar uma *imagem* a partir de seu VHD:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para corrigir isso, você pode redimensionar a máquina virtual usando o console do Gerenciador de Hyper-V ou o cmdlet do Powershell [VHD de redimensionamento](http://technet.microsoft.com/library/hh848535.aspx) .  Se você não estiver executando em um ambiente do Windows, em seguida, é recomendável usar qemu img converter (se necessário) e redimensionar o VHD.

> [AZURE.NOTE] Há um bug conhecido nas versões qemu img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2.6. É recomendável usar qemu-img 2.2.0 ou inferior ou atualize para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Redimensionando o VHD diretamente usando ferramentas tais como `qemu-img` ou `vbox-manage` pode resultar em um VHD impossibilidade.  Portanto é recomendável primeiro converter o VHD a uma imagem de disco BRUTO.  Se a imagem de máquina virtual já foi criada como imagem de disco BRUTO (o padrão para alguns hipervisores como KVM) você pode ignorar esta etapa:

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcule o tamanho necessário da imagem de disco para garantir que o tamanho virtual está alinhado para 1MB.  O seguinte script do shell bash pode ajudar com isso.  O script usa "`qemu-img info`" para determinar o tamanho virtual da imagem do disco e depois calcula o tamanho para o próximo 1MB:

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Redimensione o disco bruto usando $rounded_size definido no script acima:

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. Agora, converta o disco BRUTO volta para um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Requisitos de núcleo de Linux ##

Os drivers de serviços de integração de Linux (LIS) para Hyper-V e Azure são contribuiu diretamente o upstream núcleo de Linux. Muitas distribuições que incluem uma versão de núcleo Linux recente (ou seja, 3. x) serão já tiver esses drivers disponíveis ou caso contrário, forneça backported versões desses drivers com seus kernels.  Esses drivers constantemente estão sendo atualizados no núcleo upstream com novas correções e recursos, então, quando possível é recomendável para executar uma [distribuição de aprovado](virtual-machines-linux-endorsed-distros.md) que incluirá essas correções e atualizações.

Se você estiver executando uma variante do Red Hat Enterprise Linux versões **6.0 6.3**, você precisará instalar os drivers mais recentes de lista com Hyper-v. Os drivers podem ser encontrados [neste local](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir de RHEL **6.4 +** (e derivados) os drivers de lista com já estão incluídos com o núcleo e então não há pacotes de instalação adicionais são necessários para executar esses sistemas no Azure.

Se um núcleo personalizado for necessário, é recomendável usar uma versão mais recente do núcleo (isto é, **3,8 +**). Para essas distribuições ou fornecedores que mantêm seu próprios núcleo, alguns esforço será necessário para regularmente backport os drivers de lista com do núcleo upstream para seu núcleo personalizado.  Mesmo se você já estiver executando uma versão de núcleo relativamente recente, é altamente recomendável para controlar as correções superior na lista com drivers e backport aqueles conforme necessário. O local dos arquivos de origem de lista com driver está disponível no arquivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore de origem de núcleo Linux:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Em um mínimo, ausência dos seguintes patches tiver sido conhecidas por causar problemas no Azure e então elas devem ser incluídas no núcleo. Esta lista não é completa ou completa para todas as distribuições:

- [ata_piix: adiar discos para os drivers Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: para pacotes em trânsito no caminho de redefinição de conta](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: evitar o uso da WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: desabilitar ESCREVER mesmo para RAID e os drivers de adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: ponteiro NULL cancelam fix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: falhas de buffer de anel podem resultar em Congelar e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs: proteger contra execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>O agente de Linux Azure ##

O [Agente do Azure Linux](virtual-machines-linux-agent-user-guide.md) (waagent) é necessário para provisionar corretamente computador virtual Linux no Azure. Você pode obter a versão mais recente, problemas de arquivo ou envie solicitações de recepção no [Linux agente GitHub repo](https://github.com/Azure/WALinuxAgent).

- O agente de Linux é lançado sob a Licença Apache 2.0. Muitas distribuições já fornecem RPM ou deb pacotes para o agente e então em alguns casos isso pode ser instalado e atualizado com pouco esforço.

- O agente de Linux Azure requer Python v 2.6 +.

- O agente também requer o módulo pyasn1 python. A maioria das distribuições fornecem isso como um pacote separado que pode ser instalado.

- Em alguns casos o agente de Linux Azure pode não ser compatível com o Gerenciador. Muitos dos pacotes RPM/Deb fornecidos pelo distribuições configurar NetworkManager como um conflito para o pacote de waagent e, portanto, irá desinstalar NetworkManager quando você instala o pacote do agente Linux.


## <a name="general-linux-system-requirements"></a>Requisitos de sistema do Linux geral ##

- Modificar a linha de inicialização de núcleo em GRUB ou GRUB2 para incluir os parâmetros a seguir. Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas:

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Isso garantirá todas as mensagens de console são enviadas para a primeira porta serial, que pode ajudar o Azure suporte com depuração de problemas.

    Além dos acima, é recomendável *Remover* parâmetros a seguir se existirem:

        rhgb quiet crashkernel=auto

    Inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviadas para a porta serial.

    O `crashkernel` opção pode ser esquerda configuradas se desejado, mas lembre-se que esse parâmetro reduzir a quantidade de memória disponível na VM por 128MB ou mais, que pode ser problemático em tamanhos de máquina virtual menores.

- Instalando o agente Linux Azure

    O agente de Linux Azure é necessário para provisionamento de uma imagem de Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou Deb (o pacote normalmente é chamado 'WALinuxAgent' ou 'walinuxagent').  O agente também pode ser instalado manualmente seguindo as etapas no [Guia do agente de Linux](virtual-machines-linux-agent-user-guide.md).

- Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado no momento da inicialização.  Isso geralmente é o padrão.

- Não crie trocar espaço no disco SO

    O agente de Linux Azure pode configurar automaticamente o espaço de troca usando o disco de recursos local que está anexado à máquina virtual após a configuração no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada. Depois de instalar o agente do Azure Linux (consulte a etapa anterior), modificar os seguintes parâmetros na /etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Como uma etapa final, execute os seguintes comandos para desprovisionamento na máquina virtual:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] Em Virtualbox, você poderá ver o seguinte erro após a execução ' waagent-force - deprovision': `[Errno 5] Input/output error`. Essa mensagem de erro não é fundamental e pode ser ignorada.

- Em seguida, será necessário desligar a máquina virtual e carregar o VHD no Azure.
