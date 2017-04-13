<properties
   pageTitle="Guia de Design e planejamento de rede Virtual Azure (VNet) | Microsoft Azure"
   description="Saiba como planejar e criar redes virtuais no Azure com base nas necessidades isolamento, conectividade e local."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planejar e criar redes virtuais do Azure

Criar um VNet para experimentar é fácil, mas as chances são você implantará várias VNets ao longo do tempo para suportar as necessidades de produção da sua organização. Com algum planejamento e design, você poderá implantar VNets e conecte os recursos que necessários com mais eficiência. Se você não estiver familiarizado com VNets, recomenda que você [Saiba mais sobre VNets](virtual-networks-overview.md) e [como implantar](virtual-networks-create-vnet-arm-pportal.md) um antes de continuar. 

## <a name="plan"></a>Planejar

Obter uma compreensão completa de assinaturas do Azure, regiões e recursos de rede é essencial para o sucesso. Você pode usar a lista de considerações abaixo como ponto de partida. Depois de compreender essas considerações, você pode definir os requisitos para o seu design de rede.

### <a name="considerations"></a>Considerações

Antes do planejamento de atendimento perguntas abaixo, considere o seguinte:

- Tudo o que você criar no Azure é composto de um ou mais recursos. Uma máquina virtual (VM) é um recurso, a interface de adaptador de rede (NIC) usada por uma máquina virtual é um recurso, o endereço IP público usado por uma NIC é um recurso, o VNet NIC está conectada a é um recurso.
- Criar recursos em uma [região Azure](https://azure.microsoft.com/regions/#services) e assinatura. E recursos só podem ser conectados a um VNet que existe na mesma região e assinatura estiverem em. 
- Você pode conectar VNets uns aos outros, usando um [Gateway VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)do Azure. Você também pode conectar o VNets entre regiões e assinaturas dessa forma.
- Você pode conectar VNets à sua rede local usando uma das [Opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponíveis no Azure. 
- Diferentes recursos podem ser agrupados em [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), tornando mais fácil gerenciar o recurso como uma unidade. Um grupo de recursos pode conter recursos de várias regiões, desde que os recursos pertencem a mesma assinatura.

### <a name="define-requirements"></a>Definir requisitos

Use as perguntas abaixo como ponto de partida para o seu design de rede Azure.  

1. Locais que Azure você usará para o host VNets?
2. Você precisa fornecem comunicação entre esses locais Azure?
3. Você precisa fornecem comunicação entre seu VNet(s) do Azure e datacenter(s) seu local?
4. Quantos infraestrutura como um serviço (IaaS) VMs, serviços em nuvem funções e faça de aplicativos web que você precisa para sua solução?
5. Você precisa isolar o tráfego com base em grupos de VMs (servidores de web ou seja front-end e servidores de banco de dados back-end)?
6. Você precisa controlar o fluxo de tráfego usando dispositivos virtuais?
7. Os usuários precisam diferentes conjuntos de permissões para recursos Azure diferentes?

### <a name="understand-vnet-and-subnet-properties"></a>Entender as propriedades VNet e sub-rede

Recursos VNet e sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Um VNet é caracterizada por um conjunto de espaços de endereço, definido como blocos CIDR. 

>[AZURE.NOTE] Os administradores de rede estão familiarizados com notação CIDR. Se você não estiver familiarizado com CIDR, [Saiba mais sobre ele](http://whatismyipaddress.com/cidr).

VNets contêm as seguintes propriedades.

|Propriedade|Descrição|Restrições|
|---|---|---|
|**nome**|Nome de VNet|Cadeia de caracteres até 80. Pode conter letras, números, sublinhado, períodos ou hifens. Deve começar com uma letra ou número. Deve terminar com uma letra, número ou sublinhado. Pode contém superior ou letras minúsculas.|  
|**local**|Azure local (também conhecido como região).|Deve ser um dos locais de Azure válidos.|
|**addressSpace**|Coleção de prefixos de endereços que constituem o VNet na notação CIDR.|Deve ser uma matriz de blocos de endereço CIDR válidos, incluindo intervalos de endereços IP públicos.|
|**sub-redes**|Coleção de sub-redes que compõem o VNet|Consulte a tabela de propriedades de sub-rede abaixo.||
|**dhcpOptions**|Objeto que contém uma única propriedade necessária chamado **dnsServers**.||
|**dnsServers**|Matriz de servidores DNS usados pela VNet. Se nenhum servidor for especificado, a resolução de nome interno Azure é usada.|Deve ser uma matriz de até 10 servidores DNS, pelo endereço IP.| 

Uma sub-rede é um recurso de filho de um VNet e ajuda a definir segmentos de espaços de endereço em um bloco CIDR, usando prefixos de endereço IP. NICs podem ser adicionadas a sub-redes e conectadas ao VMs, fornecendo conectividade para diversas cargas de trabalho.

Sub-redes contêm as seguintes propriedades. 

|Propriedade|Descrição|Restrições|
|---|---|---|
|**nome**|Nome de sub-rede|Cadeia de caracteres até 80. Pode conter letras, números, sublinhado, períodos ou hifens. Deve começar com uma letra ou número. Deve terminar com uma letra, número ou sublinhado. Pode contém superior ou letras minúsculas.|
|**local**|Azure local (também conhecido como região).|Deve ser um dos locais de Azure válidos.|
|**addressPrefix**|Prefixo único endereço que compõem a sub-rede na notação CIDR|Deve ser um único bloco CIDR que faz parte de um dos espaços de endereço do VNet.|
|**networkSecurityGroup**|NSG aplicado à sub-rede|consulte [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Tabela de rota aplicada à sub-rede|consulte [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Coleção de objetos de configuração de IP usados pelo NICs conectados à sub-rede|consulte [configuração de IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Resolução do nome

Por padrão, usa seu VNet [resolução de nome fornecido Azure.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Para resolver nomes dentro do VNet e na Internet pública. No entanto, se você conectar seu VNets seus centros de dados local, você precisa fornecer o [seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) para resolver os nomes entre suas redes.  

### <a name="limits"></a>Limites

Examine os limites de rede no artigo [limita do Azure](../azure-subscription-service-limits.md#networking-limits) para garantir que seu design não entram em conflito com qualquer um dos limites. Alguns limites podem ser aumentados abrindo um tíquete.

### <a name="role-based-access-control-rbac"></a>Controle de acesso baseado em função (RBAC)

Você pode usar o [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) para controlar o nível de acesso de usuários diferentes podem ter que diferentes recursos no Azure. Dessa forma, que você pode separar o trabalho realizado por sua equipe de acordo com suas necessidades. 

Como diz respeito redes virtuais, os usuários na função **Colaborador de rede** tem controle total sobre os recursos de rede virtual do Gerenciador de recursos do Azure. Da mesma forma, os usuários na função **Colaborador clássico de rede** tem total controle sobre os recursos de rede virtual clássico.

>[AZURE.NOTE] Você também pode [criar suas próprias funções](../active-directory/role-based-access-control-configure.md) para separar suas necessidades administrativas.

## <a name="design"></a>Design

Depois que você tiver as respostas para as perguntas da seção [Planejar](#Plan) , revise o seguinte antes de definir seu VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Número de assinaturas e VNets

Você deve considerar a criação de vários VNets nas seguintes situações:

- **VMs que precisam ser colocados em diferentes locais Azure**. VNets no Azure são regionais. Eles não podem abranger locais. Portanto, você precisa VNet pelo menos uma para cada local Azure que VMs de host em que você deseja.
- **Cargas de trabalho que precisam ser completamente isolados uns dos outros**. Você pode criar VNets separada, que até usar os espaços de endereço IP mesmos, isolar cargas de trabalho diferentes uns dos outros. 

Tenha em mente que os limites que consulte acima são por região, por assinatura. Isso significa que você pode usar várias assinaturas para aumentar o limite de recursos que você pode manter no Azure. Você pode usar uma VPN to-site ou um circuito de rota expressa, para se conectar VNets em diferentes assinaturas.

### <a name="subscription-and-vnet-design-patterns"></a>Assinatura e padrões de design de VNet

A tabela a seguir mostra alguns padrões de design comuns para usar assinaturas e VNets.

|Cenário|Diagrama|Profissionais|Contras|
|---|---|---|---|
|Assinatura única, dois VNets por aplicativo|![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Apenas uma assinatura para gerenciar.|Número máximo de VNets por região Azure. Você precisa de mais inscrições depois disso. Examine o artigo [limita do Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma inscrição por aplicativo, dois VNets por aplicativo|![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Usa apenas dois VNets por assinatura.|Difíceis de gerenciar quando há muitos aplicativos.|
|Uma inscrição por unidade de negócios, dois VNets por aplicativo.|![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Equilíbrio entre o número de assinaturas e VNets.|Número máximo de VNets por unidade de negócios (assinatura). Examine o artigo [limita do Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma inscrição por unidade de negócios, dois VNets por grupo de aplicativos.|![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Equilíbrio entre o número de assinaturas e VNets.|Aplicativos devem ser isolados usando sub-redes e NSGs.|


### <a name="number-of-subnets"></a>Número de sub-redes

Você deve considerar várias sub-redes em uma VNet nas seguintes situações:

- **Não há endereços IP particulares para todas as NICs em uma sub-rede**. Se o seu espaço de endereço de sub-rede não contiver endereços IP suficientes para o número de NICs na sub-rede, você precisa criar várias sub-redes. Tenha em mente que Azure reserva 5 endereços IP particulares de cada sub-rede que não podem ser usados: os endereços de nome e sobrenome do espaço de endereço (para o endereço e sub-rede difusão seletiva) e 3 endereços a ser usado internamente (para fins DHCP e DNS). 
- **Segurança**. Você pode usar sub-redes para separar grupos de VMs uns dos outros para cargas de trabalho que tem uma estrutura de várias camada e aplicar diferentes [grupos de segurança de rede (NSGs)](virtual-networks-nsg.md#subnets) para essas sub-redes.
- **Conectividade de híbrido**. Você pode usar gateways VPN e circuitos de rota expressa para [Conectar](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) seu VNets uns aos outros e para seus center(s) de dados local. Rota expressa circuitos e gateways VPN exigem uma sub-rede próprias seja criado.
- **Eletrodomésticos Virtual**. Você pode usar um dispositivo virtual, como um firewall, aceleração WAN ou gateway VPN em um VNet do Azure. Quando você fizer isso, você precisa [rotear o tráfego](virtual-networks-udr-overview.md) para esses dispositivos e isolá-los em sua própria sub-rede.

### <a name="subnet-and-nsg-design-patterns"></a>Sub-rede e padrões de design NSG

A tabela a seguir mostra alguns padrões de design comuns usando sub-redes.

|Cenário|Diagrama|Profissionais|Contras|
|---|---|---|---|
|Sub-rede única, NSGs por camada de aplicativo, por aplicativo|![Sub-rede única](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Apenas uma sub-rede para gerenciar.|Vários NSGs necessários isolar cada aplicativo.|
|Uma sub-rede por aplicativo, NSGs por camada de aplicativo|![Sub-rede por aplicativo](./media/virtual-network-vnet-plan-design-arm/figure6.png)|NSGs menos para gerenciar.|Várias sub-redes para gerenciar.|
|Uma sub-rede por camada de aplicativo, NSGs por aplicativo.|![Sub-rede por camada](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Equilíbrio entre o número de sub-redes e NSGs.|Número máximo de NSGs por assinatura. Examine o artigo [limita do Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.|
|Uma sub-rede por camada de aplicativo, por aplicativo, NSGs por sub-rede|![Sub-rede por camada por aplicativo](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Possivelmente menor número de NSGs.|Várias sub-redes para gerenciar.|

## <a name="sample-design"></a>Design de amostra

Para ilustrar o aplicativo as informações neste artigo, considere o seguinte cenário.

Você trabalha para uma empresa que tem 2 data centers na América do Norte e dois data centers Europa. Você identificou 6 aplicativos opostas de cliente diferente mantidos por 2 diferentes unidades de negócios que você deseja migrar para o Azure como um piloto. A arquitetura básica para os aplicativos são da seguinte maneira:

- App1, App2, App3 e App4 são aplicativos da web hospedados em servidores Linux executando o Ubuntu. Cada aplicativo se conecta a um servidor de aplicativo separado que hospeda serviços RESTful em servidores Linux. Os serviços RESTful conectem a um banco de dados back-end MySQL.
- App5 e App6 são os aplicativos da web hospedados em servidores Windows que executam o Windows Server 2012 R2. Cada aplicativo se conecta a um banco de dados do SQL Server de back-end.
- No momento, todos os aplicativos são hospedados em um dos centros de dados da empresa na América do Norte.
- Os centros de dados locais usam o espaço de endereço de 10.0.0.0/8.

Você precisa projetar uma solução de rede virtual que atende aos seguintes requisitos:

- Cada unidade de negócios não deve ser afetada por consumo de recursos de outras unidades de negócios.
- Você deve minimizar a quantidade de VNets e sub-redes para facilitar o gerenciamento.
- Cada unidade de negócios deve ter um único teste/desenvolvimento VNet usado para todos os aplicativos.
- Cada aplicativo é hospedado em 2 centros de dados do Azure diferentes por continente (América do Norte e Europa).
- Cada aplicativo é completamente isolado uns dos outros.
- Cada aplicativo pode ser acessado por clientes pela Internet usando HTTP.
- Cada aplicativo pode ser acessado por usuários conectados os centros de dados local usando um túnel criptografado.
- Conexão centros de dados locais deve usar dispositivos VPN existentes.
- Grupo de rede da empresa deve ter controle completo sobre a configuração de VNet.
- Desenvolvedores em cada unidade de negócios só devem ser capazes de implantar VMs em sub-redes existentes.
- Todos os aplicativos serão migrados como eles estão no Azure (lift shift e).
- Os bancos de dados em cada local devem replicar para outros locais Azure uma vez por dia.
- Cada aplicativo deve usar os servidores web front-end 5, 2 servidores de aplicativo (quando necessário) e 2 servidores de banco de dados.

### <a name="plan"></a>Planejar

Você deve iniciar o design do planejamento respondendo à pergunta na seção [Definir requisitos](#Define-requirements) conforme mostrado abaixo.

1. Locais que Azure você usará para o host VNets?

    2 locais na América do Norte e 2 locais na Europa. Você deve escolher aqueles com base na localização física dos data centers existentes no local. Dessa forma a sua conexão de seus locais físicos no Azure terá uma latência melhor.

2. Você precisa fornecem comunicação entre esses locais Azure?

    Sim. Como os bancos de dados devem ser replicados para todos os locais.

3. Você precisa fornecem comunicação entre seu VNet(s) do Azure e seu local center(s) de dados?

    Sim. Como os usuários conectado aos centros de dados locais devem ser capazes de acessar os aplicativos por meio de um túnel criptografado.
 
4. Quantos VMs IaaS você precisa para sua solução?

    200 VMs de IaaS. App1, App2 e App3 exigem 5 servidores web cada, 2 servidores aplicativos cada e 2 servidores de banco de dados. Que é um total de 9 VMs IaaS por aplicativo ou 36 VMs IaaS. App5 e App6 requerem 5 servidores web e 2 servidores de banco de dados. Que é um total de 7 VMs IaaS por aplicativo ou 14 VMs IaaS. Portanto, você precisa 50 IaaS VMs para todos os aplicativos em cada região Azure. Desde que precisamos usar regiões de 4, haverá 200 VMs IaaS.

    Você também precisará fornecer servidores DNS em cada VNet, ou em seus centros de dados locais para resolver nome entre suas VMs de IaaS do Azure e sua rede local. 

5. Você precisa isolar o tráfego com base em grupos de VMs (servidores de web ou seja front-end e servidores de banco de dados back-end)?

    Sim. Cada aplicativo deve ser completamente isolado uns dos outros, e cada camada de aplicativo também deve ser isolada. 

6. Você precisa controlar o fluxo de tráfego usando dispositivos virtuais?

    Não. Dispositivos virtuais podem ser usados para fornecer mais controle sobre o fluxo de tráfego, incluindo logs de plano de dados mais detalhados. 

7. Os usuários precisam diferentes conjuntos de permissões para recursos Azure diferentes?

    Sim. A equipe de rede precisa controle total sobre as configurações de rede virtuais, enquanto desenvolvedores somente devem ser capazes de implantar suas VMs em sub-redes preexistentes. 

### <a name="design"></a>Design

Você deve seguir o design especificando assinaturas, VNets, sub-redes e NSGs. Abordaremos NSGs aqui, mas você deve saber mais sobre [NSGs](virtual-networks-nsg.md) antes de concluir o design.

**Número de assinaturas e VNets**

Os seguintes requisitos estão relacionados a assinaturas e VNets:

- Cada unidade de negócios não deve ser afetada por consumo de recursos de outras unidades de negócios.
- Você deve minimizar a quantidade de VNets e sub-redes.
- Cada unidade de negócios deve ter um único teste/desenvolvimento VNet usado para todos os aplicativos.
- Cada aplicativo é hospedado em 2 centros de dados do Azure diferentes por continente (América do Norte e Europa).

Com base nesses requisitos, você precisa uma assinatura para cada unidade de negócios. Dessa maneira, consumo de recursos de uma unidade de negócios não contará na direção limites para outras unidades de negócios. E como você deseja minimizar o número de VNets, você deve considerar usando o padrão de **uma assinatura por unidade de negócios, dois VNets por grupo de aplicativos** , conforme mostrado abaixo.

![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Você também precisa especificar o espaço de endereço para cada VNet. Como você precisa centros de conectividade entre os dados locais e as regiões Azure, o espaço de endereço usado para VNets do Azure não pode conflitar com a rede local e o espaço de endereço usado por cada VNet não deve conflitar com outros VNets existente. Você pode usar os espaços de endereço na tabela a seguir para atender a esses requisitos.  

|**Assinatura**|**VNet**|**Região Azure**|**Espaço de endereço**|
|---|---|---|---|
|BU1|ProdBU1US1|Oeste EUA|172.16.0.0/16|
|BU1|ProdBU1US2|Leste EUA|172.17.0.0/16|
|BU1|ProdBU1EU1|Norte da Europa|172.18.0.0/16|
|BU1|ProdBU1EU2|Europa Ocidental|172.19.0.0/16|
|BU1|TestDevBU1|Oeste EUA|172.20.0.0/16|
|BU2|TestDevBU2|Oeste EUA|172.21.0.0/16|
|BU2|ProdBU2US1|Oeste EUA|172.22.0.0/16|
|BU2|ProdBU2US2|Leste EUA|172.23.0.0/16|
|BU2|ProdBU2EU1|Norte da Europa|172.24.0.0/16|
|BU2|ProdBU2EU2|Europa Ocidental|172.25.0.0/16|

**Número de sub-redes e NSGs**

Os seguintes requisitos estão relacionados à sub-redes e NSGs:

- Você deve minimizar a quantidade de VNets e sub-redes.
- Cada aplicativo é completamente isolado uns dos outros.
- Cada aplicativo pode ser acessado por clientes pela Internet usando HTTP.
- Cada aplicativo pode ser acessado por usuários conectados os centros de dados local usando um túnel criptografado.
- Conexão centros de dados locais deve usar dispositivos VPN existentes.
- Os bancos de dados em cada local devem replicar para outros locais Azure uma vez por dia.

Com base nesses requisitos, você pode usar uma sub-rede por camada de aplicativo e usar NSGs para filtrar o tráfego por aplicativo. Dessa forma, você só tem 3 sub-redes em cada VNet (front-end, a camada de aplicativos e camada de dados) e um NSG por aplicativo por sub-rede. Nesse caso, você deve considerar usando o padrão de design de **uma sub-rede por camada de aplicativo, NSGs por aplicativo** . A figura a seguir mostra o uso do padrão de design que representa o VNet **ProdBU1US1** .

![Uma sub-rede por camada, um NSG por aplicativo por camada](./media/virtual-network-vnet-plan-design-arm/figure11.png)

No entanto, você também precisa criar uma sub-rede adicional para a conectividade VPN entre o VNets e seus centros de dados locais. E você precisará especificar o espaço de endereço para cada sub-rede. A figura a seguir mostra um exemplo de solução para **ProdBU1US1** VNet. Você deseja duplicar este cenário para cada VNet. Cada cor representa um aplicativo diferente.

![VNet de amostra](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controle de acesso**

Os seguintes requisitos relacionados ao controle de acesso:

- Grupo de rede da empresa deve ter controle completo sobre a configuração de VNet.
- Desenvolvedores em cada unidade de negócios só devem ser capazes de implantar VMs em sub-redes existentes.

Com base nesses requisitos, você pode adicionar usuários da equipe de rede à função de **Colaborador de rede** interna em cada assinatura; e criar uma função personalizada para os desenvolvedores de aplicativos em cada assinatura lhes direitos para adicionar VMs ao sub-redes existentes.

## <a name="next-steps"></a>Próximas etapas

- [Implantar uma rede virtual](virtual-networks-create-vnet-arm-template-click.md) com base em um cenário.
- Entender como [o balanceamento de carga](../load-balancer/load-balancer-overview.md) IaaS VMs e [Gerenciar o roteamento por várias regiões Azure](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre [NSGs e como planejar e projetar](virtual-networks-nsg.md) uma solução NSG.
- Saiba mais sobre suas [entre locais e opções de conectividade de VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
