<properties
   pageTitle="Implantar vários NIC VMs usando o PowerShell no modelo clássico de implantação | Microsoft Azure"
   description="Saiba como implantar vários NIC VMs usando o PowerShell no modelo clássico de implantação"
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

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Implantar várias VMs NIC (clássico) usando o PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Várias placas de rede Ativar separação de tipos de tráfego através de NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outro se comunica somente com os recursos internos não conectados à Internet. A capacidade de separar tráfego de rede entre várias placas de rede é necessária para muitos dispositivos virtuais de rede, como soluções de otimização de WAN e entrega de aplicativos.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Atualmente, você não pode ter VMs com uma única NIC e VMs com várias placas de rede no serviço de nuvem do mesmo. Portanto, você precisa implementar os servidores de back-end em um serviço de nuvem diferentes diferente e todos os outros componentes no cenário. As etapas abaixo usam um serviço de nuvem chamado *IaaSStory* para os recursos principais e *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar os servidores de back-end, você precisa implantar o serviço de nuvem principal com todos os recursos necessários para esse cenário. No mínimo, você precisa criar uma rede virtual com uma sub-rede para back-end. Visite a [criar uma rede virtual usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) para saber como implantar uma rede virtual.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implantar VMs back-end

As VMs de back-end dependem da criação dos recursos listados abaixo.

- **Sub-rede de back-end**. Os servidores de banco de dados será parte de uma sub-rede separada, separar tráfego. O script a seguir espera nesta sub-rede existir em um vnet chamado *WTestVnet*.
- **Conta de armazenamento para discos de dados**. Para obter melhor desempenho, os discos de dados nos servidores de banco de dados usará tecnologia de unidade (SSD) de estado sólido, que requer uma conta de armazenamento premium. Verifique se o local Azure implantar para dar suporte ao armazenamento de premium.
- **Disponibilidade definida**. Todos os servidores de banco de dados serão adicionados a uma única disponibilidade definido, para garantir que pelo menos uma das VMs é para cima e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 - começar seu script

Você pode baixar o script PowerShell completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga as etapas abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis a seguir com base em seu grupo de recursos existente implantado acima em [pré-requisitos](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Altere os valores das variáveis a seguir com base nos valores que você deseja usar para sua implantação de back-end.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2 - criar recursos necessários para suas VMs

Você precisa criar um novo serviço de nuvem e uma conta de armazenamento para os discos de dados para todas as VMs. Você também precisa especificar uma imagem e uma conta de administrador local para VMs. Para criar esses recursos, execute as etapas a seguir.

1. Crie um novo serviço de nuvem.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Crie uma nova conta de armazenamento premium.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Defina a conta de armazenamento criada acima como a conta de armazenamento atual para sua assinatura.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Selecione uma imagem para a máquina virtual.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Defina credenciais de conta de administrador local.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Etapa 3 - criar VMs

Você precisa usar um loop para criar quantas VMs conforme desejado e criar a placas de rede e VMs necessárias dentro do loop. Para criar a placas de rede e VMs, execute as etapas a seguir.

1. Iniciar um `for` loop para repetir os comandos para criar uma máquina virtual e duas placas de rede quantas vezes conforme necessário, com base no valor da `$numberOfVMs` variável.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Criar um `VMConfig` objeto especificando a imagem, o tamanho e a disponibilidade definido para a máquina virtual.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Provisione a máquina virtual como uma máquina virtual do Windows.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Definir o padrão de NIC e atribua um endereço IP estático.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Adicione uma segunda NIC para cada máquina virtual.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Crie discos de dados para cada máquina virtual.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Crie cada máquina virtual e encerrar o loop.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Etapa 4 - execute o script

Agora que você baixou e alterado o script com base em suas necessidades, runt que ele script para criar VMs do banco de dados de back-end com várias placas de rede.

1. Salve seu script e executá-la a partir do prompt de comando do **PowerShell** ou **PowerShell ISE**. Você verá a saída inicial, conforme mostrado abaixo.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Preencha as informações necessárias no prompt de credenciais e clique em **Okey**. A saída abaixo será exibida.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
