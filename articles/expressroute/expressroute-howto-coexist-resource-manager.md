<properties
   pageTitle="Configurar conexões rota expressa e to-Site VPN que podem coexistência para o modelo de implantação do Gerenciador de recursos | Microsoft Azure"
   description="Este artigo o orienta Configurando rota expressa e uma conexão de VPN to-Site pode coexistência de modelo do Gerenciador de recursos."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charleywen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-resource-manager-deployment-model"></a>Configurar conexões passa rota expressa e to-Site para o modelo de implantação do Gerenciador de recursos

> [AZURE.SELECTOR]
- [PowerShell - Gerenciador de recursos](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-coexist-classic.md)

A capacidade de configurar-to-Site VPN e rota expressa tem várias vantagens. Você pode configurar VPN to-Site como um caminho de failover seguro para ExressRoute ou usar VPNs Site-to-Site para se conectar aos sites que não estão conectadas por meio de rota expressa. Abordaremos as etapas para configurar os dois cenários neste artigo. Este artigo se aplica ao modelo de implantação do Gerenciador de recursos. Essa configuração não está disponível no portal do Azure.


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Rota expressa circuitos devem ser configurados previamente antes de seguir as instruções abaixo. Certifique-se de que você tiver seguido as guias para [criar um circuito de rota expressa](expressroute-howto-circuit-arm.md) e [Configurar o roteamento](expressroute-howto-routing-arm.md) antes de seguir as etapas abaixo.

## <a name="limits-and-limitations"></a>Limitações e limites

- **Não há suporte para o roteamento de trânsito.** Você não pode rotear (via Azure) entre sua rede local conectada por meio de-to-Site VPN e sua rede local conectada por meio de rota expressa.
- **Não há suporte para o gateway SKU básico.** Você deve usar um gateway SKU não básicas para o [gateway de rota expressa](expressroute-about-virtual-network-gateways.md) e o [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Baseada em rota somente gateway VPN é suportada.** Você deve usar um baseado em rota [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Rota estática deve ser configurada para o gateway VPN.** Se sua rede local está conectada a rota expressa e uma VPN to-Site, você deve ter uma rota estática configurada em sua rede local para rotear a conexão-to-Site VPN à Internet pública.
- **Rota expressa gateway deve ser configurado primeiro.** Você deverá criar o gateway rota expressa primeiro antes de adicionar o gateway-to-Site VPN.


## <a name="configuration-designs"></a>Designs de configuração

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma VPN to-Site como um caminho de failover para rota expressa

Você pode configurar uma conexão de VPN to-Site como um backup para rota expressa. Isso se aplica somente a redes virtuais vinculadas ao caminho aos particular do Azure. Não há uma solução com base em VPN para serviços acessíveis por meio do Azure público e Microsoft peerings. O circuito rota expressa é sempre o link principal. Dados fluirá caminho-to-Site VPN somente se o circuito de rota expressa falhar.
>[AZURE.NOTE] Enquanto rota expressa circuito está preferencial sobre-to-Site VPN quando as duas rotas são as mesmas, o Azure usará a correspondência de prefixo longuest para escolher a rota em direção a destino do pacote.

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma VPN to-Site para se conectar aos sites não conectados por meio de rota expressa

Você pode configurar sua rede onde alguns sites conectem diretamente ao Azure através de-to-Site VPN e alguns sites se conectar por meio de rota expressa. 

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Você não pode definir uma rede virtual como um roteador de trânsito.

## <a name="selecting-the-steps-to-use"></a>Selecionando as etapas para usar

Há dois conjuntos diferentes de procedimentos para escolher para configurar conexões que podem coexistência. O procedimento de configuração que você selecionar dependerá se você tem uma rede virtual existente que você deseja se conectar, ou que você deseja criar uma nova rede virtual.


- Eu não tiver um VNet e precisa criar um.
    
    Se você ainda não tiver uma rede virtual, este procedimento apresentará criando uma nova rede virtual usando o modelo de implantação do Gerenciador de recursos e criando novas conexões de VPN rota expressa e to-Site. Para configurar, siga as etapas na seção artigo [para criar uma nova rede virtual e passa conexões](#new).

- Eu já tenho um modelo de implantação do Gerenciador de recursos VNet.

    Você já pode ter uma rede virtual in-loco com uma conexão de VPN to-Site existente ou conexão de rota expressa. A seção [para configurar as conexões de coexsiting para um já existente VNet](#add) o orientará pela excluindo o gateway e, em seguida, criando novas conexões de VPN rota expressa e to-Site. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem específica muito. Não use as instruções em outros artigos para criar suas conexões e gateways.

    Neste procedimento, criação de conexões que podem coexistência será exigem que você exclua seu gateway e configure novos gateways. Isso significa que você terá tempo de inatividade para suas conexões entre locais enquanto você exclua e recrie seu gateway e conexões, mas você não precisará migrar qualquer um dos seus VMs ou serviços para uma nova rede virtual. Seus serviços e VMs ainda poderão se comunicar através de Balanceador de carga enquanto você configure seu gateway se eles estiverem configurados para fazer isso.


## <a name="new"></a>Para criar uma nova rede virtual e passa conexões

Este procedimento irá orientá-lo a criar um VNet e crie-to-Site e conexões de rota expressa que coexistam.
    
1. Você precisará instalar a versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para esta configuração pode ser um pouco diferentes do que você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções.

2. Logon de sua conta e configurar o ambiente.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Crie uma rede virtual incluindo sub-rede do Gateway. Para obter mais informações sobre a configuração de rede virtual, consulte [configuração da rede Virtual do Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

    >[AZURE.IMPORTANT] A sub-rede de Gateway deve ser /27 ou um prefixo mais curto (como /26 ou /25).
    
    Crie um novo VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Adicione sub-redes.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Salve a configuração de VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Crie um gateway rota expressa. Para obter mais informações sobre a configuração do gateway rota expressa, consulte [configuração do gateway rota expressa](expressroute-howto-add-gateway-resource-manager.md). O GatewaySKU deve ser *padrão*, *HighPerformance*ou *UltraPerformance*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Vincule o gateway rota expressa para o circuito rota expressa. Após essa etapa foi concluída, a conexão entre sua rede local e o Azure, por meio de rota expressa, é estabelecida. Para obter mais informações sobre a operação de vinculação, consulte [Link VNets a rota expressa](expressroute-howto-linkvnet-arm.md).

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Em seguida, crie o gateway-to-Site VPN. Para obter mais informações sobre a configuração do gateway VPN, consulte [Configurar um VNet com uma conexão de-to-Site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). O GatewaySKU deve ser *padrão*, *HighPerformance*ou *UltraPerformance*. O VpnType deve *RouteBased*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

    Azure gateway VPN é compatível com o BGP. Você pode especificar - EnableBgp no comando a seguir.

        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true

    Você pode encontrar o correspondências IP e o número que o Azure usa para o gateway VPN no $azureVpn.BgpSettings.BgpPeeringAddress e $azureVpn.BgpSettings.Asn de BGP. Para obter mais informações, consulte [Configurar BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para gateway VPN do Azure.

7. Crie um site local entidade de gateway VPN. Este comando não configurar o gateway VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o local de endereço espaço, para que o gateway VPN Azure pode conectá-lo.

    Se seu dispositivo VPN local suporta somente roteamento estático, você pode configurar as rotas estáticas da seguinte maneira.

        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress

    Se seu dispositivo VPN local suporta o BGP e você deseja habilitar o roteamento dinâmico, você precisa saber o correspondências IP e o número que usa seu dispositivo VPN local de BGP.

        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN

8. Configure seu dispositivo VPN local para se conectar ao novo gateway VPN do Azure. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração de dispositivo de VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Link gateway-to-Site VPN no Azure para o gateway local.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Para configurar conexões de coexsiting para um VNet já existente

Se você tiver uma rede virtual existente, verifique o tamanho de sub-rede do gateway. Se a sub-rede de gateway é /28 ou /29, você deve primeiro excluir o gateway de rede virtual e aumentar o tamanho de sub-rede do gateway. As etapas desta seção mostrará como fazer isso.

Se a sub-rede de gateway for /27 ou maior e a rede virtual está conectada por meio de rota expressa, você pode ignorar as etapas abaixo e vá para ["Etapa 6 - criar um gateway - to-Site VPN"](#vpngw) na seção anterior. 

>[AZURE.NOTE] Ao excluir o gateway existente, suas instalações locais perderá a conexão à sua rede virtual enquanto você estiver trabalhando nessa configuração. 

1. Você precisará instalar a versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para esta configuração pode ser um pouco diferentes do que você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Exclua o gateway rota expressa ou -to-Site VPN existente. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Exclua sub-rede do Gateway.
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Adicionar uma sub-rede de Gateway que está /27 ou maior.
    >[AZURE.NOTE] Se você não tiver endereços IP suficientes em sua rede virtual para aumentar o tamanho de sub-rede gateway, você precisa adicionar mais espaço de endereço IP.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Salve a configuração de VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. Neste ponto, você terá uma VNet com nenhum gateways. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [etapa 4 - criar um gateway rota expressa](#gw), encontrado no conjunto anterior de etapas.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Para adicionar a configuração de ponto-a-site ao gateway VPN
Você pode seguir as etapas abaixo para adicionar a configuração de ponto-a-Site para o gateway VPN em uma configuração de coexistência.

1. Adicione pool de endereços do cliente VPN. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Carregar o certificado de raiz VPN para o Azure para o gateway VPN. Neste exemplo, será considerado que o certificado raiz é armazenado na máquina local onde os seguintes cmdlets do PowerShell são executados. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Para obter mais informações sobre ponto-a-Site VPN, consulte [Configurar uma conexão ponto-a-Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
