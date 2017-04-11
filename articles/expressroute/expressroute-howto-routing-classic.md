<properties
   pageTitle="Como configurar o roteamento para um circuito de rota expressa para o modelo clássico de implantação usando o PowerShell | Microsoft Azure"
   description="Este artigo conduz você pelas etapas de criação e o provisionamento, público e privado Microsoft correspondência de um circuito de rota expressa. Este artigo também mostra como verificar o status, atualizar ou excluir peerings para seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Criar e modificar o roteamento para um circuito de rota expressa

> [AZURE.SELECTOR]
[Portal Azure - Gerenciador de recursos](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gerenciador de recursos](expressroute-howto-routing-arm.md)
[PowerShell - clássico](expressroute-howto-routing-classic.md)



Este artigo conduz você pelas etapas para criar e gerenciar a configuração de roteamento para um circuito de rota expressa usando o PowerShell e o modelo de implantação clássico. As etapas a seguir também mostrará a você como verificar o status, atualizar ou excluir e desprovisionamento peerings para um circuito de rota expressa.


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Você precisará a versão mais recente dos módulos do PowerShell do Azure. Você pode baixar o módulo do PowerShell mais recente da seção PowerShell da [página de Downloads do Azure](https://azure.microsoft.com/downloads/). Siga as instruções na página [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para orientações passo a passo sobre como configurar seu computador para usar os módulos do PowerShell do Azure. 
- Certifique-se de que revisou a página de [pré-requisitos](expressroute-prerequisites.md) , a página de [Roteamento requisitos](expressroute-routing.md) e página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito rota expressa ativo. Siga as instruções para [criar um circuito de rota expressa](expressroute-howto-circuit-classic.md) e ter o circuito habilitado pelo seu provedor de conectividade antes de continuar. O circuito rota expressa deve estar em um estado provisionado e habilitado para ser capaz de executar os cmdlets descritos a seguir.

>[AZURE.IMPORTANT] Estas instruções se aplicam apenas às circuitos criados com provedores de serviço, oferecendo serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços, oferecendo serviços de camada 3 gerenciados (geralmente uma IPVPN, como MPLS), seu provedor de conectividade irá configurar e gerenciar o roteamento para você.

Você pode configurar um, dois ou todos os três peerings de (Azure público particular, Azure e Microsoft) para um circuito de rota expressa. Você pode configurar peerings em qualquer ordem em que você escolher. No entanto, você deve garantir que você conclua a configuração de cada correspondência um por vez. 

## <a name="azure-private-peering"></a>Correspondência particular Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência particular do Azure para um circuito de rota expressa. 

### <a name="to-create-azure-private-peering"></a>Para criar correspondência particular Azure

1. **Importe o módulo do PowerShell para rota expressa.**
    
    Você deve importar os módulos do Azure e rota expressa para a sessão do PowerShell para começar a usar os cmdlets de rota expressa. Execute os seguintes comandos para importar os módulos do Azure e rota expressa para a sessão do PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Crie um circuito de rota expressa.**
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-classic.md) e tiver provisionado pelo provedor de conectividade. Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure particular para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo. 

3. **Verifique o circuito rota expressa para garantir que ele está provisionado.**

    Você deve primeiro verificar se o circuito de rota expressa está provisionado e também habilitado. Consulte o exemplo abaixo.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Certifique-se de que o circuito mostra como provisionado e ativado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado obrigatório e o status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configure a correspondência do Azure particular para o circuito.**

    Certifique-se de que você tenha os seguintes itens antes de continuar com as próximas etapas:

    - Um /30 sub-rede para o link principal. Isso não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
    - Um /30 sub-rede do link secundário. Isso não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números. Você pode usar uma privada como número para esta correspondência. Certifique-se de que você não estiver usando 65515.
    - Um hash MD5, se você optar por usar um. **Isso é opcional**.
    
    Você pode executar o cmdlet a seguir para configurar a correspondência do Azure particular para seu circuito.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    Você pode usar o cmdlet abaixo se você optar por usar um hash MD5.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Certifique-se de que você especifica o número como aos ASN, cliente ASN.

### <a name="to-view-azure-private-peering-details"></a>Exibir detalhes de correspondência particulares Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar a configuração de correspondência particular Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir. No exemplo abaixo, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Para excluir a correspondência particular Azure

Você pode remover sua configuração aos executando o cmdlet a seguir.

>[AZURE.WARNING] Você deve garantir que todas as redes virtuais são não vinculadas de circuito a rota expressa antes de executar este cmdlet. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Correspondência de público Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência pública do Azure para um circuito de rota expressa.

### <a name="to-create-azure-public-peering"></a>Para criar a correspondência de público Azure

1. **Importe o módulo do PowerShell para rota expressa.**
    
    Você deve importar os módulos do Azure e rota expressa para a sessão do PowerShell para começar a usar os cmdlets rota expressa. Execute os seguintes comandos para importar os módulos do Azure e rota expressa para a sessão do PowerShell. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Criar um circuito de rota expressa**
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-classic.md) e tiver provisionado pelo provedor de conectividade. Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure pública para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo.

3. **Verificar a rota expressa circuito para garantir que ele está provisionado**

    Você deve primeiro verificar se o circuito de rota expressa está provisionado e também habilitado. Consulte o exemplo abaixo.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Certifique-se de que o circuito mostra como provisionado e ativado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado obrigatório e o status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Configurar a correspondência do Azure público para o circuito**

    Certifique-se de que você tem as seguintes informações antes de prosseguir ainda mais.

    - Um /30 sub-rede para o link principal. Isso deve ser um prefixo IPv4 público válido.
    - Um /30 sub-rede do link secundário. Isso deve ser um prefixo IPv4 público válido.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números.
    - Um hash MD5, se você optar por usar um. **Isso é opcional**.
    
    Você pode executar o cmdlet a seguir para configurar a correspondência do Azure pública para seu circuito

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    Você pode usar o cmdlet abaixo se você optar por usar um hash MD5

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Certifique-se de que você especifica o número como ASN correspondência e não cliente ASN.

### <a name="to-view-azure-public-peering-details"></a>Exibir detalhes de correspondência públicos Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Atualizar a configuração de correspondência pública Azure

Você pode atualizar qualquer parte da configuração usando o seguinte cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

O ID de VLAN do circuito está sendo atualizado de 200 para 600 no exemplo acima.

### <a name="to-delete-azure-public-peering"></a>Para excluir a correspondência de público Azure

Você pode remover sua configuração aos executando o seguinte cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft correspondência

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência da Microsoft para um circuito de rota expressa. 

### <a name="to-create-microsoft-peering"></a>Para criar a correspondência da Microsoft

1. **Importe o módulo do PowerShell para rota expressa.**
    
    Você deve importar os módulos do Azure e rota expressa para a sessão do PowerShell para começar a usar os cmdlets de rota expressa. Execute os seguintes comandos para importar os módulos do Azure e rota expressa para a sessão do PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Criar um circuito de rota expressa**
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-classic.md) e tiver provisionado pelo provedor de conectividade. Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure particular para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo.

3. **Verificar a rota expressa circuito para garantir que ele está provisionado**

    Você deve primeiro verifique se o circuito de rota expressa estiver em estado provisionado e ativado.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Certifique-se de que o circuito mostra como provisionado e ativado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado obrigatório e o status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurar o Microsoft correspondência para o circuito**

    Certifique-se de que você tem as seguintes informações antes de continuar.

    - Um /30 sub-rede para o link principal. Este valor deve ser um público IPv4 prefixo válido pertencente a você e registrado em um RIR / TIR.
    - Um /30 sub-rede do link secundário. Este valor deve ser um público IPv4 prefixo válido pertencente a você e registrado em um RIR / TIR.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números.
    - Anunciado prefixos: você deve fornecer uma lista de todos os prefixos que você planeja anunciar sobre a sessão BGP. Somente prefixos de endereço IP públicos são aceitas. Se você planeja enviar um conjunto de prefixos, você pode enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados para você em um RIR / TIR.
    - Cliente ASN: Se você estiver prefixos de anúncios que não são registrados para a correspondência como número, você pode especificar o número para o qual elas são registradas. **Isso é opcional**.
    - Nome de roteamento de registro: Você pode especificar a RIR / TIR em relação à qual o número e prefixos são registrados.
    - Um hash MD5, se você optar por usar um. **Isso é opcional.**
    
    Você pode executar o seguinte cmdlet para configurar pering da Microsoft para seu circuito

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>Exibir detalhes de correspondência da Microsoft

Você pode obter detalhes de configuração usando o seguinte cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Atualizar a configuração de correspondência da Microsoft

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Para excluir a correspondência da Microsoft

Você pode remover sua configuração aos executando o cmdlet a seguir.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Próximas etapas

Em seguida, [vincular um VNet para um circuito de rota expressa](expressroute-howto-linkvnet-classic.md).


-  Para obter mais informações sobre fluxos de trabalho, consulte [fluxos de trabalho de rota expressa](expressroute-workflows.md).
-  Para obter mais informações sobre a correspondência de circuito, consulte [rota expressa circuitos e domínios de roteamento](expressroute-circuit-peerings.md).

