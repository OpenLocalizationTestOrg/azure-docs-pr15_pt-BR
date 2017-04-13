<properties
   pageTitle="Visão geral de BGP com Gateways VPN Azure | Microsoft Azure"
   description="Este artigo fornece uma visão geral de BGP com Gateways de VPN do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Visão geral de BGP com Gateways VPN Azure

Este artigo fornece uma visão geral do suporte BGP (protocolo de Gateway de borda) no Azure VPN Gateways.

## <a name="about-bgp"></a>Sobre BGP

BGP é o protocolo de roteamento padrão comumente usado na Internet para trocar informações de roteamento e acessibilidade entre duas ou mais redes. Quando usada no contexto de redes virtuais do Azure, BGP permite que os Gateways de VPN do Azure e seus dispositivos VPN local, chamados BGP colegas ou vizinhos, trocar "roteia" informará ambos os gateways sobre a disponibilidade e a acessibilidade para esses prefixos percorrer os gateways ou roteadores envolvidos. BGP também pode habilitar o roteamento de trânsito entre várias redes por propagar rotas que um gateway BGP aprende de um ponto BGP para todos os outros pontos BGP.
 
### <a name="why-use-bgp"></a>Por que usar BGP?

BGP é um recurso opcional que você pode usar com gateways VPN baseada em rota Azure. Você também deverá garantir que seus dispositivos VPN locais suportam BGP antes de habilitar o recurso. Você pode continuar a usar gateways VPN do Azure e seus dispositivos VPN local sem BGP. É o equivalente do uso de rotas estáticas (sem BGP) *versus* usando o roteamento dinâmico com BGP entre suas redes e Azure.

Há várias vantagens e novos recursos com BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Oferecer suporte a atualizações automáticas e flexível prefixo

Com BGP, você só precisa declarar um prefixo mínimo para um ponto específico de BGP pela túnel IPsec S2S VPN. Pode ser tão pequeno quanto um prefixo host (/ 32) do endereço IP BGP ponto do seu dispositivo VPN local. Você pode controlar qual local prefixos de rede que você deseja anunciar ao Azure para permitir que sua rede Virtual do Azure acessar.
    
Você também pode anunciar prefixos maiores que podem incluir alguns dos seus prefixos de endereço de VNet, como um espaço de endereço IP de particular grande (por exemplo, 10.0.0.0/8). Observe Embora os prefixos não podem ser idênticos com qualquer um dos seus prefixos VNet. Essas rotas idênticas ao seu prefixos VNet serão rejeitadas.

>[AZURE.IMPORTANT] Atualmente, anuncia a rota padrão (0.0.0.0/0) para gateways VPN Azure será bloqueada. Atualização adicional será fornecida depois que esse recurso é habilitado.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Suporte a vários encapsulamentos entre um VNet e um site local com failover automático com base em BGP

Você pode estabelecer várias conexões entre seus VNet do Azure e seus dispositivos VPN local no mesmo local. Esse recurso fornece vários encapsulamentos (caminhos) entre as duas redes em uma configuração ativa. Se um dos encapsulamentos for desconectado, as rotas correspondentes serão ser recusadas via BGP e o tráfego mudará automaticamente para os encapsulamentos restantes.
    
O diagrama a seguir mostra um exemplo simples dessa configuração altamente disponível:
    
![Vários caminhos ativos](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Suporte a roteamento de trânsito entre suas redes locais e vários VNets do Azure

BGP permite que vários gateways aprender e propagar prefixos de diferentes redes, independentemente de eles estarem direta ou indiretamente conectados. Isso pode habilitar o roteamento de trânsito com gateways VPN Azure entre seus sites locais ou em várias redes de Virtual do Azure.
    
O diagrama a seguir mostra um exemplo de uma topologia de vários salto com vários caminhos que pode tráfego entre as duas redes local por meio de gateways de VPN do Azure dentro do Microsoft Networks de trânsito:

![Vários salto trânsito](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Perguntas frequentes do BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Próximas etapas

Consulte [Introdução aos BGP em gateways VPN do Azure](./vpn-gateway-bgp-resource-manager-ps.md) para obter as etapas para configurar o BGP para suas conexões de VNet para VNet e entre locais.

