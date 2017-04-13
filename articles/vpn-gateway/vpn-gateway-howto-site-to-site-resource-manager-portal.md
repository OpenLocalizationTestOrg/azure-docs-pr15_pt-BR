<properties
   pageTitle="Criar uma rede virtual com uma conexão de VPN to-Site usando o Gerenciador de recursos do Azure e o Portal do Azure | Microsoft Azure"
   description="Como criar VNet usando o modelo de implantação do Gerenciador de recursos e conecte-o ao seu local locais rede usando uma conexão de gateway VPN de S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Criar um VNet com uma conexão de-to-Site usando o portal do Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - Portal clássico](vpn-gateway-site-to-site-create.md)


Este artigo explica a criação de uma rede virtual e uma conexão de gateway-to-Site VPN à sua rede local usando o modelo de implantação do Gerenciador de recursos do Azure e o portal do Azure. Conexões de-to-Site podem ser usadas para entre locais e híbridos configurações.

![Diagrama](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implantação e métodos para conexões to-Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os métodos para configurações de Site para Site e modelos de implantação disponível no momento. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais 

Se você deseja conectar VNets juntos, mas não estiver criando uma conexão para um local no local, consulte [Configurar uma conexão de VNet para VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se você quiser adicionar uma conexão de-to-Site a uma VNet que já tenha uma conexão, consulte [Adicionar uma conexão de S2S para um VNet com uma conexão de gateway VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar sua configuração:

- Um dispositivo VPN compatível e alguém que esteja configurá-la. Consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu local, você precisa coordenar com alguém que pode fornecer esses detalhes para você.

- Um externamente opostas público endereço IP para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.
    
- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valores de configuração de amostra para este exercício


Ao usar estas etapas como um exercício, você pode usar os valores de configuração de exemplo:

- **VNet nome:** TestVNet1
- **Espaço de endereço:** 10.11.0.0/16 e 10.12.0.0/16
- **Sub-redes:**
    - FrontEnd: 10.11.0.0/24
    - Back-end: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **Grupo de recursos:** TestRG1
- **Local:** Leste EUA
- **Servidor DNS:** 8.8.8.8
- **Nome do gateway:** VNet1GW
- **IP público:** VNet1GWIP
- **Tipo VPN:** Baseada em roteiro
- **Tipo de Conexão:** To-site (IPsec)
- **Tipo de gateway:** VPN
- **Nome do Gateway de rede local:** Site2
- **Nome de Conexão:** VNet1toSite2


## <a name="CreatVNet"></a>1. criar uma rede virtual 

Se você já tiver um VNet, verifique se as configurações são compatíveis com o seu design de gateway VPN. Atenção especial para quaisquer sub-redes que podem se sobrepor com outras redes. Se você tiver sobrepostos sub-redes, sua conexão não funcionarão corretamente. Se seu VNet estiver configurado com as configurações corretas, você pode começar as etapas na seção [especificar um servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. adicionar espaço de endereço adicional e sub-redes

Você pode adicionar espaço de endereço adicional e sub-redes a sua VNet depois que ela foi criada.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. especificar um servidor DNS

### <a name="to-specify-a-dns-server"></a>Para especificar um servidor DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. criar uma sub-rede de gateway

Antes de conectar sua rede virtual para um gateway, primeiro é necessário criar a sub-rede de gateway para a rede virtual à qual você deseja se conectar. Se possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27 para fornecer endereços IP suficientes para acomodar requisitos adicionais de configuração de futuras.

Se você estiver criando esta configuração como um exercício, consulte estes [valores](#values) ao criar sua sub-rede do gateway.

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. criar um gateway de rede virtual

Se você estiver criando esta configuração como um exercício, você pode fazer referência aos [valores de configuração de amostra](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. criar um gateway de rede local

O gateway de rede local' ' se refere ao seu local de local. Nomeie o gateway de rede local pelo qual o Azure pode se referir a ela. 

Se você estiver criando esta configuração como um exercício, você pode fazer referência aos [valores de configuração de amostra](#values).

### <a name="to-create-a-local-network-gateway"></a>Para criar um gateway de rede local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. configurar seu dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. criar uma conexão de VPN to-Site

Crie a conexão de-to-Site VPN entre seu gateway de rede virtual e o dispositivo VPN. Certifique-se de que substitua os valores por suas próprias. A chave compartilhada deve corresponder ao valor que você usou para sua configuração de dispositivo VPN. 

Antes de começar nesta seção, verifique se que o gateway de rede virtual e gateways de rede local terminou de criar. Se você estiver criando esta configuração como um exercício, consulte estes [valores](#values) ao criar sua conexão.

### <a name="to-create-the-vpn-connection"></a>Para criar a conexão VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Verifique se a conexão VPN

Você pode verificar sua conexão de VPN no portal, ou usando o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas

- Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte as máquinas virtuais [caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obter mais informações.

- Para obter informações sobre BGP, consulte a [Visão geral de BGP](vpn-gateway-bgp-overview.md) e [como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
