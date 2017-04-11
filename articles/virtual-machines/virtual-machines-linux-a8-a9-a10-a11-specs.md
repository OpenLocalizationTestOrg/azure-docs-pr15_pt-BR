<properties
 pageTitle="Sobre pesados VMs com Linux | Microsoft Azure"
 description="Obtenha informações básicas e considerações para usar os tamanhos de pesados H série e A8, A9, A10 e A11 para Linux VMs"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sobre H série e pesados VMs de uma série 

Veja aqui informações básicas e algumas considerações para usar a mais recente série H Azure e as anterior A8, A9, A10 e A11 tamanhos, também conhecido como *pesados* instâncias. Este artigo aborda sobre como usar esses tamanhos para VMs Linux. Este artigo também está disponível para [VMs do Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA

Você pode criar clusters de habilitados para RDMA VMs Linux que executar um dos seguintes suporte distribuições Linux HPC e implementação de MPI suporte para tirar proveito da rede RDMA do Azure. Consulte [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md) para opções de implantação e etapas de configuração de amostra.

* **Distribuições** - você deve implantar VMs do SUSE Linux Enterprise Server (SLES) capaz de RDMA ou baseada em OpenLogic CentOS HPC imagens do Azure Marketplace. Somente as imagens a seguir Marketplace suportam os drivers Linux RDMA necessários:

    * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
    
    * SLES 12 para HPC, SLES 12 para HPC (Premium)
    
    * Baseada em centOS 7.1 HPC
    
    * Baseada em centOS 6.5 HPC
    
    >[AZURE.NOTE]Para VMs H série, recomendamos em um SLES 12 SP1 para imagem HPC ou imagem baseada em CentOS HPC 7.1.
    >
    >Nas imagens baseado em CentOS HPC, atualizações de núcleo estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers Linux RDMA são distribuídos como um pacote RPM e atualizações de driver podem não funcionar se o núcleo for atualizado.

* **MPI** - Intel MPI biblioteca 5. x

    Dependendo da imagem Marketplace você escolher, instalação de licenciamento, separada, ou pode da configuração da Intel MPI ser necessárias, da seguinte maneira: 
    
    * **SLES 12 SP1 para imagem HPC** - instalar os pacotes de Intel MPI distribuídos na VM executando o seguinte comando:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 para imagem HPC** - separadamente, você deve se registrar para baixar e instalar Intel MPI. Consulte o [guia de instalação do Intel MPI biblioteca](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Imagens HPC baseado em centOS** - Intel MPI 5.1 já está instalado.  

    Configurações adicionais do sistema é necessário para executar MPI trabalhos em VMs agrupadas. Por exemplo, em um cluster de VMs, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Considerações para HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito da Microsoft e solução de gerenciamento de trabalho, fornece uma opção para usar as instâncias de pesados com Linux. As versões mais recentes de suporte de HPC Pack 2012 R2 várias distribuições Linux para executar em calcular nós implantado em VMs do Azure, gerenciado por um nó principal do Windows Server. Conosco de computação Linux habilitados para RDMA executando Intel MPI, HPC Pack pode agendar e executar Linux MPI aplicativos que acessam a rede RDMA. Para começar, consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Considerações de topologia de rede

* Em habilitados para RDMA VMs Linux no Azure, Eth1 é reservada para tráfego de rede RDMA. Não altere qualquer configuração de Eth1 ou qualquer informação no arquivo de configuração que fazem referência a esta rede. Eth0 é reservada para tráfego de rede Azure regular.

* No Azure, não há suporte para IP sobre InfiniBand (IB). Há suporte para apenas RDMA sobre IB.

## <a name="rdma-driver-updates-for-sles-12"></a>Atualizações de driver RDMA para SLES 12

Depois de criar uma máquina virtual com base em uma imagem de SLES 12 HPC, talvez seja necessário atualizar os drivers RDMA nas VMs RDMA conectividade de rede. 

>[AZURE.IMPORTANT]Essa etapa é **necessária** para SLES 12 para implantações de HPC VM em todas as regiões Azure. 
>Essa etapa não é necessária se você implantar um SLES 12 SP1 para HPC, baseada em CentOS HPC 7.1 ou baseado em CentOS 6.5 HPC máquina virtual. 

Antes de atualizar os drivers, interrompa todos os processos de **zypper** ou qualquer que bloquear os bancos de dados de repo SUSE na máquina virtual. Caso contrário, os drivers não podem atualizar corretamente.  

Para atualizar os drivers de Linux RDMA em cada máquina virtual, execute um dos seguintes conjuntos de comandos do Azure do computador cliente.

**SLES 12 para HPC VM provisionado no modelo clássico de implantação**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 para HPC VM provisionado no modelo de implantação do Gerenciador de recursos**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Pode levar alguns instantes para instalar os drivers e o comando retornará sem saída. Após a atualização, sua máquina virtual será reiniciado e deve estar pronto para uso em vários minutos.

### <a name="sample-script-for-driver-updates"></a>Script de exemplo para atualizações de driver

Se você tiver um cluster de 12 SLES para HPC VMs, você pode script a atualização do driver em todos os nós no seu cluster. Por exemplo, o script a seguir atualiza os drivers em um cluster de 8 nós.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços dos tamanhos pesados, consulte [máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Para capacidades de armazenamento e detalhes de disco, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implantar e usar tamanhos pesados com RDMA no Linux, consulte [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).


