<properties 
   pageTitle="Sobre o Gateway VPN | Microsoft Azure"
   description="Saiba mais sobre conexões de Gateway VPN para redes virtuais do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>Sobre o Gateway VPN


Um gateway de rede virtual é usado para enviar tráfego de rede entre redes virtuais Azure e locais de local e também entre redes virtuais dentro do Azure (VNet-para-VNet). Quando você configura um gateway VPN, você deve criar e configurar um gateway de rede virtual e uma conexão de gateway de rede virtual.

No modelo de implantação do Gerenciador de recursos, quando você cria um recurso de gateway de rede virtual, você especifica várias configurações. Uma das configurações necessárias é '-GatewayType'. Há dois tipos de gateway de rede virtual: Vpn e rota expressa. 

Quando o tráfego de rede é enviado em uma conexão privada dedicada, você pode usar o tipo de gateway 'Rota expressa'. Isso também é conhecido como um gateway rota expressa. Quando o tráfego de rede é enviado criptografado entre uma conexão pública, você pode usar o tipo de gateway 'Vpn'. Isso é conhecido como um gateway VPN. To-Site, ponto-a-Site e conexões de VNet para VNet usam um gateway VPN.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter uma rede virtual gateway que usa - GatewayType rota expressa e que usa - GatewayType Vpn. Este artigo se concentra principalmente em Gateway VPN. Para obter mais informações sobre a rota expressa, consulte [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Preços

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>SKUs de gateway

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para obter mais informações sobre o gateway SKUs, consulte [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Estimada transferência agregada por SKU

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Esta tabela se aplica ao gerente de recursos e modelos de implantação clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configurando um Gateway VPN

Quando você configura um gateway VPN, as instruções que você usa dependem do modelo de implantação que você usou para criar sua rede virtual. Por exemplo, se você criou sua VNet usando o modelo clássico de implantação, use as diretrizes e instruções para o modelo de implantação clássico para criar e configurar suas configurações de gateway VPN. Para obter mais informações sobre os modelos de implantação, consulte [Gerenciador de recursos de compreensão e modelos de implantação clássico](../resource-manager-deployment-model.md).

Uma conexão de gateway VPN depende de vários recursos que estão configurados com configurações específicas. A maioria dos recursos pode ser configurada separadamente, embora eles devem ser configurados em uma determinada ordem em alguns casos. Você pode começar a criar e configurar recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode depois decidir mudar para outra ferramenta, como o PowerShell, configurar recursos adicionais ou modificar recursos existentes quando aplicável. Atualmente, você não pode configurar cada recurso e configuração do recurso no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária. Para obter informações sobre as configurações de Gateway VPN e recursos individuais, consulte [configurações de Gateway sobre VPN](vpn-gateway-about-vpn-gateway-settings.md).

As seções a seguir contêm tabelas que listam:

- modelo de implantação disponível
- ferramentas de configuração disponíveis
- links que o levam diretamente para um artigo, se disponível

Use os diagramas e descrições para ajudar a selecionar a topologia de conexão para combinar com suas necessidades. Os diagramas mostram as topologias de linha de base principal, mas é possível criar configurações mais complexas usando os diagramas como orientação.

## <a name="site-to-site-and-multi-site"></a>To-Site e em vários locais

### <a name="site-to-site"></a>To-Site

Uma conexão de gateway-to-Site (S2S) VPN é uma conexão pela túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público atribuído a ele e não está atrás de um NAT. Conexões de S2S podem ser usadas para entre locais e híbridos configurações.   

![Conexão S2S] (./media/vpn-gateway-about-vpngateways/demos2s.png "to-site")


### <a name="multi-site"></a>Vários locais

Você pode criar e configurar uma conexão de gateway VPN entre seu VNet e várias redes de local. Ao trabalhar com várias conexões, você deve usar um tipo de RouteBased VPN (gateway dinâmico para VNets clássico). Como um VNet só pode ter um gateway VPN, todas as conexões através do gateway compartilham largura de banda disponível. Isso geralmente é chamado uma conexão de "vários local".
 

![Conexão de vários local] (./media/vpn-gateway-about-vpngateways/demomulti.png "vários locais")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implantação e métodos para-to-Site e em vários locais

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet para VNet

Conectando a uma rede virtual para outra rede virtual (VNet-para-VNet) é semelhante à conectando um VNet para um local de site local. Os dois tipos de conectividade usam um gateway VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode até mesmo combinar comunicação de VNet para VNet com configurações de conexão de vários locais. Isso permite estabelecer topologias de rede que combinam a conectividade entre locais com conectividade de rede entre virtual.

O VNets que você se conecta pode ser:

- nas regiões iguais ou diferentes
- nas assinaturas iguais ou diferentes 
- nos mesmo diferentes modelos de implantação


![VNet para conexão de VNet] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet para vnet")

#### <a name="connections-between-deployment-models"></a>Conexões entre os modelos de implantação

Azure atualmente tem dois modelos de implantação: clássico e Gerenciador de recursos. Se você tem usado o Azure por algum tempo, você provavelmente têm VMs do Azure e funções de instância em execução em um VNet clássico. Seu VMs mais recentes e instâncias de função podem estar executando em um VNet criado no Gerenciador de recursos. Você pode criar uma conexão entre o VNets para permitir que os recursos de um VNet para se comunicar diretamente com recursos em outro.

#### <a name="vnet-peering"></a>Correspondência de VNet

Você pode ser capaz de usar VNet correspondência para criar sua conexão, desde que sua rede virtual atende a certos requisitos. Correspondência de VNet não usa um gateway de rede virtual. Para obter mais informações, consulte [VNet correspondência](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implantação e métodos para VNet para VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Ponto-a-Site

Uma conexão de gateway VPN de ponto-a-Site (P2S) permite que você criar uma conexão segura à sua rede virtual a partir de um computador cliente individual. P2S é uma conexão de VPN SSTP (protocolo SSTP). Conexões de P2S não exigem um dispositivo VPN ou um endereço IP público para trabalhar. Você pode estabelecer a conexão VPN por iniciá-lo do computador cliente. Essa solução é útil quando você deseja se conectar seu VNet de um local remoto, como em casa ou em uma conferência, ou quando tiver apenas alguns clientes que precisam se conectar a um VNet. Conexões de P2S podem ser usados em conjunto com conexões de S2S através do mesmo gateway VPN, desde que todos os requisitos de configuração para ambas as conexões são compatíveis.


Conexão ponto-a-site ![] (./media/vpn-gateway-about-vpngateways/demop2s.png "ponto-a-site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modelos de implantação e métodos para ponto-a-Site

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>Rota expressa

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Em uma conexão de rota expressa, um gateway de rede virtual está configurado com o tipo de gateway 'Rota expressa', ao invés de 'Vpn'. Para obter mais informações sobre a rota expressa, consulte [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Conexões de passa-to-Site e rota expressa

Rota expressa é uma conexão direto e dedicado de sua WAN (não pela Internet pública) ao Microsoft Services, incluindo o Azure. To-Site VPN tráfego viaja criptografadas pela Internet pública. Ser capaz de configurar conexões VPN de Site para Site e rota expressa para a mesma rede virtual tem várias vantagens.

Você pode configurar uma VPN to-Site como um caminho de failover seguro para rota expressa ou usar VPNs Site-to-Site para se conectar aos sites que não fazem parte da sua rede, mas que estão conectadas por meio de rota expressa. Observe que isso requer dois gateways de rede virtual para a mesma rede virtual, usando uma - GatewayType Vpn e outra usando - GatewayType rota expressa.


![Conexão Coexist] (./media/vpn-gateway-about-vpngateways/demoer.png "rota expressa-site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelos de implantação e métodos para S2S e rota expressa

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Próximas etapas

Planeje sua configuração de gateway VPN. Consulte [planejamento de Gateway VPN e Design](vpn-gateway-plan-design.md) e [conectar sua rede local para o Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
