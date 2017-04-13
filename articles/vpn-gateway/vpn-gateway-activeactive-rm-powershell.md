<properties
   pageTitle="Como configurar conexões de VPN S2S ativa com Gateways de VPN Azure usando o Gerenciador de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo o orienta Configurando ativa conexões com Gateways de VPN do Azure usando o Gerenciador de recursos do Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Configurar conexões de VPN S2S ativa com Gateways de VPN Azure usando o Gerenciador de recursos do Azure e PowerShell

Este artigo conduz você pelas etapas para criar conexões de VNet para VNet usando o modelo de implantação do Gerenciador de recursos e PowerShell e ativa entre locais.


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>Sobre conexões entre locais altamente disponível

Para obter alta disponibilidade para entre locais e conectividade de VNet para VNet, você deve implantar vários gateways VPN e estabelecer várias conexões paralelas entre suas redes e Azure. Consulte [altamente disponível entre locais e conectividade de VNet para VNet](./vpn-gateway-highlyavailable.md) para uma visão geral de topologia e opções de conectividade.

Este artigo fornece as instruções para configurar uma conexão de VPN ativa entre locais e ativa conexão entre duas redes virtuais:

- [Parte 1 - criar e configurar o gateway VPN Azure no modo ativo-ativo](#aagateway)

- [Parte 2 - estabelecer conexões entre locais ativa](#aacrossprem)

- [Parte 3 - estabelecer conexões de VNet para VNet ativa](#aav2v)

- [Parte 4 - atualizar o gateway existente entre ativa e espera ativo](#aaupdate)

Você pode combinar essas juntas para criar uma topologia de rede mais complexas, altamente disponível que atenda às suas necessidades.

>[AZURE.IMPORTANT] Observe que o modo de ativa só funciona em HighPerformance SKU


## <a name ="aagateway"></a>Parte 1 - criar e configurar os gateways VPN ativa

As etapas a seguir irá configurar o gateway VPN Azure nos modos de ativa. As principais diferenças entre os gateways ativa e ativa-espera:

- Você precisa criar duas configurações de IP do Gateway com dois endereços IP públicos
- Você precisa definir o sinalizador EnableActiveActiveFeature
- O gateway SKU deve estar HighPerformance

As outras propriedades são a mesma que os gateways de ativo ativo. 

### <a name="before-you-begin"></a>Antes de começar

- Verifique se você tem uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Você precisará instalar cmdlets do PowerShell do Gerenciador de recursos do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Etapa 1 - criar e configurar VNet1

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Para este exercício, começaremos declarando nossas variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Certifique-se de substituir os valores com seu próprio durante a configuração para produção. Você pode usar essas variáveis se você estiver executando as etapas para se familiarizar com esse tipo de configuração. Modificar as variáveis e em seguida, copie e cole seu console do PowerShell.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. se conectar à sua assinatura e criar um novo grupo de recursos

Verifique se que você alterna para o modo de PowerShell para usar os cmdlets do Gerenciador de recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conectar à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. criar TestVNet1

O exemplo a seguir cria uma rede virtual chamada TestVNet1 e três sub-redes, um GatewaySubnet chamado, um FrontEnd chamado e back-end chamado um. Ao substituir valores, é importante que você sempre pode nomear sua sub-rede gateway especificamente GatewaySubnet. Se você nomear-algo diferente, a criação de seu gateway falhará. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Etapa 2 - criar o gateway VPN para TestVNet1 com o modo de ativa

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Crie os endereços IP e o gateway pública configurações de IP

Solicite dois endereços IP públicos a ser alocado para o gateway que você criará para sua VNet. Você também deverá definir as configurações de IP obrigatórias e sub-rede. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Crie o gateway VPN com a configuração ativa

Crie o gateway de rede virtual para TestVNet1. Observe que há duas entradas de GatewayIpConfig, e o sinalizador EnableActiveActiveFeature está definido. Modo de ativa requer um gateway VPN baseada em rota de HighPerformance SKU. Criar um gateway pode demorar um pouco (30 minutos ou mais para ser concluída).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. obter os endereços IP públicos do gateway e o endereço IP do ponto de BGP

Depois que o gateway é criado, você precisará obter o endereço de IP de ponto de BGP no Azure VPN Gateway. Esse endereço é necessário para configurar o Gateway de VPN do Azure como um par de BGP para os seus dispositivos VPN local.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Use os seguintes cmdlets para mostrar os dois endereços IP públicos alocados para o gateway VPN e seus endereços de IP de ponto de BGP correspondentes para cada instância do gateway:

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

A ordem do IP público endereços para as instâncias de gateway e os endereços de correspondência de BGP correspondentes são os mesmos. Neste exemplo, o gateway de máquina virtual com IP público de 40.112.190.5 usará 10.12.255.4 como seu endereço de correspondência de BGP e o gateway com 138.91.156.129 usará 10.12.255.5. Essas informações são necessárias quando você configura seus dispositivos VPN local no conexão com o gateway ativa. O gateway é mostrado no diagrama abaixo com todos os endereços:

![gateway ativa](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Depois que o gateway é criado, você pode usar esse gateway para estabelecer ativa entre locais ou conexão de VNet para VNet. As seções a seguir orientará durante as etapas para concluir o exercício.


## <a name ="aacrossprem"></a>Parte 2 - estabelecer uma conexão local ativa cruzado

Para estabelecer uma conexão entre locais, você precisa criar um Gateway de rede Local para representar o seu dispositivo VPN local e uma Conexão para conectar o gateway VPN Azure com o gateway de rede local. Neste exemplo, o gateway VPN Azure está no modo de ativa. Como resultado, mesmo que haja apenas um local dispositivo VPN (rede local gateway) e o recurso de uma conexão, ambas as instâncias de gateway VPN Azure estabelece túneis VPN S2S com o dispositivo local.

Antes de prosseguir, certifique-se de que você concluiu a [parte 1](#aagateway) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Etapa 1 - criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Neste exercício continuará criar a configuração mostrada no diagrama. Certifique-se de que substitua os valores por aqueles que você deseja usar para sua configuração.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Algumas coisas nota sobre os parâmetros de gateway de rede local:

- O gateway de rede local pode ser no local iguais ou diferente e grupo de recursos, como o gateway VPN. Este exemplo mostra-los em grupos de recursos diferentes, mas no mesmo local Azure.

- Se houver apenas um dispositivo VPN local conforme mostrado acima, a conexão ativa pode trabalhar com ou sem protocolo BGP. Este exemplo usa BGP para a conexão entre locais.

- Se BGP estiver habilitada, o prefixo que é necessário declarar para o gateway de rede local é o endereço de host do seu endereço de IP de ponto de BGP em seu dispositivo VPN. Nesse caso, é um /32 prefixo de "10.52.255.253/32".

- Como um lembrete, você deve usar diferentes ASNs BGP entre suas redes de local e VNet do Azure. Se eles forem o mesmo, você precisa alterar seu ASN VNet se seu dispositivo VPN local já usa a ASN ao correspondente com outros vizinhos BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para Site5
    
Antes de continuar, verifique se que você ainda está conectado à assinatura 1. Crie o grupo de recursos se ele ainda não foi criado.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2: conectar o gateway VNet e o gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. obter os dois gateways

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Crie o TestVNet1 para conexão de Site5

Nesta etapa, você irá criar a conexão de TestVNet1 para Site5_1 com "EnableBGP" definida como $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN e BGP parâmetros para seu dispositivo VPN de local

O exemplo a seguir lista os parâmetros que você digitará para a seção de configuração de BGP em seu dispositivo VPN local para este exercício:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.253
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 para túnel para 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 para túnel para 138.91.156.129
    - Rotas estáticas: destino 10.12.255.4/32, nexthop túnel VPN interface para 40.112.190.5 destino 10.12.255.5/32, nexthop interface de túnel VPN para 138.91.156.129
    - eBGP Multihop: Verifique se a opção de "multihop" eBGP estiver ativado no seu dispositivo, se necessário

A conexão deve ser estabelecida após alguns minutos, e a sessão de correspondência BGP será iniciado assim que a conexão IPsec for estabelecida. Este exemplo até configurou apenas um dispositivo VPN local, resultando em diagrama mostrado abaixo:

![ativo-ativo-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Etapa 3: conectar dois dispositivos VPN de local para o gateway VPN ativa

Se você tiver dois dispositivos VPN na mesma rede local, você pode obter redundância dupla conectando-se o gateway VPN do Azure para o segundo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. criar o segundo gateway de rede local para Site5

Observe que o endereço IP do gateway, o prefixo de endereço e o endereço de correspondência BGP para o segundo gateway de rede local devem não se sobreponham com o gateway de rede local anterior para a mesma rede local. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Conecte o gateway VNet e o segundo gateway de rede local

Criar a conexão de TestVNet1 para Site5_2 com "EnableBGP" definido como $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN e BGP parâmetros para seu dispositivo VPN segundo local

Da mesma forma, abaixo listas os parâmetros que você digitará no segundo dispositivo VPN:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.254
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 para túnel para 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 para túnel para 138.91.156.129
    - Rotas estáticas: destino 10.12.255.4/32, nexthop túnel VPN interface para 40.112.190.5 destino 10.12.255.5/32, nexthop interface de túnel VPN para 138.91.156.129
    - eBGP Multihop: Verifique se a opção de "multihop" eBGP estiver ativado no seu dispositivo, se necessário

Depois que a conexão (encapsulamentos) são estabelecidas, você terá dois dispositivos VPN redundantes e encapsulamentos conectando sua rede local e o Azure:

![duplo-redundância-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Parte 3 - estabelecer uma conexão de VNet para VNet ativa

Esta seção cria uma conexão de VNet para VNet ativa com BGP. 

Continuam as instruções abaixo das etapas anteriores listadas acima. Você deve concluir a [parte 1](#aagateway) para criar e configurar TestVNet1 e o Gateway VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Etapa 1 - criar TestVNet2 e o gateway VPN

É importante garantir que o espaço de endereço IP da nova rede virtual, TestVNet2, não sobreponha com qualquer um dos seus intervalos VNet.

Neste exemplo, as redes virtuais pertencem a mesma assinatura. Você pode configurar conexões de VNet para VNet entre diferentes assinaturas; consulte [Configurar uma conexão de VNet para VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para obter mais detalhes. Verifique se você adicionou o "-EnableBgp $True" ao criar as conexões para habilitar o BGP.

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Certifique-se de que substitua os valores por aqueles que você deseja usar para sua configuração.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Crie TestVNet2 no novo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. criar o gateway VPN ativa para TestVNet2

Solicite dois endereços IP públicos a ser alocado para o gateway que você criará para sua VNet. Você também deverá definir as configurações de IP obrigatórias e sub-rede. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Crie o gateway VPN com o número e o sinalizador de "EnableActiveActiveFeature". Observe que você deve substituir o padrão ASN nos seus gateways VPN do Azure. Os ASNs para a VNets conectada devem ser diferentes para habilitar o roteamento de trânsito e BGP.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2: conectar os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma assinatura. Você pode concluir esta etapa na mesma sessão do PowerShell.

#### <a name="1-get-both-gateways"></a>1. obter ambos os gateways

Certifique-se de login e conectar a 1 de assinatura.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Crie conexões

Nesta etapa, você criará a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Certifique-se de habilitar BGP para ambas as conexões.

Após concluir essas etapas, a conexão estabelece em poucos minutos e BGP uma sessão de correspondência será backup depois que a conexão de VNet para VNet é concluída com redundância dupla:

![ativo-ativo-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4 - atualizar o gateway existente entre ativa e espera ativo

A última seção descrevem como você pode configurar um gateway VPN Azure existente do modo de espera ativo para o modo de ativa, ou vice-versa.

>[AZURE.IMPORTANT] Observe que o modo de ativa só funciona em HighPerformance SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurar um gateway de espera ativo para gateway ativa

#### <a name="1-gateway-parameters"></a>1. parâmetros de gateway

O exemplo a seguir converte um gateway de espera ativo em um gateway ativa. Você precisa criar outro endereço IP público, em seguida, adicionar uma segunda configuração de IP do Gateway. A seguir mostra os parâmetros usados:

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Crie o endereço IP público, em seguida, adicione a segunda configuração de IP do gateway

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. ativar o modo de ativa e atualize o gateway

Você deve definir o objeto de gateway no PowerShell disparar a atualização real. O SKU do objeto gateway também deve ser alterado para HighPerformance desde que ele foi criado anteriormente como padrão.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

Essa atualização pode levar de 30 a 45 minutos.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurar um gateway ativa ao gateway de espera ativo

#### <a name="1-gateway-parameters"></a>1. parâmetros de gateway

Use os mesmos parâmetros acima, obter o nome da configuração de IP que você deseja remover.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Remova a configuração de IP do gateway e desativar o modo de ativa

Da mesma forma, você deve definir o objeto de gateway no PowerShell disparar a atualização real.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Essa atualização pode levar até 30 para 45 minutos.


## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

