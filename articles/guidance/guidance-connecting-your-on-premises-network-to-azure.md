<properties
   pageTitle="Conectando a sua rede local ao Azure | Microsoft Azure"
   description="Explica e compara os diferentes métodos disponíveis para conexão com serviços de nuvem da Microsoft como Azure, Office 365 e Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Conectando a sua rede local para o Azure

A Microsoft fornece vários tipos de serviços de nuvem. Enquanto você pode se conectar a todos os serviços na Internet pública, você pode também conectar alguns dos serviços usando um túnel de rede virtual privada (VPN) pela Internet ou uma conexão direta, privada à Microsoft. Este artigo ajuda você a determinar qual opção de conectividade melhor atenda às suas necessidades com base nos tipos de serviços de nuvem da Microsoft que você consumir. A maioria das organizações utilizar vários tipos de conexão descritos a seguir.

## <a name="connecting-over-the-public-internet"></a>Conectando através da Internet pública

Esse tipo de conexão fornece acesso aos serviços de nuvem da Microsoft diretamente pela Internet, conforme mostrado abaixo.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Esta conexão é geralmente o primeiro tipo usado para conexão com serviços de nuvem da Microsoft. A tabela a seguir lista os prós e contras desse tipo de conexão.



| **Benefícios**| **Considerações**|
|---------|---------|
|Não requer modificação à sua rede local contanto que todos os dispositivos cliente tem acesso ilimitado a todos os endereços IP e portas na Internet.|Embora o tráfego geralmente é criptografado usando HTTPS, podem ser interceptada em trânsito desde que ele percorre a Internet pública.|
|Pode se conectar a todos os serviços de nuvem da Microsoft expostos à Internet pública.|Latência imprevisível porque a conexão percorre na Internet.|
|Usa sua conexão de Internet existente.||
|Não requer o gerenciamento de quaisquer dispositivos de conectividade.||

Esta conexão não tem conectividade ou custos de largura de banda desde que você usar sua conexão de Internet existente. 

## <a name="connecting-with-a-point-to-site-connection"></a>Conectar com uma conexão ponto-a-site

Esse tipo de conexão fornece acesso para alguns serviços de nuvem da Microsoft através de um túnel seguro soquete túnel SSTP (Protocol) pela Internet, conforme mostrado abaixo.

![P2S] Conexão ponto-a-site (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "")

A conexão é feita pela sua conexão de Internet existente, mas requer o uso de um Gateway de VPN do Azure. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios**| **Considerações**|
|---------|---------|
|Não requer modificação à sua rede local contanto que todos os dispositivos cliente tem acesso ilimitado a todos os endereços IP e portas na Internet.|Embora o tráfego é criptografado usando segurança IP, podem ser interceptada em trânsito desde que ele percorre a Internet pública.|
|Usa sua conexão de Internet existente.|Latência imprevisível porque a conexão percorre na Internet.|
|Taxa de transferência até 200 Mb/s por gateway.|Requer a criação e gerenciamento de conexões separadas entre cada dispositivo em sua rede local e cada gateway a que cada dispositivo precisa se conectar.|
|Pode ser usado para se conectar aos serviços Azure que podem ser conectados a um redes virtuais do Azure (VNet) como máquinas virtuais do Azure e serviços de nuvem do Azure.|Requer o mínimo administração contínua de um Gateway de VPN do Azure.|
||Não podem ser usados para se conectar ao Microsoft Office 365 ou Dynamics CRM Online.
||Não podem ser usados para se conectar aos serviços Azure que não podem ser conectados a um VNet.|

Saiba mais sobre o serviço de [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) , seus [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)e dados de saída [preços](https://azure.microsoft.com/pricing/details/data-transfers)de transferência.

## <a name="connecting-with-a-site-to-site-connection"></a>Conectar com uma conexão de-to-site

Esse tipo de conexão fornece acesso para alguns serviços de nuvem da Microsoft por meio de um túnel IPSec pela Internet, conforme mostrado abaixo.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Conexão to-site")

A conexão é feita pela sua conexão de Internet existente, mas requer o uso de um Gateway de VPN Azure com seu preço associado e preços de transferência de dados de saída. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios**| **Considerações**|
|---------|---------|
|Todos os dispositivos na sua rede local possam se comunicar com os serviços do Azure conectados a um VNet para que não é necessário configurar conexões individuais para cada dispositivo.|Embora o tráfego é criptografado usando segurança IP, podem ser interceptada em trânsito desde que ele percorre a Internet pública.|
|Usa sua conexão de Internet existente.|Latência imprevisível porque a conexão percorre a Internet.|
|Pode ser usado para se conectar ao Azure serviços que podem ser conectados a um VNet como máquinas virtuais e serviços de nuvem.|Configure e gerencie uma validadas dispositivo VPN * local.|
|Taxa de transferência até 200 Mb/s por gateway.|Requer o mínimo administração contínua de um Gateway de VPN do Azure.|
|Pode forçar o tráfego de saída iniciado a partir de máquinas virtuais de nuvem através da rede local para inspeção e registro em log usando rotas definidas pelo usuário ou o protocolo de Gateway de borda (BGP) * *.|Não podem ser usados para se conectar ao Microsoft Office 365 ou Dynamics CRM Online.|
||Não podem ser usados para se conectar aos serviços Azure que não podem ser conectados a um VNet.|
||Se você usar os serviços que iniciam conexões novamente para dispositivos de locais e suas políticas de segurança exigem-la, talvez seja necessário um firewall entre a rede local e o Azure.|

- * Exiba uma lista de [validados dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- * * Saiba mais sobre como usar [rotas definidas pelo usuário](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forçar o roteamento de VNets do Azure para um dispositivo local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Conectar com uma conexão privada dedicada

Esse tipo de conexão fornece acesso a todos os serviços de nuvem da Microsoft ao longo de uma conexão privada dedicada à Microsoft que não percorrer a Internet, conforme mostrado abaixo.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "Rota expressa conexão")

A conexão requer o uso do serviço rota expressa e uma conexão a um provedor de conectividade. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios**| **Considerações**|
|---------|---------|
|Tráfego não pode ser interceptado em trânsito na Internet pública, como uma conexão dedicada através de um provedor de serviço é usado.|Requer o gerenciamento de roteador local.|
|Largura de banda até 10 Gb/s por circuito de rota expressa e produtividade até 2 Gb/s para cada gateway.|Requer uma conexão dedicada a um provedor de conectividade.|
|Latência previsível porque ele é uma conexão dedicada à Microsoft que não percorrer a Internet.|Pode exigir a administração em andamento mínima de uma ou mais Gateways de VPN Azure (se conectem o circuito VNets).|
|Não exige comunicação criptografada, embora você possa criptografar o tráfego, se desejar.| Se você estiver usando os serviços de nuvem que iniciam conexões novamente para dispositivos de locais, talvez seja necessário um firewall entre a rede local e o Azure.|
|Diretamente pode conectar-se a todos os serviços de nuvem do Microsoft, com algumas exceções *.|Requer a conversão de endereço de rede (NAT) de endereços IP local inserindo os centros de dados da Microsoft para serviços que não podem ser conectados a um VNet.* *|
|Pode forçar o tráfego de saída iniciado a partir de máquinas virtuais de nuvem através da rede local para inspeção e registro em log usando BGP.|

- * Exiba uma [lista dos serviços](../expressroute/expressroute-faqs.md#supported-services) que não podem ser usadas com rota expressa. Sua assinatura do Azure deve ser aprovada para se conectar ao Office 365.  Consulte o artigo de [Rota expressa do Azure para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) para obter detalhes.
- * * Saiba mais sobre rota expressa [NAT](../expressroute/expressroute-nat.md) requisitos.

Saiba mais sobre a [rota expressa](../expressroute/expressroute-introduction.md), seus associados [preços](https://azure.microsoft.com/pricing/details/expressroute)e [provedores de conectividade](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Considerações adicionais

- Várias das opções acima tem vários limites máximos, que eles podem oferecer suporte para conexões VNet, conexões de gateway e outros critérios. É recomendável que você examine o Azure [limites de rede](../azure-subscription-service-limits.md#networking-limits) para entender se qualquer uma delas afetar os tipos de conectividade que você optar por usar. 
- Se você planeja conectar-se um gateway de uma conexão de VPN-to-site para o mesmo VNet como um gateway rota expressa, você deve se familiarizar com restrições importantes primeiro. Consulte o artigo [rota expressa configurar e conexões passa - to-Site](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

Os recursos a seguir explicam como implementar os tipos de conexão descritos neste artigo.

-   [Implementar uma conexão ponto-a-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implementar uma conexão to-site](guidance-hybrid-network-vpn.md)
-   [Implementar uma conexão privada dedicada](guidance-hybrid-network-expressroute.md)
-   [Implementar uma conexão privada dedicada com uma conexão de-to-site para alta disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)
