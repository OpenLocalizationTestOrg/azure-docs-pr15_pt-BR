<properties 
   pageTitle="Instância nível IP público (ILPIP) | Microsoft Azure"
   description="Compreendendo ILPIP (PIP) e como gerenciá-los"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Instância público IP visão geral de nível
Uma instância nível público IP (ILPIP) é um endereço IP público que você pode atribuir diretamente à sua instância de máquina virtual ou função, em vez de para o serviço de nuvem que sua instância de máquina virtual ou função residem em. Isso não levar o local de VIP (IP virtual) que é atribuído a seu serviço de nuvem. Em vez disso, é um endereço IP adicional que você pode usar para se conectar diretamente à sua instância de máquina virtual ou função.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](virtual-network-ip-addresses-overview-arm.md). 

Verifique se que você entender como os [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

>[AZURE.NOTE] No passado, um ILPIP era conhecido como um PIP, o que significa IP público. 

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como mostrado na Figura 1, o serviço de nuvem é acessado usando um VIP, enquanto as VMs individuais normalmente são acessadas usando VIP:&lt;número da porta&gt;. Atribuindo uma ILPIP a uma máquina virtual específica, que máquina virtual pode ser acessado diretamente usando o endereço IP.

Quando você cria um serviço de nuvem no Azure, registros de DNS A correspondentes são criados automaticamente para permitir acesso ao serviço através de um nome de domínio totalmente qualificado (FQDN) em vez de usar o VIP real. O mesmo processo ocorre para ILPIP, permitindo o acesso para a instância de máquina virtual ou função por FQDN em vez da ILPIP. Por exemplo, se você criar um serviço de nuvem chamado *contosoadservice*e configurar uma função de web chamada *contosoweb* com duas instâncias, o Azure registrará os seguintes registros para as instâncias:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Você pode atribuir apenas um ILPIP para cada instância de máquina virtual ou função. Você pode usar até 5 ILPIP por assinatura. No momento, ILPIP não há suporte para multi-NIC VMs.

## <a name="why-should-i-request-an-ilpip"></a>Por que eu devo solicitar um ILPIP?
Se você quiser ser capaz de se conectar à sua instância de máquina virtual ou função por um endereço IP atribuído diretamente a ele, em vez de usar na nuvem serviço VIP:&lt;número da porta&gt;, um ILPIP de solicitação de sua máquina virtual ou sua instância de função.
- **FTP passivo** - fazendo uma ILPIP em sua máquina virtual, você pode receber o tráfego em praticamente qualquer porta, você não precisará abrir um ponto de extremidade para receber tráfego. Isso permite cenários como FTP passivo em que as portas são escolhidas dinamicamente.
- **IP de saída** - tráfego de saída provenientes a máquina virtual sai com a ILPIP como a fonte e identifica exclusivamente na máquina virtual para entidades externas.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Como solicitar um ILPIP durante a criação de máquina virtual
O script do PowerShell abaixo cria um novo serviço de nuvem denominado *FTPService*, e em seguida, recupera uma imagem do Azure e cria uma máquina virtual chamada *FTPInstance* usando a imagem recuperada, define a máquina virtual para usar um ILPIP e adiciona a máquina virtual para o novo serviço:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como recuperar informações de ILPIP para uma máquina virtual
Para exibir as informações de ILPIP para a máquina virtual criada com o script acima, execute o seguinte comando do PowerShell e observar os valores para *PublicIPAddress* e *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover uma ILPIP de uma máquina virtual
Para remover o ILPIP adicionado para a máquina virtual no script acima, execute o seguinte comando do PowerShell:
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP para uma máquina virtual existente
Para adicionar um ILPIP para a máquina virtual criada usando o script acima, execute o seguinte comando:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Como associar um ILPIP para uma máquina virtual usando um arquivo de configuração do serviço
Você também pode associar um ILPIP para uma máquina virtual usando um arquivo de configuração (CSCFG) do serviço. O xml de amostra abaixo mostra como configurar um serviço de nuvem para usar um ILPIP denominado *MyPublicIP* para uma instância da função: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximas etapas

- Compreenda como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo clássico de implantação.

- Saiba mais sobre [IPs reservadas](virtual-networks-reserved-public-ip.md).
 