<properties
 pageTitle="Opções de cluster Linux HPC Pack na nuvem | Microsoft Azure"
 description="Saiba mais sobre opções com o Microsoft HPC Pack para criar e gerenciar um Linux computação de alto desempenho cluster (HPC) na nuvem Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Opções com HPC Pack para criar e gerenciar um cluster de HPC no Azure para Linux cargas de trabalho

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra em Opções de utilizar HPC Pack para executar cargas de trabalho de Linux. Também existem opções para executar [cargas de trabalho do Windows HPC com HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Executar um cluster de HPC Pack em VMs do Azure

### <a name="azure-templates"></a>Modelos do Azure


* (Marketplace) [Cluster de HPC Pack para Linux cargas de trabalho](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Início rápido) [Criar um cluster de HPC com Linux nós de computadores](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script de implantação do PowerShell

* [Criar um cluster de HPC Linux com o script de implantação de HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriais

* [Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Executar NAMD com o Microsoft HPC Pack no Linux calcular nós no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Tutorial: Executar ESTRELA-CCM + com Microsoft HPC Pack em um RDMA Linux cluster no Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Gerenciamento de cluster

* [Enviar trabalhos a um cluster de HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gerenciamento de trabalhos em HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Criar clusters RDMA para cargas de trabalho MPI

* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)

