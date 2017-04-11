<properties
   pageTitle="Como configurar o roteamento para um circuito de rota expressa | Microsoft Azure"
   description="Este artigo conduz você pelas etapas de criação e o provisionamento, público e privado Microsoft correspondência de um circuito de rota expressa. Este artigo também mostra como verificar o status, atualizar ou excluir peerings para seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Criar e modificar o roteamento para um circuito de rota expressa


> [AZURE.SELECTOR]
[Portal Azure - Gerenciador de recursos](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gerenciador de recursos](expressroute-howto-routing-arm.md)
[PowerShell - clássico](expressroute-howto-routing-classic.md)



Este artigo conduz você pelas etapas para criar e gerenciar a configuração de roteamento para um circuito de rota expressa usando o PowerShell e o modelo de implantação do Gerenciador de recursos do Azure.  As etapas a seguir também mostrará a você como verificar o status, atualizar ou excluir e desprovisionamento peerings para um circuito de rota expressa. 


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Você precisará a versão mais recente dos módulos do PowerShell do Azure, versão 1.0 ou posterior. 
- Certifique-se de que revisou a página de [pré-requisitos](expressroute-prerequisites.md) , a página de [Roteamento requisitos](expressroute-routing.md) e página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito rota expressa ativo. Siga as instruções para [criar um circuito de rota expressa](expressroute-howto-circuit-arm.md) e ter o circuito habilitado pelo seu provedor de conectividade antes de continuar. O circuito rota expressa deve estar em um estado provisionado e habilitado para ser capaz de executar os cmdlets descritos a seguir.

Estas instruções se aplicam apenas às circuitos criados com provedores de serviço, oferecendo serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços, oferecendo serviços de camada 3 gerenciados (geralmente uma IPVPN, como MPLS), seu provedor de conectividade irá configurar e gerenciar o roteamento para você.

>[AZURE.IMPORTANT] Estamos atualmente não anunciar peerings configurado pelo provedores de serviços por meio do portal de gerenciamento de serviço. Estamos trabalhando em ativar esse recurso em breve. Verifique com seu provedor de serviço antes de configurar o BGP peerings.

Você pode configurar um, dois ou todos os três peerings de (Azure público particular, Azure e Microsoft) para um circuito de rota expressa. Você pode configurar peerings em qualquer ordem em que você escolher. No entanto, você deve garantir que você conclua a configuração de cada correspondência um por vez. 

## <a name="azure-private-peering"></a>Correspondência particular Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência particular do Azure para um circuito de rota expressa. 

### <a name="to-create-azure-private-peering"></a>Para criar correspondência particular Azure

1. Importe o módulo do PowerShell para rota expressa.
    
    Você deve instalar o instalador do PowerShell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de recursos do Azure para a sessão do PowerShell para começar a usar os cmdlets rota expressa. Você precisará executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecida

        Import-AzureRM

    Você também pode importar um módulo selecione dentro do intervalo de versão semântico conhecida 
        
        Import-Module AzureRM.Network 

    Logon em sua conta

        Login-AzureRmAccount

    Selecione a assinatura que você deseja criar a rota expressa circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito de rota expressa.
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-arm.md) e tiver provisionado pelo provedor de conectividade. 

    Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure particular para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo. 

3. Verifique o circuito rota expressa para garantir que ele está provisionado.

    Você deve primeiro verificar se o circuito de rota expressa está provisionado e também habilitado. Consulte o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Configure a correspondência do Azure particular para o circuito.

    Certifique-se de que você tenha os seguintes itens antes de continuar com as próximas etapas:

    - Um /30 sub-rede para o link principal. Isso não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
    - Um /30 sub-rede do link secundário. Isso não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números. Você pode usar uma privada como número para esta correspondência. Certifique-se de que você não estiver usando 65515.
    - Um hash MD5, se você optar por usar um. **Isso é opcional**.
    
    Você pode executar o cmdlet a seguir para configurar a correspondência do Azure particular para seu circuito.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Você pode usar o cmdlet abaixo se você optar por usar um hash MD5.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Certifique-se de que você especifica o número como aos ASN, cliente ASN.

### <a name="to-view-azure-private-peering-details"></a>Exibir detalhes de correspondência particulares Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar a configuração de correspondência particular Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir. No exemplo abaixo, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Para excluir a correspondência particular Azure

Você pode remover sua configuração aos executando o cmdlet a seguir.

>[AZURE.WARNING] Você deve garantir que todas as redes virtuais são não vinculadas de circuito a rota expressa antes de executar este cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Correspondência de público Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência pública do Azure para um circuito de rota expressa.

### <a name="to-create-azure-public-peering"></a>Para criar a correspondência de público Azure

1. Importe o módulo do PowerShell para rota expressa.
    
    Você deve instalar o instalador do PowerShell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de recursos do Azure para a sessão do PowerShell para começar a usar os cmdlets rota expressa. Você precisará executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecida

        Import-AzureRM

    Você também pode importar um módulo selecione dentro do intervalo de versão semântico conhecida 
        
        Import-Module AzureRM.Network 

    Logon em sua conta

        Login-AzureRmAccount

    Selecione a assinatura que você deseja criar a rota expressa circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito de rota expressa.
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-arm.md) e tiver provisionado pelo provedor de conectividade. 

    Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure pública para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo.

3. Verificar circuito de rota expressa para garantir que ele está provisionado.

    Você deve primeiro verificar se o circuito de rota expressa está provisionado e também habilitado. Consulte o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Configure a correspondência do Azure público para o circuito.

    Certifique-se de que você tem as seguintes informações antes de prosseguir ainda mais.

    - Um /30 sub-rede para o link principal. Isso deve ser um prefixo IPv4 público válido.
    - Um /30 sub-rede do link secundário. Isso deve ser um prefixo IPv4 público válido.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números.
    - Um hash MD5, se você optar por usar um. **Isso é opcional**.
    
    Você pode executar o cmdlet a seguir para configurar a correspondência do Azure pública para seu circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Você pode usar o cmdlet abaixo se você optar por usar um hash MD5

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Certifique-se de que você especifica o número como ASN correspondência e não cliente ASN.

### <a name="to-view-azure-public-peering-details"></a>Exibir detalhes de correspondência públicos Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Atualizar a configuração de correspondência pública Azure

Você pode atualizar qualquer parte da configuração usando o seguinte cmdlet

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

O ID de VLAN do circuito está sendo atualizado de 200 para 600 no exemplo acima.

### <a name="to-delete-azure-public-peering"></a>Para excluir a correspondência de público Azure

Você pode remover sua configuração aos executando o seguinte cmdlet

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft correspondência

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de correspondência da Microsoft para um circuito de rota expressa. 

### <a name="to-create-microsoft-peering"></a>Para criar a correspondência da Microsoft

1. Importe o módulo do PowerShell para rota expressa.
    
    Você deve instalar o instalador do PowerShell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de recursos do Azure para a sessão do PowerShell para começar a usar os cmdlets rota expressa. Você precisará executar o PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecida

        Import-AzureRM

    Você também pode importar um módulo selecione dentro do intervalo de versão semântico conhecida 
        
        Import-Module AzureRM.Network 

    Logon em sua conta

        Login-AzureRmAccount

    Selecione a assinatura que você deseja criar a rota expressa circuito
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crie um circuito de rota expressa.
    
    Siga as instruções para criar um [circuito de rota expressa](expressroute-howto-circuit-arm.md) e tiver provisionado pelo provedor de conectividade. 

    Se o seu provedor de conectividade oferecer serviços de camada 3 gerenciados, você pode solicitar o seu provedor de conectividade para habilitar a correspondência do Azure particular para você. Nesse caso, não será preciso siga instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar seu circuito, siga as instruções abaixo.

3. Verificar circuito de rota expressa para garantir que ele está provisionado.

    Você deve primeiro verificar se o circuito de rota expressa está provisionado e também habilitado. Consulte o exemplo abaixo.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    A resposta será algo semelhante ao exemplo abaixo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Configure o Microsoft correspondência para o circuito.

    Certifique-se de que você tem as seguintes informações antes de continuar.

    - Um /30 sub-rede para o link principal. Este valor deve ser um público IPv4 prefixo válido pertencente a você e registrado em um RIR / TIR.
    - Um /30 sub-rede do link secundário. Este valor deve ser um público IPv4 prefixo válido pertencente a você e registrado em um RIR / TIR.
    - Um ID de VLAN válido para estabelecer a essa correspondência em. Certifique-se de que nenhuma outra correspondência no circuito usa a mesma ID de VLAN.
    - COMO número para correspondência. Você pode usar 2 bytes e de 4 bytes como números.
    - Anunciado prefixos: você deve fornecer uma lista de todos os prefixos que você planeja anunciar sobre a sessão BGP. Somente prefixos de endereço IP públicos são aceitas. Se você planeja enviar um conjunto de prefixos, você pode enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados para você em um RIR / TIR.
    - Cliente ASN: Se você estiver prefixos de anúncios que não são registrados para a correspondência como número, você pode especificar o número para o qual elas são registradas. **Isso é opcional**.
    - Nome de roteamento de registro: Você pode especificar a RIR / TIR em relação à qual o número e prefixos são registrados.
    - Um hash MD5, se você optar por usar um. **Isso é opcional.**
    
    Você pode executar o cmdlet a seguir para configurar o Microsoft correspondência para seu circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>Para obter detalhes de correspondência da Microsoft

Você pode obter detalhes de configuração usando o seguinte cmdlet.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Atualizar a configuração de correspondência da Microsoft

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Para excluir a correspondência da Microsoft

Você pode remover sua configuração aos executando o cmdlet a seguir.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Próximas etapas

Próxima etapa, [um VNet para um circuito de rota expressa do Link](expressroute-howto-linkvnet-arm.md).

-  Para obter mais informações sobre fluxos de trabalho de rota expressa, consulte [fluxos de trabalho de rota expressa](expressroute-workflows.md).

-  Para obter mais informações sobre a correspondência de circuito, consulte [rota expressa circuitos e domínios de roteamento](expressroute-circuit-peerings.md).

-  Para obter mais informações sobre como trabalhar com redes virtuais, consulte [Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md).

