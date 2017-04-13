<properties
   pageTitle="Pública e privada endereçamento IP (clássico) no Azure | Microsoft Azure"
   description="Saiba mais sobre pública e privada endereçamento IP no Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Endereços IP (clássico) no Azure
Você pode atribuir endereços IP aos recursos Azure para se comunicar com outros recursos Azure, sua rede local e a Internet. Há dois tipos de endereços IP, você pode usar no Azure: pública e privada.

Endereços IP públicos são usados para comunicação com a Internet, incluindo os serviços do Azure público.

Endereços IP privados são usados para comunicação dentro de uma rede virtual Azure (VNet), um serviço de nuvem e sua rede local quando você usa um gateway VPN ou circuito de rota expressa para estender sua rede para o Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo de implantação do Gerenciador de recursos](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitem Azure recursos para se comunicar com serviços de Internet e Azure público como [Azure relacionada Cache](https://azure.microsoft.com/services/cache/), [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/), [bancos de dados SQL](../sql-database/sql-database-technical-overview.md)e [armazenamento do Azure](../storage/storage-introduction.md).

Um endereço IP público é associado com os seguintes tipos de recurso:

- Serviços de nuvem
- IaaS VMs (máquinas virtuais)
- Instâncias de função de PaaS
- Gateways VPN
- Gateways de aplicativos

### <a name="allocation-method"></a>Método de alocação
Quando um endereço IP público precisa ser atribuída a um recurso Azure, é *dinamicamente* alocado de um pool de endereço IP público disponível dentro do local em que o recurso é criado. Esse endereço IP é lançado quando o recurso for interrompido. No caso de um serviço de nuvem, isso acontece quando todas as instâncias de função são interrompidas, que pode ser evitar usando um endereço IP *estático* (reservado) (consulte [Serviços de nuvem](#Cloud-services)).

>[AZURE.NOTE] Na lista de intervalos de IP do qual os endereços IP públicos estão alocados para recursos Azure é publicada em [intervalos de IP do data center Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolução de nome de host DNS
Quando você cria uma VM IaaS ou um serviço de nuvem, você precisa fornecer um nome DNS de serviço de nuvem que seja exclusivo entre todos os recursos no Azure. Isso cria um mapeamento nos servidores DNS gerenciado Azure *dnsname*. cloudapp.net para o endereço IP público do recurso. Por exemplo, quando você cria um serviço na nuvem com um nome DNS do serviço de nuvem da **contoso**, o FQDN (nome) de domínio totalmente qualificado **contoso.cloudapp.net** resolverá para um endereço IP público (VIP) do serviço de nuvem. Você pode usar este FQDN para criar um registro CNAME de domínio personalizado apontando para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços de nuvem
Um serviço de nuvem sempre tem um endereço IP público conhecido como um endereço IP virtual (VIP). Você pode criar pontos de extremidade em um serviço de nuvem para associar portas diferentes no VIP interna portas VMs e instâncias de função dentro do serviço de nuvem. 

Um serviço de nuvem pode conter várias VMs IaaS ou instâncias de função de PaaS, tudo expostas por meio do mesmo VIP de serviço de nuvem. Você também pode atribuir [VIPs múltiplos para um serviço de nuvem](../load-balancer/load-balancer-multivip.md), que permite cenários de multi-VIP como ambiente de vários locatário com sites baseada em SSL.

Você pode garantir que o endereço IP público de um serviço de nuvem permanece o mesmo, mesmo quando todas as instâncias de função são interrompidas, usando um endereço IP público *estático* , chamado de [IP reservado](virtual-networks-reserved-public-ip.md). Você pode criar um recurso IP estático (reservado) em um local específico e atribuí-la a qualquer serviço de nuvem nesse local. Você não pode especificar o endereço IP real para o IP reservado, ela é alocada do pool de endereços IP disponíveis no local que é criado. Este endereço IP não será lançado até que você explicitamente excluí-lo.

Estáticos endereços IP públicos (reservados) são usados nos cenários onde um serviço de nuvem:

- requer regras de firewall sejam configurados pelos usuários finais.
- depende de resolução de nomes DNS externa, e um IP dinâmico pode ser necessário atualizar registros.
- consome serviços web externos que usam o modelo de segurança com base em IP.
- usa certificados SSL vinculados a um endereço IP.

>[AZURE.NOTE] Quando você cria uma máquina virtual clássica, um contêiner de *serviço de nuvem* é criado pela Azure, que tem um endereço IP virtual (VIP). Quando a criação é feita por meio do portal, um padrão RDP ou SSH *ponto de extremidade* é configurado pelo portal para que você possa se conectar para a máquina virtual por meio do VIP de serviço de nuvem. Este VIP de serviço de nuvem pode ser reservada, que efetivamente fornece um endereço IP reservado para conectar-se para a máquina virtual. Você pode abrir portas adicionais configurando mais pontos de extremidade.

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VMs e PaaS instâncias de função
Você pode atribuir um endereço IP público diretamente para um IaaS [máquina virtual](../virtual-machines/virtual-machines-linux-about.md) ou instância de função de PaaS dentro de um serviço de nuvem. Isso é conhecido como um endereço IP público do nível de instância ([ILPIP](virtual-networks-instance-level-public-ip.md)). Este endereço IP público só pode ser dinâmico.

>[AZURE.NOTE] Isso é diferente do VIP de serviço de nuvem, o que é um contêiner de IaaS VMs ou PaaS instâncias de função, como um serviço de nuvem pode conter várias VMs IaaS ou instâncias de função de PaaS, todos exposto o mesmo VIP de serviço de nuvem.

### <a name="vpn-gateways"></a>Gateways VPN
Um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usado para conectar um VNet do Azure para outros Azure VNets ou redes de local. Um gateway VPN é atribuído um público IP endereço *dinamicamente*, que permite a comunicação com a rede remota.

### <a name="application-gateways"></a>Gateways de aplicativos
Um [gateway de aplicativo](../application-gateway/application-gateway-introduction.md) do Azure podem ser usados para Layer7 balanceamento de carga para rotear o tráfego de rede com base em HTTP. Gateway de aplicativo é atribuído um público IP endereço *dinamicamente*, que serve como o VIP balanceamento de carga.

### <a name="at-a-glance"></a>Num relance
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico estático) e capacidade de atribuir diversos endereços IP públicos.

|Recurso|Dinâmico|Estático|Vários endereços IP|
|---|---|---|---|
|Serviço de nuvem|Sim|Sim|Sim|
|VM IaaS ou PaaS instância de função|Sim|Não|Não|
|Gateway VPN|Sim|Não|Não|
|Gateway de aplicativo|Sim|Não|Não|

## <a name="private-ip-addresses"></a>Endereços IP privados
Endereços IP privados permitem Azure recursos para se comunicar com outros recursos em um serviço na nuvem ou em uma [rede virtual](virtual-networks-overview.md)(VNet), ou para a rede local (por meio de um gateway VPN ou rota expressa circuito), sem usar um endereço IP Internet acessíveis.

No modelo de implantação clássico Azure, um endereço IP particular pode ser atribuído os seguintes recursos Azure:

- IaaS VMs e PaaS instâncias de função
- Balanceador de carga interno
- Gateway de aplicativo

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VMs e PaaS instâncias de função
Máquinas virtuais (VMs) criadas com o modelo clássico de implantação são sempre colocadas em um serviço de nuvem, semelhante a instâncias de função de PaaS. O comportamento de endereços IP particulares, portanto, são semelhantes para esses recursos.

É importante observar que um serviço de nuvem pode ser implantado de duas maneiras:

- Como um nuvem serviço *autônomo* , onde ele não está dentro de uma rede virtual.
- Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
No caso de um serviço de nuvem *autônomo* , recursos Obtenha uma privada IP endereço alocado *dinamicamente* do data center Azure particular intervalo de endereço IP. Ele pode ser usado somente para comunicação com outras VMs dentro do mesmo serviço de nuvem. Este endereço IP pode alterar quando o recurso for interrompido e iniciado.

No caso de um serviço de nuvem implantado em uma rede virtual, recursos obtém particular endereços IP alocados do intervalo de endereços do subnet(s) associado (conforme especificado em sua configuração de rede). Este endereços IP particular podem ser usados para a comunicação entre todas as VMs dentro do VNet.

Além disso, no caso de serviços de nuvem dentro de um VNet, um endereço IP privado é alocado *dinamicamente* (usando DHCP) por padrão. Ele pode alterar quando o recurso for interrompido e iniciado. Para garantir que o endereço IP permanece o mesmo, você precisa definir o método de alocação para *estático*e forneça um endereço IP válido dentro do intervalo de endereço correspondente.

Endereços IP particulares estáticos são usados para:

 - VMs que atuam como controladores de domínio ou servidores DNS.
 - VMs que exigem regras de firewall usando endereços IP.
 - VMs executando serviços acessados por outros aplicativos por meio de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução de nome de host DNS interna
Todas as VMs do Azure e instâncias de função de PaaS são configuradas com [servidores DNS Azure gerenciados](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você configure explicitamente personalizados servidores DNS. Esses servidores DNS fornecem resolução de nome interno para VMs e instâncias de função que residem dentro do mesmo serviço VNet ou na nuvem.

Quando você cria uma máquina virtual, um mapeamento para o nome de host para seu endereço IP particular é adicionado para os servidores DNS gerenciado Azure. No caso de uma máquina virtual multi-NIC, o nome do host é mapeado para o endereço IP privado NIC principal. No entanto, essa informação de mapeamento é restrita aos recursos dentro do mesmo serviço de nuvem ou VNet.

No caso de um serviço de nuvem *autônomo* , você poderá resolver nomes de host de todas as instâncias de VMs/função dentro do mesmo serviço de nuvem apenas. No caso de um serviço de nuvem dentro de uma VNet, você poderá resolver nomes de host de todas as instâncias de VMs/função dentro do VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga interno (ILB) e os gateways de aplicativos
Você pode atribuir um endereço IP particular para a configuração de **front-end** de um [Balanceador de carga interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Gateway de aplicativo do Azure](../application-gateway/application-gateway-introduction.md). Este endereço IP privado serve como uma empresa interna, acessível apenas para os recursos dentro de sua rede virtual (VNet) e as redes remotas conectado à VNet. Você pode atribuir tanto um endereço IP privado dinâmico ou estático na configuração de front-end. Você também pode atribuir vários endereços IP particulares para habilitar cenários de multi-vip.

### <a name="at-a-glance"></a>Num relance
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico estático) e capacidade de atribuir vários endereços IP particulares.

|Recurso|Dinâmico|Estático|Vários endereços IP|
|---|---|---|---|
|Máquina virtual (em um serviço de nuvem *autônomo* )|Sim|Sim|Sim|
|Instância de função de PaaS (em um serviço de nuvem *autônomo* )|Sim|Não|Sim|
|Instância de função de máquina virtual ou PaaS (em um VNet)|Sim|Sim|Sim|
|Front-end balanceador de carga interno|Sim|Sim|Sim|
|Front-end de gateway aplicativo|Sim|Sim|Sim|

## <a name="limits"></a>Limites

A tabela abaixo mostra os limites de impostos no IP endereçamento no Azure por assinatura. Você pode [contatar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites de padrão até os limites máximos de acordo com suas necessidades de negócios.

||Limite padrão|Limite máximo|
|---|---|---|
|Endereços IP públicos (dinâmico)|5|Contate o suporte|
|Endereços IP públicos reservados|20|Contate o suporte|
|VIP público por implantação (serviço de nuvem)|5|Contate o suporte|
|Particular VIP (ILB) por implantação (serviço de nuvem)|1|1|

Certifique-se de que você leia o conjunto completo de [limites para uma rede](azure-subscription-service-limits.md#networking-limits) no Azure.

## <a name="pricing"></a>Preços

Na maioria dos casos, os endereços IP públicos são gratuitos. Há um custo nominal usar adicionais e/ou estáticos endereços IP públicos. Verifique se que você compreender a [estrutura para público IPs de preços](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre o Gerenciador de recursos e implantações clássicas
Abaixo está uma comparação de recursos de endereçamento de IP no Gerenciador de recursos e o modelo de implantação clássico.

||Recurso|Clássico|Gerenciador de recursos|
|---|---|---|---|
|**Endereço IP público**|MÁQUINA VIRTUAL|Denomina um ILPIP (somente dinâmico)|Denomina um público IP (dinâmico ou estático)|
|||Atribuídos a uma VM IaaS ou uma instância da função PaaS|Associado NIC a máquina virtual|
||Balanceador de carga oposta da Internet|Denomina VIP (dinâmico) ou IP reservado (estática)|Denomina um público IP (dinâmico ou estático)|
|||Atribuído a um serviço de nuvem|Associado a configuração de front-end do balanceador de carga|
||||
|**Endereço IP particular**|MÁQUINA VIRTUAL|Denomina um DIP|Conhecido como um endereço IP privado|
|||Atribuídos a uma VM IaaS ou uma instância da função PaaS|Atribuído a NIC da VM|
||Balanceador de carga interno (ILB)|Atribuído ao ILB (dinâmico ou estático)|Atribuído a configuração de front-end do ILB (estática ou dinâmica)|

## <a name="next-steps"></a>Próximas etapas
- [Implantar uma máquina virtual com um endereço IP privado estático](virtual-networks-static-private-ip-classic-pportal.md) usando o portal de clássico.
