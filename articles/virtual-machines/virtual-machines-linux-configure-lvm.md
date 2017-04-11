<properties 
    pageTitle="Configurar LVM em uma máquina virtual executando Linux | Microsoft Azure" 
    description="Saiba como configurar LVM no Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar LVM em uma máquina virtual de Linux no Azure

Este documento sobre como configurar o Gerenciador de Volume lógico (LVM) na sua máquina virtual Azure. Embora seja viável para configurar LVM em qualquer disco anexado à máquina virtual, por padrão a maioria das imagens de nuvem não terá LVM configurado no disco sistema operacional. Isso é para evitar problemas com grupos de volumes duplicados se o disco de sistema operacional é nunca anexado para outra máquina virtual do mesmo distribuição e tipo, ou seja, durante um cenário de recuperação. Portanto, é recomendável somente para usar o LVM nos discos de dados.


## <a name="linear-vs-striped-logical-volumes"></a>Linear versus volumes lógicos distribuídos

LVM pode ser usado para combinar um número de discos físicos em um único volume de armazenamento. Por padrão LVM geralmente criará volumes lógicos lineares, significando que o armazenamento físico é concatenado. Nesse caso operações de leitura/gravação serão normalmente apenas enviadas para um único disco. Em contraste, podemos também pode criar volumes lógicos distribuídos onde leituras e gravações são distribuídas em vários discos contidos no grupo de volume (isto é semelhante a RAID0). Por razões de desempenho, é provável que você desejará seus volumes lógicos de distribuição para que leituras e gravações utilizam todos os discos de dados anexada.

Este documento descreverá como combinar vários discos de dados em um grupo único volume e, em seguida, crie um volume lógico distribuído. As etapas a seguir são um pouco generalizadas para trabalhar com a maioria das distribuições. Na maioria dos casos os utilitários e fluxos de trabalho de gerenciamento de LVM no Azure não são bem diferentes de outros ambientes. Como de costume, também consulte o fornecedor Linux para documentação e práticas recomendadas para usar o LVM com sua distribuição específico.


## <a name="attaching-data-disks"></a>Anexar discos de dados
Normalmente, um desejará começar com dois ou mais discos de dados vazia ao usar LVM. Com base nas suas necessidades de IO, você pode optar por anexar discos que são armazenados em nosso armazenamento padrão, com até 500 IO/ps por disco ou nosso armazenamento Premium com até 5000 IO/ps por disco. Este artigo não entrará em detalhes sobre como provisionar e anexar discos de dados a uma máquina virtual Linux. Consulte o Microsoft Azure artigo [anexar um disco](virtual-machines-linux-add-disk.md) para obter instruções detalhadas sobre como anexar um disco de dados vazia ao computador virtual Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instalar os utilitários LVM

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS & Linux Oracle**

        # sudo yum install lvm2

- **12 de SLES e openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    Em SLES11 também edite /etc/sysconfig/lvm e definir `LVM_ACTIVATED_ON_DISCOVERED` "Habilitar":

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurar LVM
Neste guia vamos supor que você conectou três discos de dados, vamos nos referir como `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Observe que esses nem sempre podem ser os mesmos nomes de caminho na sua máquina virtual. Você pode executar '`sudo fdisk -l`' ou comando semelhante para listar os discos disponíveis.

1. Prepare os volumes físicos:

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Crie um grupo de volume. Neste exemplo estamos ligando o volume grupo "dados-vg01":

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Crie os volumes lógicos. O comando abaixo estamos criará um único volume lógico chamado "dados-lv01" para o grupo de todo o volume do período, mas observe que também é possível criar vários volumes lógicos no grupo de volume.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formatar o volume lógico

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Com o uso de SLES11 "-t ext3" em vez de ext4. SLES11 suporta apenas o acesso somente leitura para sistemas de arquivos ext4.


## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de arquivos a /etc/fstab.

**Cuidado:** O arquivo /etc/fstab de edição incorretamente pode resultar em um sistema de impossibilidade. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este arquivo. Também é recomendável que um backup do arquivo /etc/fstab é criado antes de editar.

1. Crie o ponto de montagem desejado para seu novo sistema de arquivos, por exemplo:

        # sudo mkdir /data


2. Localize o caminho do volume lógico

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Abra /etc/fstab em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Em seguida, salve e feche o /etc/fstab.


4. Teste se entrada /etc/fstab está correta:

        # sudo mount -a

    Se esse comando resulta em uma mensagem de erro Verifique a sintaxe no arquivo /etc/fstab.

    Próxima execução a `mount` comando para garantir que o sistema de arquivos é montado:

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Opcional) Parâmetros de inicialização de Failsafe no /etc/fstab.

    Muitas distribuições incluem a `nobootwait` ou `nofail` montar parâmetros que podem ser adicionados ao arquivo /etc/fstab. Esses parâmetros permitem para falhas ao montar um sistema de arquivo específico e permitir que o sistema Linux continuar a inicializar mesmo se não for possível montar corretamente o sistema de arquivos RAID. Consulte a documentação da sua distribuição para obter mais informações sobre esses parâmetros.

    Exemplo (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
