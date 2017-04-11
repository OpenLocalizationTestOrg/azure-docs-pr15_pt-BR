<properties
    pageTitle="Conectar Linux VMs em um serviço de nuvem | Microsoft Azure"
    description="Conecte máquinas virtuais de Linux criadas com o modelo de implantação clássico para um serviço de nuvem Azure ou uma rede virtual."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Conectar máquinas virtuais de Linux criadas com o modelo de implantação clássico com um serviço de rede ou nuvem virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Máquinas virtuais de Linux criadas com o modelo clássico de implantação são sempre colocadas em um serviço de nuvem. O serviço de nuvem atua como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar em uma rede virtual, mas que não é um requisito. Você também pode [se conectar a máquinas virtuais do Windows com um serviço de rede ou nuvem virtual](virtual-machines-windows-classic-connect-vms.md).

Se um serviço de nuvem não estiver em uma rede virtual, ele se chama um serviço de nuvem *autônomo* . As máquinas virtuais em um serviço de nuvem autônomo podem apenas se comunicar com outras máquinas virtuais usando nomes DNS públicos das outras máquinas virtuais e o tráfego viaja pela Internet. Se um serviço de nuvem estiver em uma rede virtual, máquinas virtuais desse serviço de nuvem pode se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar qualquer tráfego pela Internet.

Se você colocar suas máquinas virtuais no serviço de nuvem mesmo autônomo, você ainda pode usar balanceamento de carga e conjuntos de disponibilidade. Para obter detalhes, consulte [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-linux-manage-availability.md)e [máquinas virtuais de balanceamento de carga](virtual-machines-linux-load-balance.md) . No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo à sua rede local. Aqui está um exemplo:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Próximas etapas

Depois de criar uma máquina virtual, é uma boa ideia [Adicionar um disco de dados](virtual-machines-linux-classic-attach-disk.md) para que seus serviços e cargas de trabalho têm um local para armazenar dados. 



