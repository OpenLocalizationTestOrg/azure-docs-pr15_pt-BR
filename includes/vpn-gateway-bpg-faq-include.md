### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP é suportado em todas as SKUs de Gateway VPN Azure?

Não, BGP é suportado no Azure gateways VPN **padrão** e **HighPerformance** . **Básico** SKU não é suportada.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Pode usar BGP com gateways VPN Azure Policy-Based?

Não, BGP é suportado em gateways VPN baseada em rota somente.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Posso usar ASNs particulares (números de sistema autônomo)?

Sim, você pode usar seu próprio ASNs públicos ou particulares ASNs para suas redes locais e redes virtuais Azure.

#### <a name="are-there-asns-reserved-by-azure"></a>Existem ASNs reservados pelo Azure?

Sim, os seguintes ASNs são reservados pela Azure para peerings internos e externos:

- ASNs públicos: 8075, 8076, 12076
- Particular ASNs: 65515, 65517, 65518, 65519, 65520

Você não pode especificar estes ASNs para seu local em dispositivos VPN ao conectar com gateways VPN do Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Pode usar a mesma ASN para redes VPN local e o Azure VNets?

Não, você deve atribuir ASNs diferentes entre suas redes local e sua VNets Azure se você estiver conectando-los junto com o BGP. Azure Gateways VPN possui um padrão ASN de 65515 atribuído, se BGP está habilitado para não para a conectividade entre locais. Você pode substituir esse padrão atribuindo um ASN diferente ao criar o gateway VPN ou alterar a ASN após o gateway é criado. Você precisa atribuir ASNs seu local para os Gateways de correspondente rede de Local do Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quais prefixos de endereço gateways VPN Azure anunciará para mim?

Azure gateway VPN anunciará as seguintes rotas para seus dispositivos BGP local:

- Seus prefixos de endereço VNet
- Prefixos de endereço para cada Gateways de rede Local conectado ao gateway VPN do Azure
- Rotas aprendidas de outras sessões de correspondência BGP conectados ao gateway VPN do Azure, **exceto padrão ou mais rotas sobrepostas com qualquer prefixo VNet**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota padrão (0.0.0.0/0) para gateways VPN Azure?

Não neste momento.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Pode anunciar os prefixos exatos como prefixos minha rede Virtual?

Não, anunciar os mesmos prefixos como qualquer um de sua rede Virtual prefixos de endereço serão bloqueados ou filtrados por plataforma Windows Azure. No entanto, você pode anunciar um prefixo que é um subconjunto do que você tem dentro da sua rede Virtual. Por exemplo, sua rede Virtual poderia usar o endereço espaço 10.10.0.0/16 e você pode anunciar 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Pode usar BGP com minhas conexões VNet para VNet?

Sim, você pode usar BGP para conexões de VNet para VNet e de conexões entre locais.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar BGP com conexões não-BGP para minha gateways VPN Azure?

Sim, você pode misturar conexões BGP e não são de BGP para o mesmo gateway VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Faz roteamento de trânsito BGP do Azure VPN gateway suporte?

Sim, roteamento de trânsito BGP é compatível, com exceção de que **não** serão de gateways VPN Azure anuncia rotas padrão para outros pontos BGP. Para habilitar o roteamento de trânsito entre vários gateways VPN do Azure, você deve habilitar o BGP em todas as conexões de VNet para VNet intermediárias.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais de um encapsulamentos entre gateway VPN do Azure e minha rede local?

Sim, você pode estabelecer túneis VPN S2S mais de uma entre um gateway VPN do Azure e sua rede local. Observe que todos esses encapsulamentos serão contados contra o número total de encapsulamentos para seus gateways VPN do Azure. Por exemplo, se você tiver dois encapsulamentos redundantes entre o gateway VPN do Azure e um da sua rede local, eles serão consumir 2 túneis fora a cota de total para o gateway de VPN do Azure (10 para padrão) e 30 para HighPerformance.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Posso ter vários encapsulamentos entre duas VNets Azure com BGP?

Não, não há suporte encapsulamentos redundantes entre um par de redes virtuais.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Posso usar BGP para S2S VPN em uma configuração de coexistência rota expressa/S2S VPN?

Não neste momento.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Qual endereço gateway VPN do Azure, usa para BGP ponto IP?

O gateway VPN do Azure será alocar um único endereço IP no intervalo de GatewaySubnet definido para a rede virtual. Por padrão, é o segundo último endereço do intervalo. Por exemplo, se sua GatewaySubnet for 10.12.255.0/27, que varia de 10.12.255.0 a 10.12.255.31, em seguida, o endereço de IP de ponto de BGP no gateway VPN Azure será 10.12.255.30. Você pode encontrar essas informações quando você lista as informações de gateway VPN do Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para os endereços de IP de ponto de BGP em meu dispositivo VPN?

BGP local endereço de mesmo nível **Não deve** ser igual o endereço IP público do seu dispositivo VPN. Use um endereço IP diferente do dispositivo de VPN para o IP de ponto de BGP. Pode ser um endereço atribuído a interface de loopback no dispositivo. Especifique esse endereço no Gateway de rede Local correspondente que representa o local.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que deve para especificar como prefixos meu endereço para o Gateway de rede Local quando usa o BGP?

Gateway de rede Local Azure Especifica os prefixos de endereço inicial para a rede local. Com BGP, você deve alocar o prefixo de host (/ 32 prefixo) de seu endereço de IP de ponto de BGP como o espaço de endereço de rede local. Se o seu IP de ponto BGP 10.52.255.254, você deverá especificar "10.52.255.254/32" como o localNetworkAddressSpace do Gateway rede Local que representa a esta rede local. Isso é para garantir que o gateway VPN Azure estabelece a sessão BGP através do túnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que é aconselhável adicionar ao meu dispositivo VPN local para sessão de correspondência BGP?

Você deve adicionar uma rota de host do endereço IP do Azure BGP ponto em seu dispositivo VPN apontando para túnel IPsec S2S VPN. Por exemplo, se o IP de mesmo nível do Azure VPN é "10.12.255.30", você deve adicionar uma rota de host para "10.12.255.30" com uma interface nexthop da interface de túnel IPsec correspondente em seu dispositivo VPN.
