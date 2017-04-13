<properties
    pageTitle="Otimizando sua VM Linux no Azure | Microsoft Azure"
    description="Aprenda algumas dicas de otimização para certificar-se de que você configurou sua VM Linux para um desempenho ideal no Azure"
    keywords="máquina virtual Linux, máquina virtual linux, ubuntu virtual machine" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar sua VM Linux no Azure

Criando uma máquina virtual do Linux (máquina virtual) é fácil fazer da linha de comando ou a partir do portal. Este tutorial mostra como garantir que você tenha configurá-lo para otimizar seu desempenho na plataforma do Microsoft Azure. Este tópico usa um servidor de Ubuntu máquina virtual, mas você também pode criar usando [suas próprias imagens como modelos](virtual-machines-linux-create-upload-generic.md)de máquina virtual Linux.  

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que você já tem uma assinatura do Azure ([inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)), [instalou o CLI Azure](../xplat-cli-install.md) em funcionamento e já tiver provisionado uma máquina virtual em sua assinatura do Azure. Antes de fazer nada com o Azure - você precisa autenticar à sua assinatura. Para fazer isso com o Azure CLI, basta digitar `azure login` para iniciar o processo interativo. 

## <a name="azure-os-disk"></a>Azure disco do sistema operacional

Depois de criar uma Linux Vm no Azure, ele tem dois discos associados a ele. /dev/sda é seu disco de sistema operacional, /dev/sdb é seu disco temporário.  Não use o disco de sistema operacional principal (/ desenvolvimento/sda) para tudo, exceto o sistema operacional como ele é otimizado para o tempo de inicialização de máquina virtual rápido e não oferece bom desempenho para suas cargas de trabalho. Você deseja anexar um ou mais discos para sua máquina virtual para obter persistente e otimizado armazenamento para seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionando discos para destinos de tamanho e desempenho 

Com base no tamanho da máquina virtual, você pode anexar até 16 discos adicionais em uma série de um, 32 discos em uma série de D e 64 discos em uma série de G máquina - cada até 1 TB em tamanho. Adicionar discos extras conforme necessário por suas necessidades de IOps e espaço. Cada disco tem uma meta de desempenho de 500 IOps para armazenamento padrão e até 5000 IOps por disco para armazenamento de Premium.  Para obter mais informações sobre discos de armazenamento de Premium, consulte [armazenamento Premium: armazenamento de alto desempenho para VMs Azure](../storage/storage-premium-storage.md)

Para obter o maior IOps em mídias de armazenamento de Premium onde as configurações de cache foram definidas para "Somente leitura" ou "Nenhum", você deve desativar "obstáculos" durante a montagem do sistema de arquivo no Linux. Você não precisa obstáculos porque as gravações discos de armazenamento de Premium feito são duráveis para essas configurações de cache.

- Se você usar **reiserFS**, desative obstáculos usando a opção de montagem "barreira = nenhum" (para habilitar obstáculos, use "barreira = liberação")
- Se você usar **ext3/ext4**, desative obstáculos usando a opção de montagem "barreira = 0" (para habilitar obstáculos, use "barreira = 1")
- Se você usar **XFS**, desative obstáculos usando a opção de montagem "nobarrier" (para habilitar obstáculos, use a opção "barreira")

## <a name="storage-account-considerations"></a>Considerações de conta de armazenamento

Quando você cria sua VM Linux no Azure, você deverá verificar se que você anexar discos de contas de armazenamento residente na mesma região como sua máquina virtual para garantir proximidade e minimizar latência de rede.  Cada conta de armazenamento padrão tem um máximo de 20 k IOps e uma capacidade de tamanho de 500 TB.  Isso funciona para aproximadamente 40 discos muito usados, incluindo o disco de sistema operacional e qualquer disco de dados que você criar. Para contas de armazenamento Premium, não há nenhum limite máximo de IOps, mas há um limite de tamanho de 32 TB. 

Quando lidar com alta IOps cargas de trabalho e você tiver escolhido padrão de armazenamento para seus discos, talvez seja necessário dividir os discos em várias contas de armazenamento para garantir que você não tiver atingido o limite de IOps 20.000 para contas de armazenamento padrão. Sua máquina virtual pode conter uma mistura de discos de entre contas de armazenamento diferentes e tipos de conta de armazenamento para atingir sua configuração ideal. 

## <a name="your-vm-temporary-drive"></a>Sua unidade temporária de máquina virtual

Por padrão quando você cria uma máquina virtual, Azure oferece um sistema operacional (/ desenvolvimento/sda) e um disco temporário (/ desenvolvimento/sdb).  Todos os discos adicionais que adicionar aparecem como /dev/sdc, /dev/sdd, /dev/sde e assim por diante. Todos os dados em seu disco temporário (/ desenvolvimento/sdb) não é duráveis e pode ser perdidos se eventos específicos, como redimensionamento de máquina virtual, nova implantação, ou manutenção força uma reinicialização da sua máquina virtual.  O tamanho e tipo do disco temporário está relacionado ao tamanho máquina virtual escolhido no momento da implantação. No caso de qualquer do tamanho premium VMs (série DS, G e DS_V2) na unidade temporária é feita por um SSD local para desempenho adicional de até 48 k IOps. 

## <a name="linux-swap-file"></a>Arquivo de troca de Linux

Se sua máquina virtual do Azure for de uma imagem Ubuntu ou CoreOS, você pode usar CustomData para enviar uma nuvem-config inicialização de nuvem. Se [carregar uma imagem de Linux personalizada](virtual-machines-linux-upload-vhd.md) que usa a inicialização de nuvem, você também configurar partições de troca usando a inicialização de nuvem.

Em imagens de nuvem Ubuntu, você deve usar inicialização de nuvem para configurar a partição de troca. Consulte a seguinte página na wiki do Ubuntu para obter mais detalhes: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte de inicialização de nuvem, imagens de máquina virtual implantadas do Azure Marketplace têm um agente de Linux de máquina virtual integrado com o sistema operacional. Este agente permite a máquina virtual interagir com vários serviços Azure. Considerando que você implantou uma imagem padrão do Azure Marketplace, você precisaria fazer o seguinte para configurar corretamente suas configurações de arquivo de troca de Linux:

Localizar e modificar duas entradas no arquivo **/etc/waagent.conf** . Eles controlam a existência de um arquivo de troca dedicada e o tamanho do arquivo de troca. Os parâmetros que você quer para modificar são `ResourceDisk.EnableSwap=N` e`ResourceDisk.SwapSizeMB=0` 

Você precisa alterá-los para o seguinte:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size em MB para atender às suas necessidades} 

Depois de fazer a alteração, você precisará reiniciar o waagent ou sua VM Linux para refletir essas alterações.  Você sabe que as alterações foram implementadas e um arquivo de troca foi criado quando você usa o `free` comando para exibir o espaço livre. O exemplo a seguir tem um arquivo de troca de 512MB criado como resultado de modificar o arquivo waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de programação de e/s para armazenamento de Premium

Com o 2.6.18 Linux núcleo, o padrão de i/o algoritmo de agendamento foi alterado de data limite para CFQ (algoritmo de fila completamente justo). Para padrões de i/o acesso aleatório, há diferença significativo em diferenças de desempenho entre CFQ e data limite.  Para discos baseados em SSD onde o padrão de i/o disco está basicamente sequencial, alternar de volta para o algoritmo NOOP ou prazo pode obter melhor desempenho e/s.

### <a name="view-the-current-io-scheduler"></a>Exibir o Agendador de e/s atual

Use o seguinte comando:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Você verá seguindo saída, que indica o Agendador atual.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/ desenvolvimento/sda) de i/o algoritmo de agendamento

Use os seguintes comandos:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Essa configuração para /dev/sda sozinho não é útil. Ele precisa ser definidas em todos os discos de dados onde i/o sequencial domina o padrão de e/s.  

Você verá a seguinte saída, indicando que grub.cfg recriado com êxito e que o agendador padrão foi atualizado para NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para a família de distribuição Redhat, você só precisará o seguinte comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Usando o Software RAID para atingir superior eu / operações

Se suas cargas de trabalho exigem mais IOps que um único disco pode fornecer, você precisará usar uma configuração de RAID de software de vários discos. Como Azure já realiza resiliência de disco na camada tecidos local, você atingir o nível mais alto de desempenho de uma configuração de distribuição RAID-0.  Você precisa provisionar e criar novos discos no ambiente do Azure e anexá-los a sua VM Linux antes da partição, formatação e montagem das unidades.  Mais detalhes sobre como configurar uma configuração de RAID de software em sua VM Linux no azure podem ser encontradas no documento **[Configurando o Software RAID no Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Próximas etapas

Lembre-se de que, assim como com todas as discussões de otimização, você precisa executar testes antes e após cada alteração para medir o impacto que terá a alteração.  Otimização é um processo passo a passo que terá resultados diferentes em máquinas diferentes em seu ambiente.  O que funciona para uma configuração pode não funcionar para que outras pessoas.

Alguns links úteis para recursos adicionais: 

- [Armazenamento de Premium: Armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md)
- [Guia de usuário do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Otimização de desempenho de MySQL em VMs Linux Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurar o Software RAID no Linux](virtual-machines-linux-configure-raid.md)
