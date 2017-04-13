<properties
   pageTitle="Visão geral de rede Virtual Azure (VNet)"
   description="Saiba mais sobre redes virtuais (VNets) no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Visão geral de rede virtual

Uma rede virtual Azure (VNet) é uma representação de sua própria rede na nuvem.  É um isolamento lógico da nuvem Azure dedicado à sua assinatura. Você pode controlar totalmente os blocos de endereço IP, as configurações DNS, políticas de segurança e tabelas de rota dentro dessa rede. Você pode também ainda mais sua VNet de segmento em sub-redes e iniciar Azure IaaS VMs (máquinas virtuais) e/ou [Serviços de nuvem (instâncias de função de PaaS)](../cloud-services/cloud-services-choose-me.md). Além disso, você pode se conectar a rede virtual à sua rede local usando uma das [Opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponível no Azure. Essencialmente, você pode expandir sua rede para o Azure, com controle total em blocos de endereço IP com o benefício de escala empresarial que Azure fornece.

Para entender melhor VNets, dê uma olhada na figura abaixo, que mostra uma rede local simplificadas.

![Rede local](./media/virtual-networks-overview/figure01.png)

A figura acima mostra uma rede local conectada à Internet pública através de um roteador. Você também pode ver um firewall entre o roteador e DMZ hospedar um servidor DNS e um farm de servidores web. O farm de servidores web é carga equilibrada usando um balanceador de carga de hardware que é exposto à Internet e consome recursos da sub-rede interno. A sub-rede interna é separada da DMZ por outro firewall e os servidores de controlador de domínio do Active Directory de hosts, servidores de banco de dados e servidores de aplicativos.

Mesma rede pode ser hospedada no Azure, conforme mostrado na figura abaixo.

![Rede virtual Azure](./media/virtual-networks-overview/figure02.png)

Observe como infraestrutura do Azure assume a função do roteador, permitindo o acesso à Internet pública sem a necessidade de qualquer configuração de seu VNet. Firewalls podem ser substituídos por grupos de segurança de rede (NSGs) aplicado a cada sub-rede. E balanceadores de carga físicos são substituídos por balanceadores de carga opostas e internos de internet no Azure.

>[AZURE.NOTE] Há dois modos de implantação no Azure: clássico (também conhecido como gerenciamento de serviço) e Azure Resource Manager (ARM). VNets clássico pode ser adicionada a um grupo de afinidade ou criado como um VNet regional. Se você tiver um VNet em um grupo de afinidade, é recomendável migrar [para um VNet regional](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Benefícios de rede virtual

- **Isolamento**. VNets são completamente isolados uns dos outros. Que permite que você crie redes não contíguas para desenvolvimento, teste e produção que usam os mesmos blocos de endereço CIDR.

- **Acesso à Internet pública**. Todas as VMs IaaS e PaaS instâncias de função em uma VNet podem acessar a Internet pública por padrão. Você pode controlar o acesso, usando grupos de segurança de rede (NSGs).

- **Acesso a VMs dentro do VNet**. Instâncias de função de PaaS e IaaS VMs podem ser iniciadas na mesma rede virtual e eles podem se conectar a outro usando endereços IP particulares, mesmo se eles estiverem em sub-redes diferentes sem a necessidade de configurar um gateway ou use endereços IP públicos.

- **Resolução de nomes**. Azure fornece resolução de nome interno para IaaS VMs e instâncias de função de PaaS implantadas em seu VNet. Você também pode implantar seus próprios servidores DNS e configurar o VNet para usá-los.

- **Segurança**. Entrar e sair as máquinas virtuais e PaaS instâncias de função em uma VNet o tráfego pode ser controlado usando grupos de segurança de rede.

- **Conectividade**. VNets podem ser conectados uns aos outros usando os gateways de rede ou VNet correspondência. VNets pode ser conectado centros de dados locais por meio de redes VPN-to-site ou rota expressa do Azure. Para saber mais sobre a conectividade VPN-to-site, visite [gateways sobre VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Para saber mais sobre a rota expressa, visite [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md). Para saber mais sobre VNet correspondência, visite [VNet correspondência](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Verifique se que você criar um VNet antes de implantar qualquer IaaS VMs ou instâncias de função de PaaS seu ambiente do Azure. Com base em ARM VMs exigem um VNet, e se você não especificar um VNet existente, Azure cria um padrão VNet que pode ter um conflito de bloco de endereço CIDR à sua rede local. Tornando impossível conectar seu VNet à sua rede local.

## <a name="subnets"></a>Sub-redes

Sub-rede é um intervalo de endereços IP na VNet, você pode dividir um VNet em várias sub-redes para organização e segurança. VMs e instâncias de função de PaaS implantadas em sub-redes (iguais ou diferentes) dentro de uma VNet podem se comunicar com os outros sem qualquer configuração extra. Você também pode configurar tabelas de rotas e NSGs a uma sub-rede.

## <a name="ip-addresses"></a>Endereços IP


Há dois tipos de endereços IP atribuídos aos recursos do Azure: *pública* e *privada*. Endereços IP públicos permitir Azure recursos para se comunicar com a Internet e outros serviços de público Azure como [Azure relacionada Cache](https://azure.microsoft.com/services/cache/), [Hubs de evento do Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Endereços IP privados permite a comunicação entre recursos em uma rede virtual, juntamente com os conectados através de uma VPN, sem usar um IP Internet roteáveis endereços.

Para saber mais sobre endereços IP no Azure, visite [endereços IP na rede virtual](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Balanceadores de carga Azure

Serviços de nuvem em uma rede Virtual e máquinas virtuais podem ser expostos à Internet usando balanceadores de carga do Azure. Linha de aplicativos de negócios que estão interna só pode ser carga equilibrada usando balanceador de carga interno.

- **Balanceador externo**. Você pode usar um balanceador externo para fornecer alta disponibilidade para instâncias de função IaaS VMs e PaaS acessadas pela Internet pública.

- **Balanceador de carga de interno**. Você pode usar um balanceador de carga interno para fornecer alta disponibilidade IaaS VMs e instâncias de função de PaaS acessadas a partir de outros serviços no seu VNet.

Para saber mais sobre balanceamento de carga no Azure, visite a [Visão geral de Balanceador de carga](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Grupo de segurança de rede (NSG)

Você pode criar NSGs para controlar o acesso de entrada e saído para interfaces de rede (NICs), VMs e sub-redes. Cada NSG contém uma ou mais regras especificando estando ou não o tráfego é aprovado ou negado com base no endereço IP de origem, porta de origem, endereço IP de destino e porta de destino. Para saber mais sobre NSGs, visite [o que é um grupo de segurança de rede](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Dispositivos virtuais

Um dispositivo virtual é apenas outra máquina virtual no seu VNet que executa uma função de dispositivo baseada em software, como firewall, otimização de WAN ou detecção de intrusos. Você pode criar uma rota no Azure para rotear o tráfego de VNet por meio de um dispositivo virtual para usar seus recursos.

Por exemplo, NSGs podem ser usados para fornecer segurança em seu VNet. No entanto, NSGs fornecem camada 4 lista de controle de acesso (ACL) para os pacotes de entrada e de saída. Se você quiser usar um modelo de segurança 7 de camada, você precisa usar um dispositivo de firewall.

Dispositivos virtuais dependem de [rotas e encaminhamento IP definidas pelo usuário](virtual-networks-udr-overview.md).

## <a name="limits"></a>Limites
Há limites no número de redes virtuais permitido em uma assinatura, consulte [limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## <a name="pricing"></a>Preços
Não há custo adicional para usar redes virtuais do Azure. As instâncias de computação iniciadas dentro do Vnet serão cobradas as taxas padrão conforme descrito em [Preços de máquina virtual do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/). Os [Gateways VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e [endereços IP públicos] (https://azure.microsoft.com/pricing/details/ip-addresses/) usado na VNet também será cobradas taxas padrão.

## <a name="next-steps"></a>Próximas etapas

- [Criar um VNet](virtual-networks-create-vnet-arm-pportal.md) e sub-redes.
- [Criar uma máquina virtual em um VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre [rotas e encaminhamento IP definidas pelo usuário](virtual-networks-udr-overview.md).
