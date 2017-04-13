<properties
    pageTitle="Diretrizes de infraestrutura de rede | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para implantar rede virtual nos serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>Diretrizes de infraestrutura de rede

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo aborda Noções básicas sobre as etapas necessárias planejamento de rede virtual dentro do Azure e conectividade entre ambientes de local existente.


## <a name="implementation-guidelines-for-virtual-networks"></a>Diretrizes de implementação para redes virtuais

Decisões:

- Que tipo de rede virtual você precisar hospedar sua carga de trabalho TI ou infraestrutura (somente na nuvem ou locais cruzado)?
- Para redes virtuais entre locais, quanto espaço de endereço você precisa hospedar as sub-redes e VMs agora e razoável expansão no futuro?
- Você vai criar redes virtuais centralizadas ou criar redes virtuais individuais para cada grupo de recursos?

Tarefas:

- Defina o espaço de endereço para as redes virtuais a ser criado.
- Defina o conjunto de sub-redes e o espaço de endereço para cada um.
- Para redes virtuais entre locais, defina o conjunto de espaços de endereço de rede local para os locais no local que as VMs da rede virtual precisam contatar.
- Trabalho com locais equipe de rede para garantir o roteamento apropriado é configurada quando criar entre locais redes virtuais.
- Crie rede virtual usando a convenção de nomenclatura.


## <a name="virtual-networks"></a>Redes virtuais

Redes virtuais são necessárias para oferecer suporte a comunicações entre máquinas virtuais (VMs). Você pode definir sub-redes, endereço IP personalizado, configurações de DNS, filtragem de segurança e balanceamento de carga, como em redes físicas. Usando um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [circuito de via expressa](../expressroute/expressroute-introduction.md), você pode conectar redes virtuais Azure às suas redes locais. Você pode ler mais sobre [redes virtuais e seus componentes](../virtual-network/virtual-networks-overview.md).

Usando grupos de recursos, você tem flexibilidade em como você projeta componentes de sua rede virtuais. VMs podem conectar ao redes virtuais fora do seu próprio grupo de recursos. Uma abordagem de design comum seria criar grupos de recursos centralizado que contêm sua infraestrutura de rede de núcleo que pode ser gerenciada por uma equipe comuns e, em seguida, VMs e seus aplicativos implantados em grupos de recursos separados. Essa abordagem permite o acesso de proprietários de aplicativo ao grupo de recursos que contém suas VMs sem abrir o acesso à configuração dos recursos rede virtuais mais amplo.

## <a name="site-connectivity"></a>Conectividade de site

### <a name="cloud-only-virtual-networks"></a>Redes virtuais somente na nuvem
Se computadores e usuários locais não exigem contínuos de conectividade em VMs em uma rede virtual Azure, o design de rede virtual é direta:

![Somente na nuvem virtual diagrama básico de rede](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Essa abordagem é geralmente para cargas de trabalho com a Internet, como um servidor web baseados na Internet. Você pode gerenciar essas VMs usando RDP ou conexões VPN de ponto-a-site.

Porque eles não se conectar à sua rede local, somente Azure redes virtuais pode utilizar qualquer parte do espaço de endereço IP particular, mesmo se o mesmo espaço particular estiver em usar o local.


### <a name="cross-premises-virtual-networks"></a>Redes virtuais entre locais
Se os computadores e usuários locais exigem contínuos de conectividade em VMs em uma rede virtual Azure, crie uma rede virtual entre locais.  Conecte-o à sua rede local com uma rota expressa ou conexão de VPN-to-site.

![Diagrama de rede virtual entre locais](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

Nessa configuração, a rede virtual Azure é essencialmente um extensão baseado em nuvem da sua rede local.

Porque eles se conectar à sua rede local, entre locais redes virtuais devem usar uma parte do espaço de endereço usado por sua organização exclusiva. Da mesma forma que diferentes locais corporativos são atribuídos a uma sub-rede IP específica, o Azure fica outro local, como estender sua rede.

Para permitir que pacotes de viagem da sua rede virtual entre local à sua rede local, você deve configurar o conjunto de prefixos de endereço do local relevante como parte da definição da rede local para a rede virtual. Dependendo do espaço de endereço da rede virtual e o conjunto de locais relevantes no local, pode haver vários prefixos de endereço na rede local.

Você pode converter uma rede virtual somente na nuvem para uma rede virtual entre locais, mas provavelmente requer que você a re-IP seu espaço de endereço de rede virtual e recursos Azure. Portanto, considere cuidadosamente se uma rede virtual precisa estar conectado à sua rede local quando você atribui uma sub-rede IP.

## <a name="subnets"></a>Sub-redes
Sub-redes permitem organizar recursos relacionados, logicamente (por exemplo, uma sub-rede para VMs associado ao mesmo aplicativo), ou física (por exemplo, uma sub-rede por grupo de recursos). Você também pode usar técnicas de isolamento de sub-rede para segurança adicional.

Para redes virtuais entre locais, você deve criar sub-redes com as mesmas convenções que você usa para recursos locais. **Azure sempre usa os três primeiros endereços IP do espaço de endereço para cada sub-rede**. Para determinar o número de endereços necessários para a sub-rede, comece por contagem do número de VMs que você precisa agora. Estimar crescimento futuro e use a tabela a seguir para determinar o tamanho da sub-rede.

Número de VMs necessária | Número de bits de host necessário | Tamanho da sub-rede
--- | --- | ---
1 – 3 | 3 | / 29
4 – 11     | 4 | / 28
12 – 27 | 5 | / 27
28 – 59 | 6 | / 26
60 – 123 | 7 | / 25

> [AZURE.NOTE] Para sub-redes normal no local, o número máximo de endereços de host para uma sub-rede com bits de host n é 2<sup>n</sup> – 2. Para uma sub-rede Azure, o número máximo de endereços de host para uma sub-rede com bits de host n é 2<sup>n</sup> – 5 (2 mais 3 para os endereços que usa o Azure em cada sub-rede).

Se você escolher um tamanho de sub-rede que é muito pequeno, você precisa re-IP e reimplanta VMs na sub-rede.


## <a name="network-security-groups"></a>Grupos de segurança de rede
Você pode aplicar regras de filtragem para o tráfego que flui através de suas redes virtuais usando grupos de segurança de rede. Você pode criar regras de filtragem granulares para proteger o seu ambiente de rede virtual, controle de entrada e saídos tráfego, origem e destino intervalos de IP, permitido portas, etc. Grupos de segurança de rede podem ser aplicados a sub-redes dentro de uma rede virtual ou diretamente em uma interface de rede virtual fornecido. É recomendável ter algum nível de grupo de segurança de rede filtrando o tráfego nas redes virtuais. Você pode ler mais sobre [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).


## <a name="additional-network-components"></a>Componentes de rede adicionais
Como com uma infraestrutura de rede física do local, Azure rede virtual pode conter mais de sub-redes e endereçamento IP. Ao projetar sua infraestrutura de aplicativo, talvez você queira incorporar alguns desses componentes adicionais:

- [Gateways VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) - conecte Azure redes virtuais a outras redes virtuais Azure, ou se conectar a redes de local por meio de uma conexão de VPN to-Site. Implementar via expressa conexões dedicado e seguro. Você também pode fornecer acesso direto aos usuários com conexões VPN de ponto-a-Site.
- [Balanceador de carga](../load-balancer/load-balancer-overview.md) - fornece balanceamento de carga de tráfego para o tráfego interno e externo conforme desejado.
- [Gateway do aplicativo](../application-gateway/application-gateway-introduction.md) - balanceador de carga de HTTP balanceamento de carga na camada de aplicativo, fornecendo alguns benefícios adicionais para aplicativos da web em vez de implantar o Azure.
- [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md) - distribuição de tráfego baseado em DNS para direcionar os usuários finais para o ponto de extremidade do aplicativo disponível mais próximo, permitindo que você hospedar seu aplicativo fora dos data centers do Azure em diferentes regiões.


## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 