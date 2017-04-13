<properties 
   pageTitle="Como definir um IP estático de particular interno"
   description="Compreendendo IPs estático interno (queda) e como gerenciá-los"
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

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como definir um interno particular endereço IP estático usando o PowerShell (clássico)
Na maioria dos casos, você não precisa especificar um endereço IP estático interno para sua máquina virtual. VMs em uma rede virtual receberão automaticamente um endereço IP interno de um intervalo que você especificar. Mas em alguns casos, especificando um endereço IP estático para uma determinada VM faz sentido. Por exemplo, se sua máquina virtual for vai executar DNS ou será um controlador de domínio. Um endereço IP estático interno permanece com a máquina virtual mesmo por meio de um estado de parada/deprovision. 

> [AZURE.IMPORTANT] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda usando o modelo clássico de implantação. Recomendamos que mais novas implantações de usam o [modelo de implantação do Gerenciador de recursos](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível em um vnet chamado *TestVnet*, execute o seguinte comando do PowerShell e verifique se o valor for *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Se você quiser testar o comando acima em um ambiente seguro acompanhar as diretrizes em [criar uma rede Virtual](virtual-networks-create-vnet-classic-portal.md) para criar um vnet chamado *TestVnet* e certifique-se de que ele usa o espaço de endereço de *10.0.0.0/8* .

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar IP estático interno ao criar uma máquina virtual
O script do PowerShell abaixo cria um novo serviço de nuvem denominado *TestService*, em seguida, recupera uma imagem do Azure, e em seguida, cria uma máquina virtual chamada *TestVM* no serviço de nuvem novo usando a imagem recuperada, define a máquina virtual estar em uma sub-rede denominada *sub-rede-1*e define *10.0.0.7* como IP estático interno para a máquina virtual:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como recuperar informações de IP estático internas para uma máquina virtual
Para exibir as informações de IP estático internas para a máquina virtual criada com o script acima, execute o seguinte comando do PowerShell e observar os valores para o *endereço IP*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um IP interno estático de uma máquina virtual
Para remover o IP interno estático adicionado para a máquina virtual no script acima, execute o seguinte comando do PowerShell:
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar IP estático interno para uma máquina virtual existente
Para adicionar um estático interno IP para a máquina virtual criada usando o script acima, runt ele comandos a seguir:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Próximas etapas

[IP reservado](virtual-networks-reserved-public-ip.md)

[Nível de instância pública IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[IP reservado restante APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
