<properties
    pageTitle="Sobre discos e VHDs para Linux VMs | Microsoft Azure"
    description="Saiba mais sobre as Noções básicas de discos e VHDs para máquinas virtuais de Linux no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Sobre discos e VHDs para Azure máquinas virtuais

Assim como qualquer outro computador, máquinas virtuais no Azure usar discos como um local para armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais Azure ter pelo menos dois discos – um disco do sistema operacional Linux (no caso de uma VM Linux) e um disco temporário. O disco de sistema operacional é criado a partir de uma imagem e o disco de sistema operacional e a imagem são realmente rígido VHDs (discos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também pode ter um ou mais discos de dados, que também são armazenados como VHDs. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

> [AZURE.NOTE] Se você tiver alguns momentos, ajude-na melhorar a documentação de máquina virtual do Azure Linux fazendo esta [pesquisa rápida](https://aka.ms/linuxdocsurvey) de suas experiências. Cada resposta nos ajudam a ajuda você executar seu trabalho.

## <a name="operating-system-disk"></a>Disco do sistema operacional

Cada máquina virtual tem um disco de sistema operacional conectado. Ela tem registrado como uma unidade SATA e rotulado como a unidade c: por padrão. Este disco tem a capacidade máxima de 1023 GB (Gigabytes). 

##<a name="temporary-disk"></a>Disco temporário

O disco temporário é criado automaticamente para você. Em máquinas virtuais de Linux, o disco é geralmente /dev/sdb é formatado e montado /mnt/resource pelo agente de Linux do Azure.

O tamanho do disco temporário varia com base no tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos para máquinas virtuais Linux](virtual-machines-linux-sizes.md).

>[AZURE.WARNING] Não armazene dados no disco temporário. Ele fornece armazenamento temporário para aplicativos e processos e destina-se apenas armazenar dados como arquivos de página ou troca. 

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária em máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de dados

Um disco de dados é um VHD que está anexado a uma máquina virtual para armazenar dados de aplicativo ou outros dados que você precisa manter. Discos de dados são registrados como unidades SCSI e são rotulados com uma letra que você escolher.  Cada disco de dados tem a capacidade máxima de 1023 GB. O tamanho da máquina virtual determina quantos discos de dados, você pode anexar a ele e o tipo de armazenamento que você pode usar para hospedar os discos.

>[AZURE.NOTE] Para obter mais detalhes sobre capacidades de máquinas virtuais, consulte [tamanhos para máquinas virtuais Linux](virtual-machines-linux-sizes.md).

Azure cria um disco de sistema operacional quando você cria uma máquina virtual a partir de uma imagem. Se você usar uma imagem que inclui discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, você adicionar discos de dados, depois de criar a máquina virtual.

Você pode adicionar discos de dados a uma máquina virtual a qualquer momento, **como anexar** o disco na máquina virtual. Você pode usar um VHD que você carregou ou copiados para sua conta de armazenamento que ou Azure cria para você. Anexar um disco de dados associa o arquivo VHD de sua conta de armazenamento da máquina virtual, colocando uma concessão no VHD não pode ser excluído do armazenamento enquanto ele ainda está conectado.

## <a name="about-vhds"></a>Sobre VHDs

Os VHDs usados no Azure são arquivos. vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. Para obter detalhes sobre blobs de página, consulte [blobs de bloco de compreensão e blobs de página](https://msdn.microsoft.com/library/ee691964.aspx). Para obter detalhes sobre o armazenamento de premium, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure máquina virtual](../storage/storage-premium-storage.md).

Azure suporta o formato VHD de disco fixo. O formato fixo apresenta o disco lógico linear dentro do arquivo, para que esse deslocamento de disco X é armazenado no deslocamento de blob X. Um pequeno rodapé no final do blob descreve as propriedades do VHD. Geralmente, o formato fixo desperdiça espaço porque a maioria dos discos tem grandes intervalos não utilizados neles. No entanto, o Azure armazena arquivos. vhd em um formato esparso, para que você receba os benefícios de discos fixos e dinâmicos ao mesmo tempo. Para obter mais detalhes, consulte [Introdução aos discos virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os arquivos. vhd no Azure que você deseja usar como uma fonte para criar discos ou imagens são somente leitura. Quando você cria um disco ou uma imagem, o Azure faz cópias dos arquivos. vhd. Essas cópias podem ser somente leitura ou leitura e gravação, dependendo de como você pode usar o VHD.

Quando você cria uma máquina virtual a partir de uma imagem, o Azure cria um disco da máquina virtual que é uma cópia do arquivo. vhd de origem. Para proteger contra exclusão acidental, o Azure colocará uma concessão em qualquer arquivo de VHD de fonte que é usado para criar uma imagem, um disco de sistema operacional ou um disco de dados.

Antes de excluir um arquivo. vhd de origem, você precisará remover a concessão excluindo o disco ou a imagem. Para excluir um arquivo. vhd que está sendo usado por uma máquina virtual como um disco de sistema operacional, você pode excluir a máquina virtual, o disco de sistema operacional, e o arquivo de origem. vhd ao mesmo tempo, excluindo a máquina virtual e excluir todos discos associados. No entanto, a exclusão de um arquivo. vhd que é uma fonte de um disco de dados requer várias etapas em uma ordem de conjunto – desanexar o disco na máquina virtual, excluir o disco e exclua o arquivo. vhd.

>[AZURE.WARNING] Se você excluir um arquivo. vhd de origem do armazenamento ou excluir sua conta de armazenamento, a Microsoft não pode recuperar esses dados para você.


## <a name="troubleshooting"></a>Solução de problemas
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas

-  [Anexar um disco](virtual-machines-linux-add-disk.md) adicionar armazenamento adicional para sua máquina virtual.
-  [Configurar o software RAID](virtual-machines-linux-configure-raid.md) para redundância.
-  [Capturar uma máquina virtual Linux](virtual-machines-linux-classic-capture-image.md) para que você pode implantar rapidamente VMs adicionais.


