<properties
   pageTitle="Pública e privada no Gerenciador de recursos do Azure de endereçamento IP | Microsoft Azure"
   description="Saiba mais sobre pública e privada no Gerenciador de recursos do Azure de endereçamento IP"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Endereços IP no Azure
Você pode atribuir endereços IP aos recursos Azure para se comunicar com outros recursos Azure, sua rede local e a Internet. Há dois tipos de endereços IP, que você pode usar no Azure:

- **Endereços IP públicos**: usado para comunicação com a Internet, incluindo os serviços do Azure público
- **Endereços IP privados**: usado para comunicação dentro de uma rede virtual Azure (VNet) e seu local de rede quando você usa um gateway VPN ou circuito de rota expressa para estender sua rede para o Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](virtual-network-ip-addresses-overview-classic.md).

Se você estiver familiarizado com o modelo clássico de implantação, verifique o [diferenças em endereçamento entre clássico IP e Gerenciador de recursos](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitem Azure recursos para se comunicar com serviços de Internet e Azure público como [Azure relacionada Cache](https://azure.microsoft.com/services/cache/), [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/), [bancos de dados SQL](../sql-database/sql-database-technical-overview.md)e [armazenamento do Azure](../storage/storage-introduction.md).

No Gerenciador de recursos do Azure, um endereço [IP público](resource-groups-networking.md#public-ip-address) é um recurso que tem suas próprias propriedades. Você pode associar um recurso de endereço IP público com qualquer um dos seguintes recursos:

- Máquinas virtuais (VM)
- Balanceadores de carga de voltado para a Internet
- Gateways VPN
- Gateways de aplicativos

### <a name="allocation-method"></a>Método de alocação
Há dois métodos em que um endereço IP está alocado para um recurso IP *público* - *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, onde um endereço IP **não** está alocada no momento da sua criação. Em vez disso, o endereço IP público é alocado quando você inicia (ou cria) o recurso associado (como um balanceador de carga ou máquina virtual). O endereço IP é lançado quando você parar (ou excluir) do recurso. Isso faz com que o endereço IP alterar quando você parar e iniciar um recurso.

Para garantir que o endereço IP para o recurso associado permanece o mesmo, você pode definir o método de alocação explicitamente para *estático*. Nesse caso um endereço IP é atribuído imediatamente. Ela foi lançada somente quando você excluir o recurso ou alterar seu método de alocação para *dinâmico*.

>[AZURE.NOTE] Mesmo quando você definir o método de alocação como *estático*, é possível especificar o endereço IP real atribuído ao *recurso IP público*. Em vez disso, ela é alocada de um pool de endereços IP disponíveis no local Azure que o recurso é criado no.

Endereços IP públicos estáticos são usados nas seguintes situações:

- Os usuários finais precisam atualizar regras de firewall para se comunicar com os recursos do Azure.
- Resolução, onde uma alteração no endereço IP exijam Atualizando registros de nome de DNS.
- Seus recursos Azure se comunicar com outros aplicativos ou serviços que usam um modelo de segurança baseada em endereço IP.
- Use os certificados SSL vinculados a um endereço IP.

>[AZURE.NOTE] Na lista de intervalos de IP do qual os endereços IP públicos (dinâmico estático) estão alocados para recursos Azure é publicada em [intervalos de IP do data center Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolução de nome de host DNS
Você pode especificar um rótulo de nome de domínio DNS para um recurso IP público, que cria um mapeamento para *domainnamelabel*. *local*. cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciado Azure. Por exemplo, se você criar um recurso IP público com **contoso** como um *domainnamelabel* nos **EUA Oeste** Azure *local*, o FQDN (nome) de domínio totalmente qualificado **contoso.westus.cloudapp.azure.com** resolverá para o endereço IP público do recurso. Você pode usar este FQDN para criar um registro CNAME de domínio personalizado apontando para o endereço IP público no Azure.

>[AZURE.IMPORTANT] Cada etiqueta de nome de domínio criada deve ser exclusiva dentro de seu local Azure.  

### <a name="virtual-machines"></a>Máquinas virtuais
Você pode associar um endereço IP público um [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) máquina virtual atribuindo a ele à sua **interface de rede**. No caso de uma interface de rede vários máquina virtual, você pode atribuí-lo para a interface de rede *primária* somente. Você pode atribuir um dinâmico ou um endereço IP público estático para uma máquina virtual.

### <a name="internet-facing-load-balancers"></a>Balanceadores de carga de voltado para a Internet
Você pode associar um endereço IP público com um [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md), atribuindo-o para a configuração de **frontend** de Balanceador de carga. Este endereço IP público serve como um balanceamento de carga endereço IP virtual (VIP). Você pode atribuir um dinâmico ou um endereço IP público estático para um balanceador de carga front-end. Você também pode atribuir diversos endereços IP públicos para um balanceador de carga front-end, que permite cenários de [multi-VIP](../load-balancer/load-balancer-multivip.md) como um ambiente de vários locatário com sites baseada em SSL.

### <a name="vpn-gateways"></a>Gateways VPN
[Gateway do Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) é usado para se conectar a uma rede virtual Azure (VNet) a outros VNets Azure ou a uma rede local. Você precisa atribuir um endereço IP público a sua **configuração IP** para ativá-lo para se comunicar com a rede remota. Atualmente, você só pode atribuir um endereço IP público *dinâmico* para um gateway VPN.

### <a name="application-gateways"></a>Gateways de aplicativos
Você pode associar um endereço IP público com um Azure [Application Gateway](../application-gateway/application-gateway-introduction.md), designando-configuração de **front-end** do gateway. Este endereço IP público serve como um VIP balanceamento de carga. Atualmente, você só pode atribuir um endereço IP público *dinâmico* para uma configuração de front-end de gateway do aplicativo.

### <a name="at-a-glance"></a>Em geral
A tabela a seguir mostra a propriedade específico através do qual um endereço IP público possa ser associado a um recurso de nível superior e os métodos de alocação possíveis (dinâmicos ou estáticos) que podem ser usados.

|Recursos de nível superior|Associação de endereço IP|Dinâmico|Estático|
|---|---|---|---|
|Máquina virtual|Interface de rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|Gateway VPN|Configuração de IP do gateway|Sim|Não|
|Gateway de aplicativo|Configuração de front-end|Sim|Não|

## <a name="private-ip-addresses"></a>Endereços IP privados
Endereços IP privados permitem Azure recursos para se comunicar com outros recursos em uma [rede virtual](virtual-networks-overview.md) ou uma rede local através de um gateway VPN ou rota expressa circuito, sem usar um endereço IP Internet acessíveis.

No modelo de implantação do Gerenciador de recursos do Azure, um endereço IP privado está associado aos seguintes tipos de recursos Azure:

- VMs
- Balanceadores de carga interno (ILBs)
- Gateways de aplicativos

### <a name="allocation-method"></a>Método de alocação
Um endereço IP privado é alocado do intervalo de endereços da sub-rede à qual o recurso está conectado. O intervalo de endereços da sub-rede em si é uma parte do intervalo de endereços do VNet.

Há dois métodos em que um endereço IP privado é alocado: *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, onde o endereço IP é alocado automaticamente da sub-rede do recurso (usando DHCP). Este endereço IP pode alterar quando você parar e iniciar o recurso.

Você pode definir o método de alocação para *estático* para garantir que o endereço IP permanece o mesmo. Nesse caso, você também precisa fornecer um endereço IP válido que faz parte de sub-rede do recurso.

Endereços IP particulares estáticos são usados para:

- VMs que atuam como controladores de domínio ou servidores DNS.
- Recursos que exigem regras de firewall usando endereços IP.
- Recursos acessados por outros aplicativos/recursos por meio de um endereço IP.

### <a name="virtual-machines"></a>Máquinas virtuais
Um endereço IP privado é atribuído para a **interface de rede** de um [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) máquina virtual. No caso de uma interface de rede vários máquina virtual, cada interface obtém um endereço IP privado atribuído. Você pode especificar o método de alocação como dinâmico ou estático para uma interface de rede.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Resolução de nome do host DNS interna (para VMs)
Todas as VMs Azure são configuradas com [servidores DNS Azure gerenciados](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você configure explicitamente personalizados servidores DNS. Esses servidores DNS fornecem resolução de nome interno para VMs que residem dentro do mesmo VNet.

Quando você cria uma máquina virtual, um mapeamento para o nome de host para seu endereço IP particular é adicionado para os servidores DNS gerenciado Azure. No caso de uma interface de rede vários máquina virtual, o nome do host é mapeado para o endereço IP privado da interface de rede principal.

VMs configuradas com servidores DNS gerenciado Azure poderão resolver os nomes de host de todas as VMs dentro de seu VNet para seus endereços IP privados.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga interno (ILB) e os gateways de aplicativos
Você pode atribuir um endereço IP particular para a configuração de **front-end** de um [Balanceador de carga interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Gateway de aplicativo do Azure](../application-gateway/application-gateway-introduction.md). Este endereço IP privado serve como uma empresa interna, acessível apenas para os recursos dentro de sua rede virtual (VNet) e as redes remotas conectado à VNet. Você pode atribuir tanto um endereço IP privado dinâmico ou estático na configuração de front-end.

### <a name="at-a-glance"></a>Em geral
A tabela a seguir mostra a propriedade específico através do qual um endereço IP particular pode ser associado a um recurso de nível superior e os métodos de alocação possíveis (dinâmicos ou estáticos) que podem ser usados.

|Recursos de nível superior|Associação de endereço IP|Dinâmico|Estático|
|---|---|---|---|
|Máquina virtual|Interface de rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|Gateway de aplicativo|Configuração de front-end|Sim|Sim|

## <a name="limits"></a>Limites

Os limites impostos endereçamento IP são indicados no conjunto completo de [limites para uma rede](azure-subscription-service-limits.md#networking-limits) no Azure. Esses limites são por região e por assinatura. Você pode [contatar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites de padrão até os limites máximos de acordo com suas necessidades de negócios.

## <a name="pricing"></a>Preços

Endereços IP públicos podem ter um custo nominal. Para saber mais sobre os preços de endereço IP no Azure, examine a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Próximas etapas
- [Implantar uma máquina virtual com IP estático público usando o portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Implantar uma máquina virtual com IP estático público usando um modelo](virtual-network-deploy-static-pip-arm-template.md)
- [Implantar uma máquina virtual com um endereço IP privado estático usando o portal do Azure](virtual-networks-static-private-ip-arm-pportal.md)
