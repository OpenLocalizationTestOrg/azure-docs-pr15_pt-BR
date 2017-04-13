<properties
 pageTitle="Opções de cluster Windows HPC Pack na nuvem | Microsoft Azure"
 description="Saiba mais sobre opções com o Microsoft HPC Pack para criar e gerenciar um Windows computação de alto desempenho cluster (HPC) na nuvem Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Opções com HPC Pack para criar e gerenciar um cluster de HPC do Windows Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo aborda opções para criar clusters HPC Pack para executar cargas de trabalho do Windows. Também existem opções para a criação de clusters para executar [cargas de trabalho de HPC Linux com HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Executar um cluster de HPC Pack em VMs do Azure

### <a name="azure-templates"></a>Modelos do Azure

* (Marketplace) [Cluster de HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster de HPC Pack para cargas de trabalho do Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Início rápido) [Criar um cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Início rápido) [Criar um cluster de HPC com imagem de nó de computação personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imagens de máquina virtual Azure

* [Pacote de HPC no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nó de computação HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [HPC Pack calcular nó com o Excel no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script de implantação do PowerShell

* [Criar um cluster de HPC com o script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriais

* [Tutorial: Começar com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Implantação manual com o portal do Azure

* [Configurar o nó principal de um cluster de HPC Pack em uma máquina virtual do Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Gerenciamento de cluster

* [Gerenciar nós de computação em um cluster de HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Ampliar ou reduzir recursos de computação Azure em um cluster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Enviar trabalhos a um cluster de HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gerenciamento de trabalhos em HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Adicionar nós de função de trabalho a um cluster de HPC Pack


* [Intermitente para instâncias de trabalho Azure com HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configurar um cluster híbrida com HPC Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adicionar nós do Azure "intermitente" para um nó de cabeça HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Integrar com o Azure lote 

* [Intermitente ao lote Azure com HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Criar clusters RDMA para cargas de trabalho MPI

* [Configurar um cluster de Windows RDMA com HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
