<properties
   pageTitle="Visão geral de segurança de rede Azure | Microsoft Azure"
   description=" Este artigo torna mais fácil entender o que a Microsoft Azure tem a oferecer na área de segurança de rede. Fornecemos explicações básicas para os conceitos básicos de segurança de rede e requisitos e informações sobre que Azure tem a oferecer em cada uma dessas áreas. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Visão geral de segurança de rede Azure

Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte ao seu aplicativo e os requisitos de conectividade do serviço. Conectividade de rede é possível entre recursos localizados no Azure, entre locais e Azure hospedado recursos e para e da Internet e Azure.

O objetivo deste artigo é tornar mais fácil entender o que a Microsoft Azure tem a oferecer na área de segurança de rede. Aqui, fornecemos explicações básicas para requisitos e conceitos básicos de segurança de rede. Podemos também fornecer informações em que Azure tem a oferecer em cada uma dessas áreas. Há vários links para outro conteúdo que permitirá que você obtenha uma compreensão mais profunda para as áreas nas quais você está interessado.

Este artigo de visão geral de segurança de rede do Azure se concentrará no seguinte:

- Rede Azure
- Controle de acesso de rede
- Proteger conectividade remota de acesso e entre locais
- Disponibilidade
- Registro em log
- Resolução do nome
- Arquitetura de DMZ
- O Centro de segurança do Azure

## <a name="azure-networking"></a>Rede Azure

Máquinas virtuais precisam de conectividade de rede. Para oferecer suporte a esse requisito, o Azure requer máquinas virtuais estar conectado a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica criada na parte superior da estrutura de rede Azure física. Cada rede Virtual do Azure lógica está isolada de todas as outras redes Virtual do Azure. Isso ajuda a garantir que o tráfego de rede nas implantações não é acessível para outros clientes do Microsoft Azure.

Saiba Mais:

- [Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controle de acesso de rede
Controle de acesso de rede é o ato de limitar a conectividade de e para dispositivos específicos ou sub-redes dentro de uma rede Virtual do Azure. O objetivo de controle de acesso de rede é garantir que seus serviços e máquinas virtuais estão acessíveis somente para usuários e dispositivos ao qual você deseja-las acessíveis. Controles de acesso são baseados em permitir ou negar decisões para conexões de e para sua máquina virtual ou serviço.

Azure oferece suporte a vários tipos de controle de acesso de rede. Eles incluem:

- Controle de camada de rede
- Encaminhar o controle e forçada túnel
- Dispositivos de segurança de rede virtual

### <a name="network-layer-control"></a>Controle de camada de rede
Qualquer implantação segura requer algumas medidas de controle de acesso de rede. O objetivo de controle de acesso de rede é certificar-se de que suas máquinas virtuais e os serviços de rede que executar nessas máquinas virtuais possam se comunicar apenas com outros dispositivos de rede que eles precisam se comunicar com e todas as outras tentativas de conexão são bloqueadas.

Se precisar de controle de acesso no nível de rede básica (baseado no endereço IP e os protocolos TCP ou UDP), você pode usar grupos de segurança de rede. Um grupo de segurança de rede (NSG) é um firewall de filtragem de pacote com informações de estado básicas e lhe permite controlar o acesso com base em uma [tupla de 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs não fornecem inspeção de camada de aplicativo ou autenticado controles de acesso.

Saiba Mais:

- [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçada
A capacidade de controlar o comportamento de roteamento nas redes virtuais Azure é um recurso de controle de segurança e acesso de rede críticas. Se o roteamento estiver configurado incorretamente, aplicativos e serviços hospedados em sua máquina virtual podem se conectar a dispositivos que não quiser que elas se conectar, incluindo dispositivos propriedade e operada de possíveis ataques.

Rede Azure suporta a capacidade de personalizar o comportamento de roteamento para tráfego de rede nas redes virtuais Azure. Isso permite que você altere as entradas de tabela de roteamento de padrão da sua rede Virtual do Azure. Controle de roteamento comportamento ajuda a que garantir que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou sai da sua rede Virtual do Azure por meio de um local específico.

Por exemplo, você pode ter um dispositivo de segurança de rede virtual na sua rede Virtual do Azure. Você quer certificar-se de que todo o tráfego de e para sua rede Virtual do Azure percorre esse dispositivo de segurança virtual. Você pode fazer isso configurando [Rotas de definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) no Azure.

[Forçado túnel](https://www.petri.com/azure-forced-tunneling) é um mecanismo que você pode usar para garantir que seus serviços não são permitidos para iniciar uma conexão com dispositivos na Internet. Observe que isso é diferente de aceitar conexões de entrada e, em seguida, responder a eles. Servidores web front-end precisam responder a solicitação de hosts da Internet e então originar de Internet tráfego permitido de entrada para esses servidores web e os servidores web tem permissão para responder.

O que você não deseja permitir que é um servidor web front-end para iniciar uma solicitação de saída. Tais solicitações podem representar um risco de segurança porque essas conexões podem ser usadas para fazer o download de malware. Mesmo se você quiser estes servidores front-end para iniciar solicitações de saída com a Internet, talvez você queira forçar eles passam por proxies de web seu local para que você possa aproveitar URL filtragem e registro em log.

Em vez disso, você usaria túnel forçada para impedir que isso. Quando você habilita túnel forçada, todas as conexões com a Internet são obrigadas por meio de seu gateway local. Você pode configurar túnel forçada aproveitando rotas de definidas pelo usuário.

Saiba Mais:

- [O que são rotas de definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual
Enquanto a grupos de segurança de rede, rotas de definidas pelo usuário e túnel forçada fornecem um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), pode haver ocasiões quando você deseja ativar a segurança em níveis superiores a rede.

Por exemplo, seus requisitos de segurança podem incluir:

- Autenticação e autorização antes de permitir o acesso ao seu aplicativo
- Detecção de intrusos e resposta de intrusos
- Inspeção de camada de aplicativo para protocolos de alto nível
- Filtragem de URLs
- Antimalware e antivírus de nível de rede
- Proteção anti-bot
- Controle de acesso do aplicativo
- Proteção de DDoS adicional (acima o DDoS proteção fornecida a própria estrutura Azure)

Você pode acessar esses recursos de segurança de rede aprimorada usando uma solução de parceiro Azure. Você pode encontrar o parceiro Azure mais recente soluções de segurança de rede visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurar por "segurança" e "rede".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade entre locais
Instalação, configuração e gerenciamento de suas necessidades de recursos Azure ser realizadas remotamente. Além disso, talvez você queira implantar soluções de [TI híbridos](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que têm componentes locais e na nuvem pública Azure. Esses cenários exigem acesso remoto seguro.

Rede Azure oferece suporte para os seguintes cenários de acesso remoto seguro:

- Conectar estações de trabalho individuais a uma rede Virtual do Azure
- Conectar a sua rede local para uma rede Virtual do Azure com uma VPN
- Conectar a sua rede local para uma rede Virtual do Azure com um link WAN dedicado
- Conectar redes virtuais Azure uns aos outros

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Conectar estações de trabalho individuais a uma rede Virtual Azure
Pode haver ocasiões em que você deseja habilitar pessoal individual de desenvolvedores ou operações para gerenciar serviços no Azure e máquinas virtuais. Por exemplo, você precisa ter acesso a uma máquina virtual em uma rede Virtual do Azure e sua política de segurança não permitir acesso remoto RDP ou SSH para máquinas virtuais individuais. Nesse caso, você pode usar uma conexão de VPN ponto-a-site.

A conexão de VPN ponto-a-site usa o protocolo [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) para que você possa configurar uma conexão privada e segura entre o usuário e a rede Virtual do Azure. Depois que a conexão VPN é estabelecida, o usuário poderá RDP ou SSH no link VPN em qualquer máquina virtual em uma rede Virtual Azure (supondo que o usuário pode autenticar e está autorizado).

Saiba Mais:

- [Configurar uma Conexão ponto-a-Site a uma rede Virtual usando o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Conectar a sua rede local com uma rede Virtual Azure usando uma VPN
Talvez você queira conectar sua rede corporativa inteira, ou partes dele, a uma rede Virtual do Azure. Isso é comum em TI híbridos cenários onde empresas [estender Center local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos empresas hospedará partes de um serviço no Azure e peças no local, como quando uma solução inclui os servidores web front-end do Azure e bancos de dados back-end local. Esses tipos de conexões de "entre locais" também tornar o gerenciamento do Azure localizado recursos mais segura e habilitar cenários como estendendo controladores de domínio do Active Directory em Azure.

Uma maneira de fazer isso é usar uma [VPN to-site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN to-site e ponto-a-site VPN é que uma VPN de ponto-a-site se conecta um único dispositivo para uma rede Virtual do Azure, enquanto uma VPN to-site se conecta a uma rede inteira (como sua rede local) a uma rede Virtual do Azure. VPNs to-site a uma rede Virtual do Azure usam o modo de túnel IPsec altamente seguro protocolo VPN.

Saiba Mais:

- [Criar um VNet Gerenciador de recursos com uma conexão de VPN-to-site usando o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planejamento e design para gateway VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Conectar a sua rede local com uma rede Virtual Azure usando uma conexão WAN dedicada
Conexões ponto-a-site e to-site VPN são eficazes para habilitar a conectividade entre locais. No entanto, algumas organizações convém que tenham as seguintes desvantagens:

- Conexões VPN mover dados pela Internet – isso expõe essas conexões possíveis problemas de segurança envolvidos na movimentação de dados em uma rede pública. Além disso, confiabilidade e disponibilidade para conexões com a Internet não não possível garantir.
- Conexões VPN para redes virtuais Azure podem ser considerados largura de banda restrita para alguns aplicativos e fins, como eles máx check-out ao redor de 200Mbps.

Organizações que precisam do alto nível de segurança e disponibilidade para suas conexões entre locais normalmente usam links da WAN dedicados para se conectar a sites remotos. Azure oferece a capacidade de usar uma conexão WAN dedicada que você pode usar para se conectar a sua rede local para uma rede Virtual do Azure. Isso é ativado por meio de rota expressa do Azure.

Saiba Mais:

- [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Conectar redes virtuais Azure uns aos outros
É possível usar muitas redes virtuais do Azure para suas implantações. Há muitos motivos por que você pode fazer isso. Um dos motivos pode ser a simplificar o gerenciamento; outro pode ser por razões de segurança. Independentemente da motivação ou lógica para colocar recursos em diferentes redes virtuais do Azure, pode haver momentos, você querer recursos em cada uma das redes conectem uns com os outros.

Uma opção seria para serviços em uma rede Virtual do Azure para se conectar aos serviços em outra rede Virtual do Azure pelo "loop volta" através da Internet. A conexão seria Iniciar em uma rede Virtual do Azure, vá através da Internet e, em seguida, volte para o destino rede Virtual do Azure. Esta opção expõe a conexão para os problemas de segurança inerentes para qualquer comunicação baseados na Internet.

Talvez seja a melhor opção Criar um Azure Virtual rede ao Azure Virtual Network-to-site VPN. Este Azure Virtual rede ao Azure Virtual Network-to-site VPN usa o mesmo protocolo de [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) como a conexão de VPN entre locais-to-site mencionado acima.

A vantagem de usar um Azure Virtual rede ao Azure Virtual Network-to-site VPN é que a conexão VPN é estabelecida na estrutura rede Azure; ele não se conectar pela Internet. Isso fornece uma camada extra de segurança em comparação com VPNs to-site que se conectam pela Internet.

Saiba Mais:

- [Configurar uma Conexão de VNet para VNet usando o Gerenciador de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidade
Disponibilidade é um componente fundamental de qualquer programa de segurança. Se os usuários e sistemas não podem acessar o que eles precisam acessar através da rede, o serviço pode ser considerado comprometido. Azure tem tecnologias de rede que oferecem suporte a mecanismos de alta disponibilidade a seguir:

- Balanceamento de carga de HTTP
- Balanceamento de carga nível de rede
- Balanceamento de carga global

Balanceamento de carga é um mecanismo criado para distribuir igualmente conexões entre vários dispositivos. As metas de balanceamento de carga são:

- Aumentar a disponibilidade – quando você carrega conexões de saldo em vários dispositivos, um ou mais dos dispositivos podem ficar indisponível e os serviços em execução nos dispositivos restantes onlinehttps podem continuar a servir o conteúdo do serviço
- Aumentar o desempenho – quando você carrega conexões de saldo em vários dispositivos, um único dispositivo não precisa levar a ocorrência de processador. Em vez disso, as exigências de processamento e memória para servir o conteúdo estiver distribuída em vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga de HTTP
As organizações que executam serviços baseados na web muitas vezes desejam ter um balanceador de carga baseado em HTTP na frente desses serviços da web para ajudar a garantir níveis adequados de desempenho e alta disponibilidade. Em contraste com balanceadores de carga tradicional baseada em rede, decisões tomadas pelo baseado em HTTP de balanceamento de carga balanceadores de carga são baseados em características do protocolo HTTP, não nos protocolos de camada de transporte e de rede.

Para fornecer baseado em HTTP balanceamento de carga para seus serviços baseados na web, o Azure fornece o Gateway de aplicativo do Azure. O Gateway de aplicativo do Azure compatível com:

- Baseada em HTTP balanceamento de carga – decisões de balanceamento de carga são feitas com base na característica especiais para o protocolo HTTP
- Afinidade de sessão baseada em cookies – esse recurso garante que conexões estabelecidas para um dos servidores atrás que balanceador de carga permanece intacta entre o cliente e servidor. Isso assegura estabilidade das transações.
- Descarregamento SSL – quando uma conexão de cliente for estabelecida com o balanceador de carga, que sessão entre o cliente e o balanceador de carga está criptografado usando o HTTPS (SSL /) protocolo. No entanto, para aumentar o desempenho, você tem a opção para que a conexão entre o balanceador de carga e o servidor de web atrás o uso de Balanceador de carga o protocolo HTTP (não criptografado). Isso é conhecido como "SSL descarregar" porque os servidores web atrás balanceador de carga não enfrenta a sobrecarga de processador envolvida na criptografia e, portanto, devem ser capazes de solicitações de serviço mais rapidamente.
- Baseado em URL encaminhamento de conteúdo – esse recurso possibilita balanceador de carga tomar decisões sobre para onde encaminhar conexões com base na URL de destino. Isso fornece flexibilidade muito mais do que as soluções que tornam carregar balanceamento decisões com base em endereços IP.

Saiba Mais:

- [Visão geral de Gateway do aplicativo](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Balanceamento de carga nível de rede
Em contraste com balanceamento de carga baseado em HTTP, balanceamento de carga de nível torna decisões de balanceamento de carga com base no endereço e porta (TCP ou UDP) números de IP.
Você pode obter os benefícios rede nível de balanceamento de carga no Azure usando o balanceador de carga do Azure. Algumas características principais de Balanceador de carga Azure incluem:

- Balanceamento de carga de nível de rede com base em números de porta e endereço IP
- Suporte para qualquer protocolo de camada de aplicativo
- Carregar saldos Azure máquinas virtuais e instâncias de função de serviços de nuvem
- Pode ser usado para voltado para a Internet (balanceamento de carga externa) e não-Internet opostas máquinas virtuais e aplicativos (balanceamento de carga interna)
- Ponto de extremidade de monitoramento, que é usado para determinar se qualquer um dos serviços atrás balanceador de carga tornaram indisponível

Saiba Mais:

- [Internet opostas balanceador de carga entre várias máquinas virtuais ou serviços](../load-balancer/load-balancer-internet-overview.md)
- [Visão geral de Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global
Algumas organizações desejará o nível mais alto de disponibilidade possíveis. É uma maneira de atingir meta para hospedar aplicativos em data centers distribuídos globalmente. Quando um aplicativo estiver hospedado em data centers localizados em todo o mundo, é possível para uma região geopolíticas inteira indisponíveis e ainda ter o aplicativo para cima e em execução.

Além das vantagens de disponibilidade que obter hospedando aplicativos em data centers distribuídos globalmente, você também pode obter benefícios de desempenho. Esses benefícios de desempenho podem ser obtidos usando um mecanismo que direciona as solicitações para o serviço ao data center mais próxima o dispositivo que está fazendo a solicitação.

Balanceamento de carga global pode fornecer ambos os benefícios. No Azure, você pode obter os benefícios global de balanceamento de carga usando o Gerenciador de tráfego do Azure.

Saiba Mais:

- [O que é o Gerenciador de tráfego?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Registro em log
Registro em log em um nível de rede é uma função essencial para qualquer cenário de segurança de rede. No Azure, você pode registrar informações obtidas para grupos de segurança de rede obter informações de registro de nível de rede. Com o log de NSG, você obtém as informações do:

- Logs de auditoria – esses logs são usados para exibir todas as operações enviadas a suas assinaturas Azure. Esses logs estão ativados por padrão e podem ser usados no portal do Azure.
- Logs de eventos – esses logs fornecem informações sobre quais regras NSG foram aplicadas.
- Logs de contador – esses logs avise quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

Você também pode usar [O Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados poderosa, para exibir e analisar esses logs.

Saiba Mais:

- [Análise de log de grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Resolução do nome
Resolução de nome é uma função crítica para todos os serviços que você hospedar no Azure. De uma perspectiva de segurança, compromisso da função de resolução de nome pode levar a um invasor redirecionando solicitações de seus sites para esse site. Resolução de nome seguro é um requisito para todos os seus serviços de nuvem hospedado.

Há dois tipos de resolução do nome do que endereço que você precisa:

- Resolução de nome interno – resolução de nome interno é usada pelos serviços em suas redes Virtual do Azure, suas redes local ou ambos. Nomes usados para resolução de nome interno não estão acessíveis pela Internet. Para maior segurança, é importante que seu esquema de resolução de nome interno não é acessível para usuários externos.
- Resolução de nome externo – resolução de nome externo é usada por pessoas e dispositivos fora do seu local e redes virtuais do Azure. Estes são os nomes que estão visíveis na Internet e são usados para direcionar conexão aos serviços baseados na nuvem.

Resolução de nomes interno, você tem duas opções:

- Um servidor DNS de rede Virtual do Azure – quando você cria uma nova Azure rede Virtual, um servidor DNS é criado. Este servidor DNS pode resolver os nomes das máquinas localizadas na rede Virtual Azure. Este servidor DNS não é configurável e é gerenciado pelo Gerenciador do Azure tecidos, tornando mais uma solução de resolução de nome seguro.
- Trazer seu próprio servidor DNS – você tem a opção de colocar um servidor DNS de sua escolha na sua rede Virtual do Azure. Este servidor DNS pode ser que um Active Directory integrado servidor DNS ou uma solução de servidor DNS dedicada fornecida por um parceiro Azure, que pode ser obtido do Azure Marketplace.

Saiba Mais:

- [Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)
- [Gerenciar servidores DNS usados por uma rede Virtual (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Para a resolução DNS externa, você tem duas opções:

- Hospedar seu próprios externos DNS server local
- Hospedar seu próprio servidor DNS externo com um provedor de serviços

Muitas grandes empresas hospedará seu próprios DNS servidores local. Eles podem fazer isso porque eles têm a experiência de rede e presença global para fazê-lo.

Na maioria dos casos, é melhor hospedar seus serviços de resolução de nome DNS com um provedor de serviço. Esses provedores de serviços têm o conhecimento de rede e presença global para garantir muito alta disponibilidade dos seus serviços de resolução de nome. Disponibilidade é essencial para serviços DNS porque se seus serviços de resolução de nome falharem, ninguém poderão acessar seu voltada para serviços de Internet.

Azure fornece um altamente disponível e desempenho solução DNS externa no formulário do Azure DNS. Essa solução de resolução de nome externo tira proveito da infraestrutura DNS do Azure em todo o mundo. Ele permite que você hospede seu domínio no Azure usando as mesmas credenciais, APIs, ferramentas e cobrança como outros serviços Azure. Como parte do Azure, ele também herda os controles de segurança forte incorporados à plataforma.

Saiba Mais:

- [Visão geral do DNS Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitetura de DMZ
Muitas empresas usam DMZs para segmento suas redes para criar uma zona de buffer entre a Internet e seus serviços. A parte de DMZ da rede é considerada uma zona de segurança baixa e sem ativos de alto valor são colocados no segmento da rede. Normalmente, você verá dispositivos de segurança de rede que têm uma interface de rede no segmento DMZ e outra interface de rede conectado a uma rede com máquinas virtuais e serviços que aceitam conexões de entrada da Internet.

Há um número de variações de design de DMZ e a decisão de implantar uma DMZ, e, em seguida, qual tipo de DMZ usar se você decidir usar um, baseado em seus requisitos de segurança de rede.

Saiba Mais:

- [Serviços de nuvem da Microsoft e a segurança de rede](../best-practices-network-security.md)

## <a name="azure-security-center"></a>O Centro de segurança do Azure
Central de segurança ajuda a evitar, detectar e responder a ameaças e fornece que maior visibilidade e controle sobre, a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

O Centro de segurança do Azure ajuda você a otimizar e monitorar a segurança de rede por:

- Fornecendo recomendações de segurança de rede
- Monitorando o estado da configuração de segurança de rede
- Alerta sobre a rede baseada ameaças ambas nos níveis de ponto de extremidade e da rede

Saiba Mais:

- [Introdução à Central de segurança do Azure](../security-center/security-center-intro.md)
