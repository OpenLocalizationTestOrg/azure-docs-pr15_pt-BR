<properties 
   pageTitle="Configurar túnel forçada para conexões de-to-Site usando o modelo de implantação do Gerenciador de recursos | Microsoft Azure"
   description="Como redirecionar ou 'force' todo o tráfego de Internet ligadas volta para seu local de local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar túnel forçada usando o modelo de implantação do Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gerenciador de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forçada permite redirecionar ou "forçar" todo o tráfego de Internet ligadas de volta para seu local por meio de um túnel de-to-Site VPN para inspeção e auditoria. Esse é um requisito de segurança crítica para a maioria dos enterprise IT políticas.

Sem túnel forçada, o tráfego de Internet ligadas de suas VMs no Azure sempre será percorrer de infraestrutura de rede Azure diretamente check-out à Internet, sem a opção para permitir que você inspecionar ou o tráfego de auditoria. Acesso não autorizado de Internet pode levar a divulgação de informações ou outros tipos de violações de segurança

Este artigo o orienta Configurando forçado túnel para redes virtuais criadas usando o modelo de implantação do Gerenciador de recursos.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implantação e ferramentas para túnel forçada**

Uma conexão de túnel forçada pode ser configurado para o modelo de implantação clássico e o modelo de implantação do Gerenciador de recursos. Consulte a tabela a seguir para obter mais informações. Podemos atualizar esta tabela como novos artigos, modelos de implantação de novo e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele da tabela.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Sobre forçado túnel


O diagrama a seguir ilustra como forçado works túnel. 

![Forçado túnel](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, o Frontend sub-rede não está sendo forçado encapsulado. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder a solicitações de cliente da Internet diretamente. As sub-redes intermediário e back-end são obrigadas encapsulado. As conexões de saída dessas duas sub-redes com a Internet serão forçados ou redirecionados para um site local por meio de um nos encapsulamentos VPN S2S.

Isso permite que você restringir e inspecionar acesso à Internet de suas máquinas virtuais ou serviços no Azure, em nuvem enquanto mantém a ativar sua arquitetura de vários níveis de serviço necessária. Você também pode aplicar túnel forçada para as redes virtuais inteiras se houver cargas de trabalho sem voltado para a Internet em suas redes virtuais.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

Túnel forçada no Azure está configurado por meio de rotas de definidas pelo usuário da rede virtual. Redirecionar o tráfego para um site local é expresso como uma rota padrão para o gateway VPN do Azure. Para obter mais informações sobre o roteamento de definidas pelo usuário e redes virtuais, consulte [rotas e encaminhamento IP definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md).

- Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento de sistema tem os seguintes três grupos de rotas:

    - **Rotas VNet local:** Diretamente para o destino VMs na mesma rede virtual
    
    - **Rotas local:** Ao gateway VPN do Azure
    
    - **Rota padrão:** Diretamente à Internet. Pacotes destinados para os endereços IP particulares não cobertos pelas duas rotas anteriores serão ignorados.

-  Este procedimento usa rotas definidas pelo usuário (UDR) para criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento para sua subnet(s) VNet para habilitar o túnel forçada nessas sub-redes.

- Túnel forçada deve ser associada um VNet que possui um gateway VPN baseada em rota. Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.

- Rota expressa forçada túnel não está configurada via esse mecanismo, mas em vez disso, é habilitada por anunciando uma rota padrão via as sessões de correspondência ExpressRoute BGP. Consulte a [Documentação de rota expressa](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

## <a name="configuration-overview"></a>Visão geral da configuração

O procedimento a seguir ajuda você a criar um grupo de recursos e um VNet. Você vai criar um gateway VPN e configurar túnel forçada. Neste procedimento, a rede virtual "Várias camadas-VNet" tem 3 sub-redes: *Frontend*, *mercado intermediário*e *back-end*, com 4 entre conexões locais: *DefaultSiteHQ*e 3 *ramificações*.

As etapas do procedimento definir o *DefaultSiteHQ* como a conexão de site padrão para forçado túnel e configurar o mercado intermediário e sub-redes de back-end para usar forçada túnel.

    
## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar a configuração.

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Você precisará instalar a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="configure-forced-tunneling"></a>Configurar túnel forçada

1. No console do PowerShell, faça logon em sua conta do Azure. Esse cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, ele baixa suas configurações de conta para que estejam disponíveis para o PowerShell do Azure.

        Login-AzureRmAccount 

2. Obter uma lista das suas assinaturas Azure.

        Get-AzureRmSubscription

2. Especifica a assinatura que você deseja usar. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Criar uma rede virtual e especificar sub-redes. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Crie os gateways de rede local.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Crie a tabela de rota e a regra de rota.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Associe a tabela de rota para as sub-redes mercado intermediário e back-end.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Crie o Gateway com um site padrão. Esta etapa leva algum tempo para ser concluída, às vezes, 45 minutos ou mais, porque você está criando e configurando o gateway.<br> O `-GatewayDefaultSite` é o parâmetro de cmdlet que permite a configuração de roteamento forçada funcione, portanto, tenha cuidado para configurar essa configuração corretamente. Este parâmetro está disponível no PowerShell 1.0 ou posterior.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Estabelece as conexões VPN to-Site.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



