<properties 
   pageTitle="Como mover uma instância de máquina virtual ou função para uma sub-rede diferente"
   description="Saiba como mover VMs e instâncias de função para uma sub-rede diferente"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Como mover uma instância de máquina virtual ou função para uma sub-rede diferente

Você pode usar o PowerShell para mover suas VMs de uma sub-rede para outro na mesma rede virtual (VNet). Instâncias de função podem ser movidas a CSCFG de edição, em vez de usar o PowerShell.

>[AZURE.NOTE] Este artigo contém informações que é relativo Azure clássicas somente as implantações.

Por que mover VMs para outra sub-rede? Migração de sub-rede é útil quando a sub-rede mais antiga é muito pequena e não pode ser expandida devido a existentes VMs em execução nessa sub-rede. Nesse caso, você pode criar uma nova, maior sub-rede e migrar VMs para a nova sub-rede, em seguida, após a conclusão da migração, você pode excluir a antiga sub-rede vazia.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Como mover uma máquina virtual para outra sub-rede

Para mover uma máquina virtual, execute o cmdlet Set-AzureSubnet PowerShell, usando o exemplo abaixo como um modelo. No exemplo abaixo, nós estão adotando TestVM de sua sub-rede presente, sub-rede-2. Certifique-se de editar o exemplo para refletir o seu ambiente. Observe que, sempre que você executar o cmdlet Update-AzureVM como parte de um procedimento, ele será reiniciar sua máquina virtual como parte do processo de atualização.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Se você tiver especificado um IP estático de particular interno para sua máquina virtual, você precisará desmarcar essa configuração antes de você pode mover a máquina virtual para uma nova sub-rede. Nesse caso, use o seguinte:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover uma instância da função para outra sub-rede

Para mover uma instância de função, edite o arquivo CSCFG. No exemplo abaixo, nós estão movendo "Role0" em rede virtual *VNETName* de sua sub-rede presente à *sub-rede 2*. Porque a instância de função já foi implantada, você vai alterar apenas o nome de sub-rede = sub-rede-2. Certifique-se de editar o exemplo para refletir o seu ambiente.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
