<properties 
   pageTitle="Perguntas Frequentes de Gateway VPN de rede virtual | Microsoft Azure"
   description="O Gateway VPN perguntas Frequentes. Perguntas Frequentes sobre conexões entre locais de rede Virtual do Microsoft Azure, conexões de configuração híbrida e Gateways VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>Gateway VPN perguntas Frequentes

## <a name="connecting-to-virtual-networks"></a>Conectando ao redes virtuais

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Posso me conectar redes virtuais em diferentes regiões Azure?
Sim. Na verdade, não há nenhuma restrição de região. Uma rede virtual pode se conectar a outra rede virtual na mesma região ou em uma região diferente de Azure.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Posso me conectar redes virtuais em diferentes assinaturas?
Sim.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Posso conectar a vários sites de uma única rede virtual?

Você pode se conectar a vários sites usando o Windows PowerShell e as APIs REST do Azure. Consulte a seção de [vários locais e conectividade de VNet para VNet](#multi-site-and-vnet-to-vnet-connectivity) perguntas Frequentes.
## <a name="what-are-my-cross-premises-connection-options"></a>Quais são minhas opções de conexão entre locais?

As seguintes conexões entre locais são suportadas:

- [to-Site](vpn-gateway-site-to-site-create.md) – conexão VPN através de IPsec (IKE v1 e v2 IKE). Esse tipo de conexão requer um dispositivo VPN ou RRAS.

- [Ponto-a-Site](vpn-gateway-point-to-site-create.md) – conexão VPN sobre SSTP (protocolo SSTP). Esta conexão não exige um dispositivo VPN.

- [VNet-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – este tipo de conexão é a mesma que uma configuração de-to-Site. VNet para VNet é uma conexão VPN através de IPsec (IKE v1 e v2 IKE). Ele não exige um dispositivo VPN.

- [Vários locais](vpn-gateway-multi-site.md) – essa é uma variação de uma configuração de-to-Site que permite que você se conecte vários sites locais a uma rede virtual.

- [Rota expressa](../expressroute/expressroute-introduction.md) – rota expressa é uma conexão direta para o Azure de sua WAN, não pela Internet pública. Consulte a [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md) e a [Rota expressa FAQ](../expressroute/expressroute-faqs.md) para obter mais informações.

Para obter mais informações sobre conexões, consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Qual é a diferença entre uma conexão de Site para Site e ponto-a-Site?

Conexões de **-to-Site** permitem que você se conectar entre qualquer um dos computadores localizados em seu local a qualquer máquina virtual ou instância de função dentro de sua rede virtual, dependendo de como você optar por configurar o roteamento. É uma ótima opção para uma conexão local cruz sempre disponível e é adequado para configurações híbridas. Esse tipo de conexão baseia-se em um dispositivo de VPN IPsec (hardware ou dispositivo suave), que deve ser implantado na borda da sua rede. Para criar esse tipo de conexão, você deve ter o hardware VPN necessário e um endereço IPv4 externamente oposto.

**Ponto-a-Site** conexões permitem que você se conectar a partir de um único computador de qualquer lugar a qualquer coisa localizada na sua rede virtual. Ele usa o cliente VPN de caixa de entrada do Windows. Como parte da configuração do ponto-a-Site, você pode instalar um certificado e um pacote de configuração de cliente VPN, que contém as configurações que permitem que o computador para se conectar a qualquer máquina virtual ou instância de função dentro da rede virtual. É ótimo quando você deseja se conectar a uma rede virtual, mas não está localizados no local. Também é uma boa opção quando você não tiver acesso a hardware VPN ou um endereço IPv4 externamente oposto, que são necessários para uma conexão de-to-Site. 

Você pode configurar sua rede virtual para usar-to-Site e ponto-a-Site simultaneamente, desde que você criar sua conexão de-to-Site usando um tipo VPN baseado em rotear para seu gateway. Tipos VPN baseada em rota são chamados gateways dinâmicos no modelo clássico de implantação.

### <a name="what-is-expressroute"></a>O que é a rota expressa?

Rota expressa permite que você crie conexões privadas entre data centers Microsoft e infraestrutura que está em seu local ou em um ambiente de localização conjunta. Com a rota expressa, você pode estabelecer conexões com serviços de nuvem do Microsoft como o Microsoft Azure e o Office 365 em uma instalação de localização conjunta de parceiro rota expressa ou conectar-se diretamente da sua rede WAN existente (como um MPLS VPN fornecida por um provedor de serviços de rede).

Rota expressa conexões oferecem maior segurança, mais confiabilidade, maior largura de banda e latências mais conexões típicas pela Internet. Em alguns casos, usando conexões de rota expressa para transferir dados entre sua rede local e o Azure pode gerar também benefícios de custo significativas. Se você já tiver criado uma conexão entre locais da sua rede local para o Azure, você pode migrar para uma conexão de rota expressa enquanto mantém sua rede virtual intactos.

Consulte as [Perguntas Frequentes de rota expressa](../expressroute/expressroute-faqs.md) para obter mais detalhes.

## <a name="site-to-site-connections-and-vpn-devices"></a>Conexões de site para Site e dispositivos VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>O que devo considerar ao selecionar um dispositivo VPN?

Podemos ter validados um conjunto de dispositivos VPN to-Site padrão em parceria com fornecedores de dispositivo. Uma lista de dispositivos VPN compatíveis conhecidos, seus correspondentes instruções de configuração ou amostras e especificações do dispositivo pode ser encontrada [aqui](vpn-gateway-about-vpn-devices.md). Todos os dispositivos nas famílias dispositivo listados como compatível conhecido devem trabalhar com uma rede Virtual. Para ajudar a configurar seu dispositivo VPN, consulte a amostra de configuração de dispositivo ou o link que corresponde à família de dispositivo apropriado.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>O que fazer se eu tiver um dispositivo VPN que não está na lista de dispositivos compatíveis conhecidos?

Se você não vir seu dispositivo listado como um dispositivo VPN compatível conhecido e deseja usá-lo em sua conexão de VPN, você precisará verificar se ele atende a compatíveis IKE/IPsec configuração Opções e parâmetros listados [aqui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Dispositivos os requisitos mínimos de reunião devem funcionar bem com gateways VPN. Contate o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Por que meu túnel VPN baseado em políticas vai para baixo quando o tráfego está ocioso?

Este é o comportamento esperado baseada em políticas (também conhecido como roteamento estático) gateways VPN. Quando o tráfego através do túnel está ocioso por mais de 5 minutos, o túnel será interrompido. Quando o tráfego começa fluindo em qualquer direção, o túnel será restabelecido imediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Pode usar o software VPNs para se conectar ao Azure?

Oferecemos suporte roteamento do Windows Server 2012 e os servidores de acesso remoto (RRAS) para configuração de cruz local-to-Site.

Outras soluções VPN software devem trabalhar com nosso gateway, desde que eles estão em conformidade com implementações de IPsec padrão do setor. Contate o fornecedor do software para obter instruções de configuração e suporte.

## <a name="point-to-site-connections"></a>Conexões ponto-a-Site

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Quais sistemas operacionais pode usar com o ponto-a-Site?

Há suporte para os seguintes sistemas operacionais:

- Windows 7 (32 bits e 64 bits)

- Windows Server 2008 R2 (somente 64 bits)

- Windows 8 (32 bits e 64 bits)

- Windows 8.1 (32 bits e 64 bits)

- Windows Server 2012 (somente 64 bits)

- Windows Server 2012 R2 (somente 64 bits)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Pode usar qualquer cliente VPN de software para ponto-a-Site que ofereça suporte SSTP?

Não. Suporte é limitado somente para as versões de sistema operacional Windows listadas acima.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN pode ter em minha configuração de ponto-a-Site?

Oferecemos suporte para até 128 clientes VPN para poder se conectar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Pode usar meu próprio CA de raiz PKI interna para conectividade de ponto-a-Site?

Sim. Anteriormente, somente os certificados raiz autoassinados podem ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso desviar proxies e firewalls usando o recurso de ponto-a-Site?

Sim. Usamos SSTP (protocolo SSTP) para túnel através de firewalls. Este túnel aparecerá como uma conexão HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto-a-Site, será a VPN reconectar automaticamente?

Por padrão, o computador cliente será não restabelecer a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Suporte de ponto-a-Site reconectar automaticamente e DDNS nos clientes VPN?

Reconectar automaticamente e DDNS atualmente não são suportados no VPNs ponto-a-Site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter to-Site e configurações de ponto-a-Site coexistam para a mesma rede virtual?

Sim. Ambas as essas soluções funcionará se você tiver um tipo de RouteBased VPN para seu gateway. Para o modelo clássico de implantação, você precisa de um gateway dinâmico. Fazemos não ponto-a-Site de suporte para gateways VPN de roteamento estáticos ou gateways usando - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Pode configurar um cliente de ponto-a-Site para se conectar a várias redes virtuais ao mesmo tempo?

Sim, é possível. Mas as redes virtuais não sobrepuseram prefixos IP e os espaços de endereço do ponto-a-Site não devem ser sobrepostos entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanto produtividade pode esperar por meio de conexões-to-Site ou ponto-a-Site?

É difícil manter a produtividade exata nos encapsulamentos VPN. IPsec e SSTP são protocolos VPN Cripto pesado. Produtividade também é limitada pela latência e largura de banda entre seu local e a Internet.

## <a name="gateways"></a>Gateways

### <a name="what-is-a-policy-based-static-routing-gateway"></a>O que é um gateway (roteamento estático) baseado em políticas?

Gateways baseados em política implementam VPNs baseados em política. VPNs com base na política direcionam pacotes através de encapsulamentos de segurança IP com base em combinações de prefixos de endereço entre sua rede local e o VNet do Azure e criptografar. A política (ou seletor de tráfego) geralmente é definido como uma lista de acesso na configuração VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>O que é um gateway (roteamento dinâmico) baseado em rota?

Gateways baseados em rota implementam os VPNs baseados em rota. Baseada em rota VPNs usam "rotas" no IP encaminhamento ou tabela de roteamento para pacotes diretos em suas interfaces de túnel correspondente. Em seguida, as interfaces de túnel criptografar ou descriptografar os pacotes e sair os encapsulamentos. No seletor de política ou tráfego de VPNs rota com base estão definidas como para qualquer (ou curingas).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>É possível obter meu endereço IP de gateway VPN antes de eu criá-lo?

Não. Você precisa criar seu gateway primeiro para obter o endereço IP. Alterações no endereço IP se você excluir e recriar o gateway VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Como meu túnel VPN obter autenticado?

VPN Azure usa autenticação PSK (chave pré-compartilhada). Podemos gerar uma chave pré-compartilhada (PSK) durante a criação do túnel VPN. Você pode alterar a PSK gerado automaticamente para o seu próprio com o cmdlet do PowerShell de chave pré-compartilhada definir ou API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Posso usar a API de chave pré-compartilhada definida para configurar meu baseada em políticas (roteamento estático) gateway VPN?

Sim, o cmdlet definir API de chave pré-compartilhada e PowerShell pode ser usado para configurar Azure VPNs (estático) baseado em políticas e baseado em rota VPNs de roteamento (dinâmicos).

### <a name="can-i-use-other-authentication-options"></a>Posso usar outras opções de autenticação?

Nós são limitados ao uso de chaves pré-compartilhadas (PSK) para autenticação.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Qual é a "sub-rede de gateway" e por que ele é necessário?

Temos um serviço de gateway que podemos executar para habilitar a conectividade entre locais. 

Você precisará criar uma sub-rede de gateway para sua VNet configurar um gateway VPN. Todas as sub-redes de gateway devem ser nomeadas GatewaySubnet funcione corretamente. Não nome sua sub-rede gateway algo mais. E não implantar VMs ou qualquer outra coisa à sub-rede gateway.

O tamanho mínimo de sub-rede gateway depende totalmente a configuração que você deseja criar. Embora seja possível criar uma sub-rede de gateway o menor /29 algumas configurações, recomendamos que você crie uma sub-rede de gateway de /28 ou maior (/ 28, /27, /26 etc.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Pode implantar máquinas virtuais ou instâncias de função à minha sub-rede de gateway?

Não.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Como especificar qual o tráfego passa pelo gateway VPN?

Se você estiver usando o Portal de clássico do Azure, adicione cada intervalo que você deseja que sejam enviado por meio do gateway para sua rede virtual na página redes em redes locais.

### <a name="can-i-configure-forced-tunneling"></a>Pode configurar forçado túnel?

Sim. Consulte [Configurar forçado túnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Posso configurar meu próprio servidor VPN no Azure e usá-lo para se conectar à minha rede local?

Sim, você pode implantar sua própria gateways VPN ou servidores no Azure a partir do Azure Marketplace ou criar seus próprios roteadores VPN. Você precisará configurar rotas definidas pelo usuário em sua rede virtual para garantir que o tráfego é roteado corretamente entre suas redes locais e sub-redes sua rede virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Por que determinadas portas são abertas no meu gateway VPN?

Eles são necessários para comunicação de infraestrutura Azure. Eles são protegidos (bloqueado) por certificados Azure. Sem certificados adequados, entidades externas, incluindo os clientes desses gateways, não poderá causar nenhum efeito sobre esses pontos de extremidade.

Um gateway VPN bem é um dispositivo com diversas bases com um toque de NIC para a rede privada do cliente e uma NIC opostas a rede pública. Entidades de infraestrutura Azure não toque redes privadas do cliente por razões de conformidade, para que eles precisam utilizar pontos de extremidade públicos para comunicação de infraestrutura. Os pontos de extremidade públicos periodicamente são verificados por auditoria de segurança do Azure.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Obter mais informações sobre tipos de gateway, requisitos e produtividade

Para obter mais informações, consulte [Sobre configurações de Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Conectividade de VNet para VNet e em vários locais

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Que tipo de gateways pode oferecer suporte a vários locais e conectividade de VNet para VNet?

Somente baseado em rota VPNs (roteamento dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso conectar um VNet com um tipo de VPN RouteBased para outra VNet com um tipo de PolicyBased VPN?

Não, ambas as redes virtuais devem estar usando baseada em rota (roteamento dinâmico) VPNs.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>O tráfego de VNet para VNet é seguro?

Sim, ele é protegido por criptografia IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>O tráfego de VNet para VNet passar o backbone Azure?

Sim.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Quantos sites de local e redes virtuais posso uma rede virtual conectar?

Máx. 10 combinado para os gateways Basic e roteamento dinâmico padrão; 30 para os gateways de alto desempenho VPN.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>É possível usar ponto-a-Site VPNs com minha rede virtual com vários túneis VPN?

Sim, VPNs ponto-a-Site (P2S) podem ser usadas com os gateways VPN conexão com vários sites locais e outras redes virtuais.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Pode configurar vários encapsulamentos entre minha rede virtual e meu site local usando a VPN de vários local?

Não, não há suporte encapsulamentos redundantes entre uma rede virtual Azure e um site local.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Existe a podem ser sobrepostos espaços de endereço entre as redes virtuais conectadas e sites locais local?

Não. Espaços de endereço sobrepostos fará com que o carregamento de arquivo de configuração de rede ou "Criando a rede Virtual" falha.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Posso obter mais largura de banda com mais VPNs to-Site que para uma única rede virtual?

Não, todos os encapsulamentos VPN, incluindo VPNs de ponto-a-Site, compartilham o mesmo gateway VPN do Azure e a largura de banda disponível.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Pode usar o gateway VPN Azure tráfego de trânsito entre meus sites de local ou a outra rede virtual?

**Modelo de implantação clássico**<br>
Tráfego de trânsito via gateway Azure VPN é possível usar o modelo clássico de implantação, mas depende de espaços de endereço estática definido no arquivo de configuração de rede. BGP ainda não é suportado com redes virtuais do Azure e gateways VPN usando o modelo clássico de implantação. Sem BGP, definir manualmente os espaços de endereço de trânsito é muito e propensa a erros, não é recomendada.<br>
**Modelo de implantação do Gerenciador de recursos**<br>
Se você estiver usando o modelo de implantação do Gerenciador de recursos, consulte a seção [BGP](#bgp) para obter mais informações.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure gerar a mesma chave previamente compartilhada IPsec/IKE para todas as minhas conexões VPN para a mesma rede virtual?

Não, o Azure por padrão gera chaves pré compartilhadas diferentes para diferentes conexões VPN. No entanto, você pode usar o cmdlet definir API de REST chave de Gateway de VPN ou PowerShell para definir o valor de chave que você preferir. A chave deve ser uma cadeia de caracteres alfanuméricos de comprimento entre 1 e 128 caracteres.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure cobra para tráfego entre redes virtuais?

Para o tráfego entre diferentes redes virtuais Azure, Azure encargos somente para tráfego percorrer de uma região Azure para outro. A taxa de custo é listada na página do Azure [Preços de Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Posso me conectar uma rede virtual com VPNs IPsec meu circuito rota expressa?

Sim, isso é suportado. Para obter mais informações, consulte [rota expressa configurar-to-Site conexões e VPN que coexistam](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Conectividade entre locais e VMs

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se minha máquina virtual está em uma rede virtual e tiver uma conexão entre locais, como devo me conectar a máquina virtual?

Você tem algumas opções. Se você tiver RDP habilitado e você tiver criado um ponto de extremidade, você pode se conectar à sua máquina virtual usando o VIP. Nesse caso, especifique o VIP e a porta que você deseja se conectar. Você precisará configurar a porta na sua máquina virtual para o tráfego. Normalmente, você faria acesse o Portal de clássico do Azure e salvar as configurações para a conexão RDP com seu computador. As configurações contêm as informações de conexão necessárias.

Se você tiver uma rede virtual com conectividade entre local configurada, você pode se conectar à sua máquina virtual usando o DIP interno ou o endereço IP particular. Você pode também se conectar à sua máquina virtual por DIP interno outra máquina virtual que está localizada na mesma rede virtual. Você não consegue RDP para sua máquina virtual usando o DIP, se você estiver se conectando de um local fora da sua rede virtual. Por exemplo, se você tiver uma rede virtual ponto-a-Site configurada e você não estabelecer uma conexão do seu computador, você não pode se conectar à máquina virtual por DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se minha máquina virtual estiver em uma rede virtual com conectividade entre locais, todo o tráfego do meu máquina virtual percorrer essa conexão?

Não. Somente o tráfego que tem um destino IP contida nos intervalos de endereços IP de rede Local de rede virtual que você especificou irão através do gateway de rede virtual. Tráfego tem um destino IP localizada dentro da rede virtual permaneça dentro da rede virtual. Outro tráfego enviado por meio de Balanceador de carga para redes públicas ou se túnel forçada é usado, enviado através do gateway VPN do Azure. Se você estiver solucionando problemas, é importante garantir que todos os intervalos listados na sua rede Local que você deseja enviar por meio do gateway. Verifique se que os intervalos de endereços de rede Local não se sobreponham com qualquer um dos intervalos de endereços da rede virtual. Além disso, você deseja verificar se o servidor DNS que você está usando está resolvendo o nome para o endereço IP correto.


## <a name="virtual-network-faq"></a>Perguntas Frequentes de rede virtual

Você pode exibir informações adicionais de rede virtual as [Perguntas Frequentes de rede Virtual](../virtual-network/virtual-networks-faq.md).
 
