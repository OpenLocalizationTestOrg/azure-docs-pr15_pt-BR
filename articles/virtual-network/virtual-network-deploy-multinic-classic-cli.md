<properties
   pageTitle="Implantar vários NIC VMs utilizando a CLI Azure no modelo clássico de implantação | Microsoft Azure"
   description="Saiba como implantar vários NIC VMs utilizando a CLI Azure no modelo clássico de implantação"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Implantar vários NIC VMs (clássico) usando a CLI do Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Várias placas de rede Ativar separação de tipos de tráfego através de NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outro se comunica somente com os recursos internos não conectados à Internet. A capacidade de separar tráfego de rede entre várias placas de rede é necessária para muitos dispositivos virtuais de rede, como soluções de otimização de WAN e entrega de aplicativos.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Atualmente, você não pode ter VMs com uma única NIC e VMs com várias placas de rede no serviço de nuvem do mesmo. Portanto, você precisa implementar os servidores de back-end em um serviço de nuvem diferentes diferente e todos os outros componentes no cenário. As etapas abaixo usam um serviço de nuvem chamado *IaaSStory* para os recursos principais e *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar os servidores de back-end, você precisa implantar o serviço de nuvem principal com todos os recursos necessários para esse cenário. No mínimo, você precisa criar uma rede virtual com uma sub-rede para back-end. Visite a [criar uma rede virtual usando a CLI do Azure](virtual-networks-create-vnet-classic-cli.md) para saber como implantar uma rede virtual.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implantar VMs back-end

As VMs de back-end dependem da criação dos recursos listados abaixo.

- **Conta de armazenamento para discos de dados**. Para obter melhor desempenho, os discos de dados nos servidores de banco de dados usará tecnologia de unidade (SSD) de estado sólido, que requer uma conta de armazenamento premium. Verifique se o local Azure implantar para dar suporte ao armazenamento de premium.
- **NICs**. Cada máquina virtual terá duas NICs, uma para acesso de banco de dados e outra para gerenciamento.
- **Disponibilidade definida**. Todos os servidores de banco de dados serão adicionados a uma única disponibilidade definido, para garantir que pelo menos uma das VMs é para cima e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 - começar seu script

Você pode baixar o script completo bash usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Siga as etapas abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis a seguir com base em seu grupo de recursos existente implantado acima em [pré-requisitos](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Altere os valores das variáveis a seguir com base nos valores que você deseja usar para sua implantação de back-end.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2 - criar recursos necessários para suas VMs

1. Crie um novo serviço de nuvem para todas as VMs de back-end. Observe o uso da `$backendCSName` variável para o nome do grupo de recursos, e `$location` para a região do Azure.

        azure service create --serviceName $backendCSName \
            --location $location

2. Crie uma conta de armazenamento premium para o sistema operacional e dados discos a ser usado por sua VMs.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Etapa 3 - criar VMs com várias placas de rede

1. Iniciar um loop para criar várias VMs, com base na `numberOfVMs` variáveis.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Para cada máquina virtual, especifique o nome e o endereço IP de cada uma das duas NICs.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Crie a máquina virtual. Observe o uso da `--nic-config` parâmetro, contendo uma lista de todas as NICs com nome, sub-rede e endereço IP.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Para cada máquina virtual, crie dois discos de dados.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Etapa 4 - execute o script

Agora que você baixou e alterado o script com base em suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias placas de rede.

1. Salve seu script e executá-la a partir do terminal **Bash** . Você verá a saída inicial, conforme mostrado abaixo.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Após alguns minutos, a execução vai terminar e você verá o restante da saída conforme mostrado abaixo.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
