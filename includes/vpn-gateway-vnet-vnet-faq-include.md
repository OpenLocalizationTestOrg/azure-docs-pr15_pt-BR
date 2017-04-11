- As redes virtuais podem estar nas regiões Azure iguais ou diferentes (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não é possível abranger em redes virtuais, mesmo se eles estão conectados.

- Conectar várias redes virtuais Azure juntos não exige os gateways VPN local, a menos que a conectividade entre locais é necessária.

- VNet para VNet dá suporte à conexão de redes virtual. Ele não oferece suporte a conexão máquinas virtuais ou serviços não em uma rede virtual em nuvem.

- VNet para VNet requer gateways VPN Azure com RouteBased (anteriormente chamado de roteamento dinâmico) tipos VPN. 

- Conectividade de rede virtual pode ser usada simultaneamente com VPNs de vários locais, com um máximo de 10 (padrão/Gateways) ou 30 (alto desempenho Gateways) VPN túneis para um gateway VPN rede virtual se conectar a outras redes de virtuais ou sites locais.

- Os espaços de endereço dos sites de rede local redes e local virtuais não devem ser sobrepostos. Espaços de endereço sobrepostos fará com que a criação de conexões de VNet para VNet falha.

- Túneis redundantes entre um par de redes virtuais não são suportados.

- Todos os túneis VPN da rede virtual compartilham largura de banda disponível em gateway VPN do Azure e a mesma atividade de gateway VPN SLA no Azure.

- O tráfego de VNet para VNet viaja pela Microsoft Network, não a Internet.

- Tráfego de VNet para VNet dentro da mesma região é gratuito ambas as direções; entre região VNet para VNet egresso tráfego é cobrado com as taxas de transferência de dados inter-VNet de saída com base nas regiões de origem. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) para obter detalhes.