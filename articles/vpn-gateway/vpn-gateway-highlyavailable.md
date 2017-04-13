<properties
   pageTitle="Visão geral das configurações altamente disponível com Gateways de VPN do Azure | Microsoft Azure"
   description="Este artigo fornece uma visão geral das opções de configuração altamente disponível usando Azure VPN Gateways."
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
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Conectividade de VNet para VNet e altamente disponível entre locais

Este artigo fornece uma visão geral das opções de configuração altamente disponíveis para seu local cruzado e conectividade de VNet para VNet usando gateways VPN do Azure.

## <a name = "activestandby"></a>Sobre redundância de gateway VPN do Azure

Cada gateway VPN Azure consiste em duas instâncias em uma configuração de espera ativo. Qualquer manutenção planejada ou interrupções planejadas que acontece com a instância ativa, a instância de espera faria assumir (failover) automaticamente e retomar a VPN S2S ou conexões de VNet para VNet. A opção sobre causará uma breve interrupção. Para a manutenção planejada, a conectividade deve ser restaurada dentro de 10 a 15 segundos. Para problemas planejados, a recuperação de conexão será mais, aproximadamente 1 minuto a 1 e meia minutos no pior caso. Para conexões VPN de P2S de cliente para o gateway, as conexões de P2S serão desconectadas e os usuários precisarão reconectar das máquinas cliente.

![Modo de espera ativo](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Conectividade entre locais altamente disponível

Para fornecer melhor disponibilidade para suas conexões entre locais, existem algumas opções disponíveis:

- Vários dispositivos VPN de local
- Gateway do Azure VPN ativa
- Combinação dos dois

### <a name = "activeactiveonprem"></a>Vários dispositivos VPN de local

Você pode usar vários dispositivos VPN da sua rede local para se conectar ao seu gateway VPN do Azure, conforme mostrado no diagrama a seguir:

![Vários locais VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Essa configuração fornece vários encapsulamentos ativos do mesmo gateway VPN do Azure para seus dispositivos de locais no mesmo local. Existem alguns requisitos e restrições:

1. Você precisa criar várias conexões VPN S2S de seus dispositivos VPN para Azure. Quando você conecta vários dispositivos VPN da mesma rede local ao Azure, é necessário criar um gateway de rede local para cada dispositivo VPN e uma conexão de seu gateway VPN do Azure para o gateway de rede local.

2. Os gateways de rede local correspondente a seus dispositivos VPN devem ter endereços IP públicos exclusivos na propriedade "GatewayIpAddress".

3. BGP é necessária para essa configuração. Cada gateway de rede local que representa um dispositivo VPN deve ter um endereço IP de ponto BGP exclusivo especificado na propriedade "BgpPeerIpAddress".

4. O campo de propriedade AddressPrefix em cada gateway de rede local não deve ser sobrepostos. Você deverá especificar o "BgpPeerIpAddress" /32 formato CIDR no campo AddressPrefix, por exemplo, 10.200.200.254/32.

5. Você deve usar BGP para anunciar os mesmos prefixos do mesmo prefixos de rede para o gateway VPN Azure local e o tráfego será encaminhado através esses encapsulamentos simultaneamente.

6. Cada conexão é contado contra o número máximo de encapsulamentos para o gateway de VPN do Azure, 10 para Basic e SKUs padrão e 30 para HighPerformance SKU. 

Nessa configuração, o gateway VPN Azure ainda está em modo de espera ativo, para que o mesmo comportamento de failover e breve interrupção ainda acontecerá conforme descrito [acima](#activestandby). Mas essa configuração protege contra falhas ou interrupções em sua rede local e dispositivos VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Gateway do Azure VPN ativa

Agora você pode criar um gateway VPN do Azure em uma configuração ativa, onde ambas as instâncias de gateway VMs estabelece túneis de S2S VPN para o seu dispositivo VPN local, como mostra o diagrama a seguir:

![Ativa](./media/vpn-gateway-highlyavailable/active-active.png)

Nessa configuração, cada instância de gateway Azure terá um endereço IP público exclusivo, e cada estabelece um túnel IPsec/IKE S2S VPN para o seu dispositivo VPN locais especificado em seu gateway de rede local e a conexão. Observe que os dois encapsulamentos VPN são realmente parte da mesma conexão. Você ainda precisa configurar seu dispositivo VPN local para aceitar ou estabelecer dois encapsulamentos VPN S2S essas duas Azure VPN gateway endereços IP públicos.

Como as instâncias de gateway Azure estão em configuração ativo-ativo, o tráfego da sua rede virtual Azure à sua rede local será roteado por meio de ambos os encapsulamentos simultaneamente, mesmo se seu dispositivo VPN local pode preferir uma túnel acima da outra. Observação Embora o mesmo fluxo TCP ou UDP sempre irá desviar a mesma túnel ou caminho, a menos que um evento de manutenção acontece em uma das instâncias.

Quando um evento planejado ou manutenção planejada acontece a instância de um gateway, o túnel IPsec da instância ao seu dispositivo VPN local será desconectado. As rotas correspondentes em seus dispositivos VPN devem ser removidas ou recusadas automaticamente para que o tráfego será transferido para outro túnel IPsec ativo. No lado do Azure, a opção sobre acontecerá automaticamente da instância afetada na instância ativa.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Redundância de dupla: gateways VPN ativa para redes do Azure e local

A opção mais confiável é combinar os gateways ativa na sua rede e o Azure, conforme mostrado no diagrama abaixo.

![Redundância dupla](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Aqui você cria e configurar o gateway VPN do Azure em uma configuração ativa e cria dois gateways de rede local e duas conexões para seu dois locais dispositivos VPN conforme descrito acima. O resultado é uma conectividade de malha completa de 4 encapsulamentos de segurança IP entre sua rede virtual Azure e sua rede local.

Todos os gateways e encapsulamentos estão ativos do Azure lado, para que o tráfego será ser distribuído entre todos os 4 encapsulamentos simultaneamente, embora cada fluxo TCP ou UDP novamente seguirá o mesmo túnel ou o caminho do lado Azure. Apesar distribuindo o tráfego, você poderá ver um pouco melhor produtividade sobre os encapsulamentos de segurança IP, o objetivo principal dessa configuração é para alta disponibilidade. E devido a natureza estatística da disseminação, é difícil fornecer que as unidades de medida em condições como diferentes de tráfego de aplicativo afetará a produtividade agregada.

Essa topologia exigirá dois gateways de rede local e duas conexões para dar suporte o par de dispositivos VPN local e BGP é necessária para permitir que as duas conexões à mesma rede local. Esses requisitos são iguais a [acima](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Conectividade de VNet para VNet altamente disponível por meio de Gateways VPN Azure

A mesma configuração ativa também pode aplicar às conexões de VNet para VNet Azure. Você pode criar gateways VPN ativa para ambas as redes virtuais e conectá-los ao formulário a mesma conectividade de malha completa de 4 túneis entre as duas VNets, conforme mostrado no diagrama abaixo:

![VNet para VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Isso garante que sempre há um par de túneis entre as duas redes virtuais para qualquer eventos de manutenção planejada, fornecendo disponibilidade ainda melhor. Embora a topologia mesmo para cruz local conectividade requer duas conexões, a topologia de VNet para VNet mostrada acima será necessário apenas uma conexão para cada gateway. Além disso, BGP é opcional, a menos que o roteamento de trânsito sobre a conexão de VNet para VNet é necessário.


## <a name="next-steps"></a>Próximas etapas

Consulte [Configurando Gateways de VPN ativa para entre locais e conexões de VNet para VNet](vpn-gateway-activeactive-rm-powershell.md) para obter as etapas para configurar conexões de VNet para VNet e ativa entre locais.
