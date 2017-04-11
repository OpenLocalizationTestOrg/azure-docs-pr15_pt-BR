<properties
 pageTitle="Sobre pesados VMs com Windows | Microsoft Azure"
 description="Obtenha informações básicas e considerações para usar os tamanhos de pesados H série e A8, A9, A10 e A11 Azure para serviços de nuvem e VMs do Windows"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sobre H série e pesados VMs de uma série

Veja aqui informações básicas e algumas considerações para usar a mais recente série H Azure e as anteriores A8, A9, A10 e A11 instâncias, também conhecido como *pesados* instâncias. Este artigo se concentra nos usando estas instâncias para VMs do Windows. Este artigo também está disponível para [VMs Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA

Você pode criar clusters de instâncias de habilitados para RDMA Windows Server e implantar uma das implementações MPI com suporte para tirar proveito da rede RDMA do Azure. Esta rede baixa latência, alta produtividade é reservado MPI somente para tráfego.

* **Sistema Operacional**
    * **Máquinas virtuais** - Windows Server 2012 R2, Windows Server 2012
    * **Serviços de nuvem** - Windows Server 2012 R2, Windows Server 2012, família SO de convidado do Windows Server 2008 R2

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 ou posterior, a biblioteca de MPI Intel 5. x

Com suporte implementações da MPI usam a interface direta de rede do Microsoft para se comunicar entre as instâncias. Consulte [Configurar um cluster de Windows RDMA com HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [tarefas de várias instâncias de uso para executar aplicativos de Interface de transmissão de mensagens (MPI) em lote do Azure](../batch/batch-mpi.md) para opções de implantação e etapas de configuração de amostra.


>[AZURE.NOTE]Em VMs pesados habilitados para RDMA, a extensão de HpcVmDrivers deve ser adicionada às VMs para instalar os drivers de dispositivos de rede do Windows que são necessárias para conectividade RDMA. Na maioria das implantações, a extensão de HpcVmDrivers é adicionada automaticamente. Se você precisar adicionar a extensão por conta própria, consulte [extensões de máquina virtual gerenciar](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Considerações para HPC Pack e Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito da Microsoft e solução de gerenciamento de trabalho, não é necessário usar as instâncias de pesados com o Windows Server. No entanto, é uma opção para criar um cluster de cálculo no Azure para executar aplicativos MPI baseado no Windows e outras cargas de trabalho HPC. HPC Pack 2012 R2 e versões posteriores incluem um ambiente de tempo de execução para MS-MPI que pode usar a rede Azure RDMA quando implantado em VMs habilitados para RDMA.

Para obter mais informações e listas de verificação para usar as instâncias de pesados com HPC Pack no Windows Server, consulte [Configurar um cluster de Windows RDMA com HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços dos tamanhos pesados, consulte [máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [preços de serviços de nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para capacidades de armazenamento e detalhes de disco, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implantar e usar pesados instâncias com HPC Pack no Windows, consulte [Configurar um cluster de Windows RDMA com HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obter informações sobre como usar instâncias A8 e A9 para executar aplicativos MPI com lotes do Azure, consulte [tarefas de várias instâncias de uso para executar aplicativos de Interface de transmissão de mensagens (MPI) em lote do Azure](../batch/batch-mpi.md).
