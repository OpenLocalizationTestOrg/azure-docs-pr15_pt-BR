
<properties
   pageTitle="Correspondência de rede virtual Azure | Microsoft Azure"
   description="Saiba mais sobre VNet correspondência no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>Correspondência de VNet

Correspondência de VNet é um mecanismo que conecta duas redes virtuais (VNets) na mesma região através da rede de backbone Azure. Depois de peered, as duas redes virtuais aparecem como um para todos os fins de conectividade. Eles ainda são gerenciados como recursos separados, mas máquinas virtuais nestas redes virtuais possam se comunicar com os outros diretamente usando endereços IP particulares.

O tráfego entre máquinas virtuais em redes virtuais peered é roteado por meio de infraestrutura do Azure, assim como o tráfego é roteado entre VMs na mesma rede virtual. Alguns dos benefícios do uso de correspondência VNet incluem:

- Uma conexão de baixa latência, largura de banda alta entre recursos em redes virtuais diferentes.
- A capacidade de usar recursos como dispositivos de rede e gateways VPN como pontos de trânsito em um VNet peered.
- A capacidade de se conectar a uma rede virtual que usa o modelo do Gerenciador de recursos do Azure a uma rede virtual que usa o modelo clássico de implantação e habilitar o total conectividade entre recursos nestas redes virtuais.

Requisitos e principais aspectos de correspondência VNet:

- As duas redes virtuais que são peered devem estar na mesma região Azure.
- As redes virtuais que são peered devem ter não sobrepostas espaços de endereço IP.
- Correspondência de VNet é entre duas redes virtuais e há nenhuma relação transitiva derivada. Por exemplo, se A uma rede virtual é peered com uma rede virtual B e se rede virtual B é peered com uma rede virtual C, não traduzir para virtual rede uma sendo peered com uma rede virtual C.
- Correspondência pode ser estabelecida entre redes virtuais nas duas assinaturas diferentes, como longo um usuário privilegiado de ambas as assinaturas autoriza a correspondência e as assinaturas são associadas ao mesmo locatário do Active Directory. 
- Correspondência entre o modelo clássico de implantação e de rede virtual no modelo de Gerenciador de recurso exige que o VNets deve ser na mesma assinatura.
- Uma rede virtual que usa o modelo de implantação do Gerenciador de recursos pode ser peered com outra rede virtual que usa esse modelo ou com uma rede virtual que usa o modelo de implantação clássico. No entanto, redes virtuais que usam o modelo clássico de implantação não podem ser peered uns aos outros.
- Embora a comunicação entre máquinas virtuais em redes virtuais peered tenha sem restrições de largura de banda adicional, com base no tamanho da máquina virtual de ponta de largura de banda ainda se aplica.


![Correspondência de VNet básica](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividade
Depois de duas redes virtuais são peered, uma máquina virtual (função web/trabalho) na rede virtual diretamente pode se conectar com outras máquinas virtuais na rede virtual peered. Essas duas redes tem completo de nível de IP conectividade.

A latência de rede para uma viagem entre duas máquinas virtuais em redes virtuais peered é o mesmo para uma viagem em uma rede virtual local. A taxa de transferência de rede se baseia a largura de banda que é permitida para a máquina virtual proporcional ao seu tamanho. Não há nenhuma restrição adicional na largura de banda.

O tráfego entre máquinas virtuais peered redes virtuais é roteado diretamente por meio de infraestrutura de back-end do Azure e não por meio de um gateway.

Máquinas virtuais em uma rede virtual pode acessar os internos balanceamento de carga (ILB) pontos de extremidade da rede virtual peered. Grupos de segurança de rede (NSGs) podem ser aplicados em qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes se desejado.

Quando os usuários configuram correspondência, eles podem abrir ou fechar as regras NSG entre as redes virtuais. Se o usuário optar por abrir total conectividade entre redes virtuais peered (que é a opção padrão), eles podem usar NSGs em sub-redes específicas ou máquinas virtuais para bloquear ou negar acesso específico.

Fornecido Azure interna resolução de nomes DNS máquinas virtuais não funciona em redes peered virtual. Máquinas virtuais têm nomes DNS internos que estão resolvidos apenas dentro da rede virtual local. No entanto, os usuários podem configurar máquinas virtuais que estejam em execução em redes virtuais peered como servidores DNS de uma rede virtual.

## <a name="service-chaining"></a>Serviço encadeamento
Os usuários podem configurar tabelas de rota definidas pelo usuário que apontam para máquinas virtuais em redes virtuais peered como o "salto seguinte" endereço IP, conforme mostrado no diagrama posteriormente neste artigo. Isso permite aos usuários obter serviço encadeamento, através do qual elas podem direcionar o tráfego de uma rede virtual em um dispositivo virtual que é executado em uma rede virtual peered por meio de tabelas de rota definidos pelo usuário.

Os usuários também efetivamente podem criar ambientes de tipo de hub e spoke onde o hub pode hospedar componentes de infraestrutura como um dispositivo virtual de rede. Todas as redes de virtual com raios, em seguida, podem correspondente com ele, bem como um subconjunto de tráfego para dispositivos que executam na rede virtual hub. Em resumo, VNet correspondência habilita o próximo endereço IP na 'definidas pelo usuário tabela rota' para ser o endereço IP de uma máquina virtual na rede virtual peered.

## <a name="gateways-and-on-premises-connectivity"></a>Conectividade de gateways e local
Cada rede virtual, independentemente se ele está peered com outra rede virtual, ainda pode ter seu próprio gateway e usá-lo para se conectar ao local. Os usuários também podem configurar [conexões de VNet para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) usando gateways, embora as redes virtuais são peered.

Quando as duas opções para interconectividade de rede virtual são configuradas, o tráfego entre as redes virtuais flui através da configuração de correspondência (ou seja, por meio do backbone Azure).

Quando redes virtuais são peered, os usuários também podem configurar o gateway na rede virtual peered como um ponto de trânsito para o local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual pode ter apenas um gateway. Ele pode ser um gateway local ou um gateway remoto (em peered rede virtual), conforme mostrado na figura a seguir.

Não há suporte para o trânsito de gateway em relação entre redes virtuais usando o modelo do Gerenciador de recursos e aquelas que usam o modelo de implantação clássico. Ambas as redes virtuais em relação aos precisam usar o modelo de implantação do Gerenciador de recursos de trânsito um gateway para trabalhar.

Quando as redes virtuais que estão compartilhando uma única conexão de rota expressa do Azure são peered, o tráfego entre eles percorre a relação aos (ou seja, através da rede de backbone Azure). Usuários ainda podem usar gateways locais em cada rede virtual para se conectar ao circuito local. Como alternativa, podem usar um gateway compartilhado e configurar trânsito para conectividade de local.

![VNet aos trânsito](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Provisionamento
Correspondência de VNet é uma operação privilegiada. É uma função separada sob o namespace VirtualNetworks. Um usuário pode ser fornecido direitos específicos para autorizar a correspondência. Um usuário que tenha acesso de leitura e gravação à rede virtual herda esses direitos automaticamente.

Um usuário que seja um administrador ou um usuário privilegiado da capacidade aos pode iniciar uma operação de correspondência em outro VNet. Se houver uma solicitação correspondente para correspondência no outro lado, e outros requisitos forem atendidos, a correspondência será estabelecida.

Consulte os artigos na seção "Próximos passos" para saber mais sobre como estabelecer VNet correspondência entre duas redes virtuais.

## <a name="limits"></a>Limites
Há limites no número de peerings permitidos para uma única rede virtual. Para obter mais informações, consulte [limites de rede Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Preços
Correspondência de VNet será gratuitamente durante o período de revisão. Depois que ele é lançado, haverá um custo nominal no tráfego de entrada e saída que utiliza a correspondência. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Próximas etapas
- [Configurar a correspondência entre redes virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre o [encaminhamento de IP e rotas definidas pelo usuário](virtual-networks-udr-overview.md).
