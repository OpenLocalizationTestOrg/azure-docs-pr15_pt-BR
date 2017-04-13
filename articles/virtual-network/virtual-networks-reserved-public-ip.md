<properties
   pageTitle="Reservado IP | Microsoft Azure"
   description="Entender IPs reservadas e como gerenciá-los"
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

# <a name="reserved-ip-overview"></a>Visão geral IP reservado
Endereços IP no Azure recaem em duas categorias: dinâmico e reservado. Endereços IP públicos gerenciados pelo Azure são dinâmicos por padrão. Isso significa que o endereço IP usado para um serviço de nuvem determinado (VIP) ou para acessar uma função ou máquina virtual instância diretamente (ILPIP) podem alterar de vez em quando, quando os recursos estão desligamento ou desalocada.

Para impedir a alteração de endereços IP, você pode reservar um endereço IP. Endereços IP reservado pode ser usado somente como um VIP, garantindo que o endereço IP para o serviço de nuvem será o mesmo mesmo conforme recursos são desligamento ou desalocada. Além disso, você pode converter existente IPs dinâmico usado como um VIP para um endereço IP reservado.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como reservar um endereço IP público estático usando o [modelo de implantação do Gerenciador de recursos](virtual-network-ip-addresses-overview-arm.md).

Verifique se que você entender como os [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Quando eu preciso um IP reservado?
- **Para garantir que o IP é reservado em sua assinatura**. Se você quiser reservar um endereço IP que não será lançado de sua assinatura em qualquer caso, você deve usar um reservadas IP público.  
- **Deseja que seu IP para ficar com o seu serviço de nuvem mesmo em estado parado ou liberado (VMs)**. Se você quiser seu serviço para ser acessado por meio de um endereço IP que não serão alteradas mesmo quando as VMs no serviço de nuvem são parar ou liberada.
- **Para garantir que o tráfego de saída do Azure usa um endereço IP previsível**. Você pode ter seu firewall local configurado para permitir apenas tráfego de endereços IP específicos. Por reservar um IP, você saberá o endereço IP de origem e não é preciso atualizar suas regras de firewall devido a uma alteração de IP.

## <a name="faq"></a>Perguntas Freqüentes
1. Pode usar um IP reservado para todos os serviços do Azure?  
  - IPs reservadas só pode ser usado para VMs e funções de instância de serviço de nuvem expostas por meio de um VIP.
1. Quantos IPs reservadas pode ter?  
  - No momento, todas as assinaturas do Azure estão autorizadas a usar IPs reservadas 20. No entanto, você pode solicitar IPs reservadas adicionais. Consulte a página de [assinatura e limites de serviço](../azure-subscription-service-limits.md) para obter mais informações.
1. Há um custo para IPs reservadas?
  - Consulte [Detalhes de preços do endereço de IP reservado](http://go.microsoft.com/fwlink/?LinkID=398482) para informações sobre preços.
1. Como posso reservar um endereço IP?
  - Você pode usar o PowerShell ou a [API do Azure gerenciamento REST](https://msdn.microsoft.com/library/azure/dn722420.aspx) reservar um endereço IP em uma determinada região. Este endereço IP reservado está associado à sua assinatura. Você não pode reservar um endereço IP usando o Portal de gerenciamento.
1. Posso usar isso com o grupo de afinidade com base em VNets?
  - Somente são compatíveis com IPs reservadas VNets regionais. Ele não é suportado pelos VNets que estão associadas a grupos de afinidade. Para obter mais informações sobre como associar um VNet com uma região ou um grupo de afinidade, consulte [sobre regionais VNets e grupos de afinidade](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Como gerenciar VIPs reservados

Antes de poder usar IPs reservadas, você deve adicioná-lo à sua assinatura. Para criar um IP reservado do pool de endereços IP públicos disponíveis no local *Central EUA* , execute o seguinte comando do PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Observe, contudo, que você não pode especificar o que está sendo IP reservado. Para ver quais endereços IP são reservados na sua assinatura, execute o seguinte comando do PowerShell e observe os valores para *ReservedIPName* e *endereço*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Depois que um IP é reservado, permanece associado à sua assinatura até você excluí-lo. Para excluir o IP reservado mostrado acima, execute o seguinte comando do PowerShell:

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Como reservar o endereço IP de um serviço de nuvem existente

Você pode reservar o endereço IP de um serviço de nuvem existente, adicionando o parâmetro *- ServiceName* . Para reservar o endereço IP de um serviço de nuvem *TestService* no local *Central EUA* , execute o seguinte comando do PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Como associar um IP reservado a um novo serviço de nuvem
O script a seguir cria um novo IP reservado, depois associa a um novo serviço de nuvem denominado *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Quando você cria um IP reservado para usar com um serviço na nuvem, você ainda precisará se referir a máquina virtual usando *VIP:&lt;número da porta >* para comunicação de entrada. Reservar um IP não significa que você pode conectar diretamente para a máquina virtual. O IP reservado é atribuído ao serviço de nuvem que a máquina virtual foi implantada. Se você quiser se conectar a uma máquina virtual por IP diretamente, você precisa configurar um IP de nível de instância público. Um IP de nível de instância público é um tipo de IP público (chamado de um ILPIP) que é atribuído diretamente à sua máquina virtual. Ela não pode ser reservada. Consulte o [Nível de instância pública IP (ILPIP)](virtual-networks-instance-level-public-ip.md) para obter mais informações.

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Como remover um IP reservado de uma implantação em execução
Para remover o IP reservado adicionado ao serviço de novo criado no script acima, execute o seguinte comando do PowerShell:

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Removendo um IP reservado de uma implantação em execução não remove a reserva de sua assinatura. Ele simplesmente libera o IP a ser usado por outro recurso em sua assinatura.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Como associar um IP reservado a uma implantação em execução
O script a seguir cria um novo serviço de nuvem chamado *TestService2* com uma nova máquina virtual chamada *TestVM2*e, em seguida, associa o IP reservado existente chamado *MyReservedIP* ao serviço de nuvem.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Como associar um IP reservado para um serviço de nuvem usando um arquivo de configuração do serviço
Você também pode associar um IP reservado para um serviço de nuvem usando um arquivo de configuração (CSCFG) do serviço. O xml de amostra abaixo mostra como configurar um serviço de nuvem para usar um VIP reservado denominado *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximas etapas

- Compreenda como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo clássico de implantação.

- Saiba mais sobre [os endereços IP particulares reservados](virtual-networks-reserved-private-ip.md).

- Saiba mais sobre [endereços de instância nível público IP (ILPIP)](virtual-networks-instance-level-public-ip.md).
