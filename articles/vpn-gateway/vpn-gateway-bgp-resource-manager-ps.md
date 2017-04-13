<properties
   pageTitle="Como configurar o BGP em Gateways de VPN Azure usando o Gerenciador de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo o orienta Configurando o BGP com Gateways de VPN Azure usando o Gerenciador de recursos do Azure e PowerShell."
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
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Como configurar o BGP em Gateways de VPN Azure usando o Gerenciador de recursos do Azure e PowerShell

Este artigo conduz você pelas etapas para habilitar o BGP em uma conexão de VPN to-Site (S2S) entre local e uma conexão de VNet para VNet usando o modelo de implantação do Gerenciador de recursos e PowerShell.


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>Sobre BGP

BGP é o protocolo de roteamento padrão comumente usado na Internet para trocar informações de roteamento e acessibilidade entre duas ou mais redes. BGP habilita os Gateways de VPN do Azure e seus dispositivos VPN local, chamados BGP colegas ou vizinhos, trocar "roteia" informará ambos os gateways sobre a disponibilidade e a acessibilidade para esses prefixos percorrer os gateways ou roteadores envolvidos. BGP também pode habilitar o roteamento de trânsito entre várias redes por propagar rotas que um gateway BGP aprende de um ponto BGP para todos os outros pontos BGP.

Consulte [Visão geral de BGP com Gateways de VPN do Azure](./vpn-gateway-bgp-overview.md) para obter mais discussões sobre benefícios de BGP e entender os requisitos técnicos e considerações de usar BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introdução ao BGP em gateways VPN do Azure

Este artigo apresentará as etapas para fazer as seguintes tarefas:

- [Parte 1 - habilitar BGP no seu gateway VPN do Azure](#enablebgp)

- [Parte 2 - estabelecer uma conexão entre locais com BGP](#crossprembgp)

- [Parte 3 - estabelecer uma conexão de VNet para VNet com BGP](#v2vbgp)

Cada parte das instruções formulários um bloco de construção básico para habilitar BGP na sua conectividade de rede. Se você concluir todas as três partes, você irá criar a topologia conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar essas juntas para criar uma rede de trânsito mais complexos, vários esperanças, que atendam às suas necessidades.

## <a name ="enablebgp"></a>Parte 1 - Configurar BGP no Gateway VPN Azure

As seguintes etapas de configuração irá configurar os parâmetros BGP do gateway VPN do Azure, conforme mostrado no diagrama a seguir:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

- Verifique se você tem uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Você precisará instalar cmdlets do PowerShell do Gerenciador de recursos do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Etapa 1 - criar e configurar VNet1 

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Para este exercício, começaremos declarando nossas variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Certifique-se de substituir os valores com seu próprio durante a configuração para produção. Você pode usar essas variáveis se você estiver executando as etapas para se familiarizar com esse tipo de configuração. Modificar as variáveis e em seguida, copie e cole seu console do PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
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
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Etapa 2: criar o Gateway VPN para TestVNet1 com parâmetros BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Crie as configurações de IP e sub-rede

Solicite um endereço IP público a ser alocado para o gateway que você criará para sua VNet. Você também deverá definir as configurações de IP obrigatórias e sub-rede. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. criar o gateway VPN com o número

Crie o gateway de rede virtual para TestVNet1. Observe que BGP requer um gateway VPN baseada em rota e também o parâmetro de adição, - Asn, para definir a ASN (número) para TestVNet1. Criar um gateway pode demorar um pouco (30 minutos ou mais para ser concluída).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obter o endereço IP de ponto de BGP do Azure

Depois que o gateway é criado, você precisará obter o endereço de IP de ponto de BGP no Azure VPN Gateway. Esse endereço é necessário para configurar o Gateway de VPN do Azure como um par de BGP para os seus dispositivos VPN local.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

O último comando mostrará as configurações de BGP correspondentes no Gateway VPN Azure; Por exemplo:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Depois que o gateway é criado, você pode usar esse gateway para estabelecer conexão entre locais ou conexão de VNet para VNet com BGP. As seções a seguir orientará durante as etapas para concluir o exercício.

## <a name ="crossprembbgp"></a>Parte 2 - estabelecer uma conexão entre locais com BGP

Para estabelecer uma conexão entre locais, você precisa criar um Gateway de rede Local para representar o seu dispositivo VPN local e uma Conexão para conectar o gateway VPN Azure com o gateway de rede local. A diferença entre as instruções neste artigo é as propriedades adicionais necessárias para especificar os parâmetros de configuração de BGP.

![BGP para cruz local](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de prosseguir, certifique-se de que você concluiu a [parte 1](#enablebgp) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Etapa 1 - criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Neste exercício continuará criar a configuração mostrada no diagrama. Certifique-se de que substitua os valores por aqueles que você deseja usar para sua configuração.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Algumas coisas nota sobre os parâmetros de gateway de rede local:

- O gateway de rede local pode ser no local iguais ou diferente e grupo de recursos, como o gateway VPN. Este exemplo mostra-los em grupos de recursos diferentes em locais diferentes.

- O prefixo mínimo que é necessário declarar para o gateway de rede local é o endereço de host do seu endereço de IP de ponto de BGP em seu dispositivo VPN. Nesse caso, é um /32 prefixo de "10.52.255.254/32".

- Como um lembrete, você deve usar diferentes ASNs BGP entre suas redes de local e VNet do Azure. Se eles forem o mesmo, você precisa alterar seu ASN VNet se seu dispositivo VPN local já utiliza a ASN ao correspondente com outros vizinhos BGP.
    
Antes de continuar, verifique se que você ainda está conectado à assinatura 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para Site5

Certifique-se de criar o grupo de recursos se ele não for criado, antes de criar o gateway de rede local. Observe os dois parâmetros adicionais para o gateway de rede local: Asn e BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2: conectar o gateway VNet e o gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. obter os dois gateways

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Crie o TestVNet1 para conexão de Site5

Nesta etapa, você irá criar a conexão de TestVNet1 para Site5. Especifique "-EnableBGP $True" para habilitar o BGP para esta conexão. Conforme discutido anteriormente, é possível ter conexões de BGP e não são de BGP para o mesmo Gateway VPN Azure. A menos que BGP estiver habilitada na propriedade de conexão, Azure não irá habilitar BGP para esta conexão apesar de parâmetros BGP já estiverem configurados em ambos os gateways.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


O exemplo a seguir lista os parâmetros que você digitará para a seção de configuração de BGP em seu dispositivo VPN local para este exercício:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.254
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure ASN VNet: 65010
    - IP de BGP VNet Azure: 10.12.255.30
    - Rota estática: adicionar uma rota para 10.12.255.30/32, com nexthop sendo a interface de túnel VPN em seu dispositivo
    - eBGP Multihop: Verifique se a opção de "multihop" eBGP estiver ativado no seu dispositivo, se necessário

A conexão deve ser estabelecida após alguns minutos, e a sessão de correspondência BGP será iniciado assim que a conexão IPsec for estabelecida.
 
## <a name ="v2vbgp"></a>Parte 3 - estabelecer uma conexão de VNet para VNet com BGP

Esta seção adiciona uma conexão de VNet para VNet com BGP, conforme mostrado no diagrama abaixo. 

![BGP para VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Continuam as instruções abaixo das etapas anteriores listadas acima. Você deve concluir [Parte I](#enablebgp) para criar e configurar o Gateway de VPN e TestVNet1 com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Etapa 1 - criar TestVNet2 e o gateway VPN

É importante garantir que o espaço de endereço IP da nova rede virtual, TestVNet2, não sobreponha com qualquer um dos seus intervalos VNet.

Neste exemplo, as redes virtuais pertencem a mesma assinatura. Você pode configurar conexões de VNet para VNet entre diferentes assinaturas; consulte [Configurar uma conexão de VNet para VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para obter mais detalhes. Verifique se você adicionou o "-EnableBgp $True" ao criar as conexões para habilitar o BGP.

#### <a name="1-declare-your-variables"></a>1. declarar as variáveis

Certifique-se de que substitua os valores por aqueles que você deseja usar para sua configuração.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
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
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Crie TestVNet2 no novo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. criar o gateway VPN para TestVNet2 com parâmetros BGP

Solicite um endereço IP público a ser alocado para o gateway que você criará para sua VNet. Você também deverá definir as configurações de IP obrigatórias e sub-rede. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Crie o gateway VPN com o número. Observe que você deve substituir o padrão ASN nos seus gateways VPN do Azure. Os ASNs para a VNets conectada devem ser diferentes para habilitar o roteamento de trânsito e BGP.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2: conectar os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma assinatura. Você pode concluir esta etapa na mesma sessão do PowerShell.

#### <a name="1-get-both-gateways"></a>1. obter ambos os gateways

Verifique se você efetuar login e conectar a 1 de assinatura.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Crie conexões

Nesta etapa, você criará a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Certifique-se de habilitar BGP para ambas as conexões.

Depois de concluir essas etapas, a conexão ser estabelece em poucos minutos, e a sessão de correspondência BGP serão para cima uma vez a conexão de VNet para VNet é concluída.

Se você tiver concluído todos os três partes deste exercício, será estabelecida uma topologia de rede conforme mostrado abaixo:

![BGP para VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

