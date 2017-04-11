<properties 
   pageTitle="Sobre os gateways de rede virtual rota expressa | Microsoft Azure"
   description="Saiba mais sobre os gateways de rede virtual para rota expressa."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Sobre os gateways de rede virtual para rota expressa


Um gateway de rede virtual é usado para enviar tráfego de rede entre redes virtuais Azure e locais no local. Quando você configura uma conexão de rota expressa, você deve criar e configurar um gateway de rede virtual e uma conexão de gateway de rede virtual.

Quando você cria um gateway de rede virtual, você pode especificar várias configurações. Uma das configurações necessárias Especifica se o gateway será usado para o tráfego rota expressa ou -to-Site VPN. No modelo de implantação do Gerenciador de recursos, a configuração é '-GatewayType'.

Quando o tráfego de rede é enviado em uma conexão privada dedicada, você pode usar o tipo de gateway 'Rota expressa'. Isso também é conhecido como um gateway rota expressa. Quando o tráfego de rede é enviado criptografado através da Internet pública, você pode usar o tipo de gateway 'Vpn'. Isso é conhecido como um gateway VPN. To-Site, ponto-a-Site e conexões de VNet para VNet usam um gateway VPN. 

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter uma rede virtual gateway que usa - GatewayType Vpn e que usa - GatewayType rota expressa. Este artigo aborda o gateway de rede virtual rota expressa.

## <a name="gwsku"></a>SKUs de gateway

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se desejar atualizar o gateway para um gateway mais eficazes SKU, na maioria dos casos, você pode usar o cmdlet do PowerShell 'Redimensionamento-AzureRmVirtualNetworkGateway'. Isso funcionará para atualizações padrão e HighPerformance SKUs. No entanto, para atualizar para o SKU UltraPerformance, você precisará recriar o gateway.

###  <a name="aggthroughput"></a>Estimada transferência agregada pelo gateway SKU


A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Esta tabela se aplica ao gerente de recursos e modelos de implantação clássico.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Cmdlets APIs REST e PowerShell

Para requisitos de sintaxe específica ao usar APIs REST e cmdlets do PowerShell para configurações de gateway de rede virtual e recursos técnicos adicionais, consulte as seguintes páginas:

|**Clássico** | **Gerenciador de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Próximas etapas

Consulte [Visão geral de rota expressa](expressroute-introduction.md) para obter mais informações sobre configurações de conexão disponíveis. 







 
