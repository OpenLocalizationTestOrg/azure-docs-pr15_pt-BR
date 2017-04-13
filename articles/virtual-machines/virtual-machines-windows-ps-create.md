<properties
    pageTitle="Criar uma VM Azure usando o PowerShell | Microsoft Azure"
    description="Usar o PowerShell do Azure e Gerenciador de recursos do Azure para criar facilmente uma nova máquina virtual executando o Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Criar uma máquina de virtual do Windows usando o Gerenciador de recursos e PowerShell

Este artigo mostra como criar rapidamente uma máquina Virtual do Azure executando o Windows Server e os recursos necessários usando o [Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md) e PowerShell. 

Todas as etapas neste artigo são necessários para criar uma máquina virtual e ele deve levar cerca de 30 minutos para fazer as etapas. Substitua valores de parâmetro de exemplo na comandos com nomes que fazem sentido para o seu ambiente.

## <a name="step-1-install-azure-powershell"></a>Etapa 1: Instalar o PowerShell Azure

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.
        
## <a name="step-2-create-a-resource-group"></a>Etapa 2: Criar um grupo de recursos

Todos os recursos devem estar contidas em um grupo de recursos, então, vamos criar isso primeiro.  

1. Obter uma lista de locais disponíveis onde os recursos podem ser criados.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Defina o local para os recursos. Este comando define o local para **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Crie um grupo de recursos. Esse comando cria o grupo de recursos denominado **myResourceGroup** no local que você definir.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Etapa 3: Criar uma conta de armazenamento

Uma [conta de armazenamento](../storage/storage-introduction.md) é necessária para armazenar o disco rígido virtual que é usado pela máquina virtual que você criar. Nomes de conta de armazenamento devem estar entre 3 e 24 caracteres e podem conter números e apenas letras minúsculas.

1. Teste o nome da conta de armazenamento para exclusividade. Este comando testa o nome **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Se esse comando retorna **True**, seu nome proposto é exclusivo dentro do Azure. 
    
2. Agora, crie a conta de armazenamento.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Etapa 4: Criar uma rede virtual

Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie uma sub-rede para a rede virtual. Esse comando cria uma sub-rede denominada **mySubnet** com um prefixo de endereço de 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. Agora, crie a rede virtual. Esse comando cria uma rede virtual chamada **myVnet** usando a sub-rede que você criou e um prefixo de endereço de **10.0.0.0/16**.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Etapa 5: Criar uma interface de rede e endereços IP pública

Para permitir a comunicação com máquina virtual na rede virtual, você precisará de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o endereço IP público. Esse comando cria um endereço IP público denominado **myPublicIp** com um método de alocação de **dinâmico**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Crie a interface de rede. Esse comando cria uma interface de rede chamada **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Etapa 6: Criar uma máquina virtual

Agora que você tem todas as partes, é hora de criar a máquina virtual.

1. Execute este comando para definir o nome da conta de administrador e a senha para a máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    A senha deve estar em 12-123 caracteres e tiver um caractere de maiusculas, minúsculas pelo menos um caractere, um número e um caractere especial. 
        
2. Crie o objeto de configuração da máquina virtual. Esse comando cria um objeto de configuração chamado **myVmConfig** que define o nome da máquina virtual e o tamanho da máquina virtual.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Consulte [tamanhos para máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para uma lista de tamanhos disponíveis para uma máquina virtual.
    
3. Defina configurações de sistema operacional para a máquina virtual. Este comando define o nome do computador, tipo de sistema operacional e credenciais de conta para a máquina virtual.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Defina a imagem a ser usada para provisionar a máquina virtual. Este comando define a imagem do Windows Server para usar para a máquina virtual. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Para obter mais informações sobre como selecionar imagens para usar, consulte [navegar e selecionar imagens de máquina virtual do Windows no Azure com PowerShell ou CLI](virtual-machines-windows-cli-ps-findimage.md).
        
5. Adicione a interface de rede que você criou na configuração.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Defina o nome e local do disco rígido máquina virtual. O arquivo de disco rígido virtual está armazenado em um contêiner. Esse comando cria o disco em um contêiner chamado **vhds/WindowsVMosDisk.vhd** na conta de armazenamento que você criou.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Adicione as informações de disco do sistema operacional para a configuração de máquina virtual. Substitua o valor de **$diskName** com um nome para o disco de sistema operacional. Crie a variável e adicione as informações do disco na configuração.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Finalmente, crie a máquina virtual.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, a próxima etapa seria examinar [implantações de grupo de recursos de solução de problemas com portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerenciar a máquina virtual que você criou revisando [máquinas virtuais de gerenciar usando o Gerenciador de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).
- Aproveitar as vantagens de usar um modelo para criar uma máquina virtual usando as informações em [criar um computador de virtual do Windows com um modelo do Gerenciador de recursos](virtual-machines-windows-ps-template.md)
