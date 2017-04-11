<properties
    pageTitle="Anexar um disco de dados a uma VM Linux | Microsoft Azure"
    description="Como anexar o disco de dados novo ou existente a uma VM Linux no portal do Azure usando o modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM Linux no portal do Azure

Este artigo mostra como anexar discos novos e existentes a um computador de virtual Linux por meio do portal Azure. Você também pode [anexar um disco de dados para uma máquina de virtual do Windows no portal do Azure](virtual-machines-windows-attach-disk-portal.md). Antes de fazer isso, analise estas dicas:

- O tamanho da máquina virtual controla quantos discos de dados, você pode anexar. Para obter detalhes, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).
- Para usar o armazenamento de Premium, você precisará de uma máquina virtual série GS ou série DS. Você pode usar discos de contas de armazenamento Premium e padrão com essas máquinas virtuais. Armazenamento de Premium está disponível em determinadas regiões. Para obter detalhes, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).
- Discos anexados a máquinas virtuais são realmente. vhd arquivos em uma conta de armazenamento do Azure. Para obter detalhes, consulte [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).
- Para um novo disco, você não precisa criá-lo primeiro porque Azure cria quando você anexá-lo.
- Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um VHD que já está lá, se ela não está conectada a outra máquina virtual ou carregar o arquivo de seu próprio VHD para a conta de armazenamento.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Depois que o disco for adicionado, você precisa prepará-lo para uso. Consulte no sistema operacional da máquina virtual: "como: inicializar um novo disco de dados no Linux" neste [artigo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
