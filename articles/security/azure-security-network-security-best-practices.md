<properties
   pageTitle="Práticas recomendadas de segurança de rede Azure | Microsoft Azure"
   description="Este artigo fornece um conjunto de práticas recomendadas para usar de segurança de rede criados em funcionalidades do Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Práticas recomendadas de segurança de rede Azure

Microsoft Azure permite conectar máquinas virtuais e eletrodomésticos a outros dispositivos de rede, colocando-as em redes Virtual do Azure. Uma rede Virtual do Azure é uma construção de uma rede virtual que permite que você se conecte placas de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP/IP entre dispositivos de rede habilitado. Azure máquinas virtuais conectado a uma rede Virtual do Azure são capazes de conectar dispositivos em rede Virtual mesmo do Azure, Azure Virtual redes diferentes, na Internet ou até mesmo em seus próprio redes locais.

Neste artigo, abordaremos uma coleção de práticas recomendadas de segurança de rede Azure. Estas práticas recomendadas derivadas das nossa experiência com redes Azure e experiências de clientes como você mesmo.

Cada prática recomendada, explicaremos:

- Qual é a prática recomendada
- Por que você deseja habilitar essa prática recomendada
- O que pode ser o resultado se você não conseguir habilitar a prática recomendada
- Possíveis alternativas à prática recomendada
- Como você pode aprender habilitar a prática recomendada

Este artigo de práticas recomendadas de segurança de rede do Azure baseia-se em uma opinião de consenso e plataforma Windows Azure conjuntos de recursos, como elas existem no momento que este artigo foi escrito. Tecnologias e opiniões alterar ao longo do tempo e este artigo será atualizado com regularidade para refletir essas alterações.

Azure rede práticas recomendadas de segurança abordadas neste artigo incluem:

- Logicamente sub-redes de segmento
- Controlar o comportamento de roteamento
- Habilitar túnel forçada
- Usar os dispositivos de rede Virtual
- Implantar DMZs para zoneamento de segurança
- Evitar a exposição à Internet com links WAN dedicados
- Otimizar o desempenho e o tempo de atividade
- Usar balanceamento de carga global
- Desabilitar o acesso RDP ao Azure máquinas virtuais
- Ativar o Centro de segurança do Azure
- Estender Data Center no Azure


## <a name="logically-segment-subnets"></a>Logicamente sub-redes de segmento

[Redes virtuais Azure](https://azure.microsoft.com/documentation/services/virtual-network/) são semelhantes a uma LAN na sua rede local. A ideia por trás de uma rede Virtual do Azure é que você criar uma única IP endereço baseado em espaço rede privada no qual você pode colocar todas as suas [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/). Os espaços de endereço IP particulares disponíveis estão na classe A (10.0.0.0/8), classe B (172.16.0.0/12) e classe C intervalos (192.168.0.0/16).

Semelhante ao que você faça local, você desejará segmento o espaço de endereço maior em sub-redes. Você pode usar [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) com base em sub-redes princípios para criar suas sub-redes.

Roteamento entre sub-redes acontecerá automaticamente e você não precisa configurar manualmente as tabelas de roteamento. Entretanto, a configuração padrão é que não há nenhum controle de acesso de rede entre as sub-redes que você criar em uma rede Virtual Azure. Para criar controles de acesso de rede entre sub-redes, você precisará colocar algo entre as sub-redes.

Uma das coisas que você pode usar para realizar esta tarefa é um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs são dispositivos de inspeção de pacotes com informações de estado simples que usam a 5-tupla (o IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) abordagem para criar Permitir/Negar regras para tráfego de rede. Você pode permitir ou negar tráfego de e único endereço IP e de vários endereços IP ou até mesmo e de sub-redes inteiras.

Usar NSGs para controle de acesso de rede entre sub-redes permite colocar recursos que pertencem à mesma zona de segurança ou função em suas próprias sub-redes. Por exemplo, considere um aplicativo de nível 3 simples que tem uma camada de web, uma camada de lógica de aplicativo e um nível de banco de dados. Colocar máquinas virtuais que pertencem a cada um desses níveis em suas próprias sub-redes. Você usar NSGs para controlar o tráfego entre as sub-redes:

- Web camada virtual máquinas só pode iniciar conexões para as máquinas de lógica de aplicativo e somente pode aceitar conexões da Internet
- Máquinas de virtuais de lógica de aplicativo só podem iniciar conexões com o nível de banco de dados e só pode aceitar conexões da camada da web
- Máquinas de virtuais de nível de banco de dados não é possível iniciar uma conexão com algo fora da sua própria sub-rede e pode aceitar apenas conexões de camada de lógica de aplicativo

Para saber mais sobre grupos de segurança de rede e como você pode usá-los para segmento logicamente suas redes Virtual do Azure, leia o artigo [o que é um grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento

Quando você colocar uma máquina virtual em uma rede Virtual do Azure, você notará que a máquina virtual pode conectar a qualquer outra máquina virtual em uma mesma Azure rede Virtual, mesmo se as outras máquinas virtuais estão em sub-redes diferentes. O motivo por que isso é possível é que não há uma coleção de rotas de sistema que estão ativados por padrão que permitem esse tipo de comunicação. Essas rotas padrão permitem máquinas virtuais em uma rede Virtual Azure mesmo para iniciar as conexões com os outros e com a Internet (para comunicações de saída apenas à Internet).

Embora as rotas de sistema padrão são úteis para muitos cenários de implantação, há ocasiões quando você deseja personalizar a configuração de roteamento para suas implantações. Essas personalizações permitirá que você configure o endereço do próximo salto para atingir destinos específicos.

Recomendamos que você configure rotas definidas do usuário quando você implanta um dispositivo de segurança de rede virtual, quais falaremos sobre em uma prática recomendada posterior.

> [AZURE.NOTE] Rotas de definidos pelo usuário não são necessárias e as rotas de sistema padrão funcionarão na maioria dos casos.

Você pode saber mais sobre rotas de definidas pelo usuário e como configurá-los lendo o artigo [o que são rotas de definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Habilitar túnel forçada

Para entender melhor túnel forçada, é útil entender é que "dividir túnel".
O exemplo mais comum de túnel em divisão é visto com conexões VPN. Imagine que você estabelecer uma conexão VPN na sua sala de hotel à sua rede corporativa. Esta conexão permite que você se conectar aos recursos na sua rede corporativa e todas as comunicações aos recursos na sua rede corporativa vá através do túnel VPN.

O que acontece quando você deseja se conectar aos recursos na Internet? Quando túnel em divisão está habilitado, as conexões vá diretamente à Internet e não pelo túnel VPN. Alguns especialistas em segurança considere essa opção para ser um risco e, portanto, recomendamos que túnel em divisão seja desabilitado e todas as conexões, aqueles destinado à Internet e aqueles destinados a recursos corporativos, vá através do túnel VPN. A vantagem de fazer isso é que as conexões com a Internet, em seguida, são obrigados por meio dos dispositivos de segurança de rede corporativa, que não seria o caso se o cliente VPN conectado à Internet fora do túnel VPN.

Agora vamos trazer de volta para máquinas virtuais em uma rede Virtual do Azure. As rotas padrão para uma rede Virtual do Azure permitem máquinas virtuais iniciar o tráfego para a Internet. Isso também pode representar um risco de segurança, como estas conexões de saída podem aumentar a superfície de ataque de uma máquina virtual e ser aproveitados por ataques.
Por esse motivo, recomendamos que você habilite túnel forçada nas máquinas virtuais quando você tiver conectividade entre locais entre sua rede Virtual do Azure e sua rede local. Falaremos sobre cruzada conectividade local mais tarde neste documento de práticas recomendadas rede Azure.

Se você não tiver uma conexão local cruzada, certificar-se de que você tire proveito dos grupos de segurança de rede (discutido anteriormente) ou Azure virtual dispositivos de segurança (próximo discutido) para evitar conexões de saída com a Internet do seu máquinas virtuais do Azure de rede.

Para saber mais sobre forçado túnel e como ativá-lo, leia o artigo [Configurar forçado túnel usando o PowerShell e Gerenciador de recursos do Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utilização de dispositivos de rede virtual

Enquanto o usuário definido roteamento e grupos de segurança de rede podem fornecer uma determinada medida de segurança de rede nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), haverá ser situações em que você vai desejado ou necessário habilitar a segurança em altos níveis da pilha. Nesses casos, é recomendável que você implantar dispositivos de segurança de rede virtual fornecidos por parceiros Azure.

Dispositivos de segurança de rede Azure podem oferecer significativamente aprimorados níveis de segurança sobre o que é fornecido pela controles de nível de rede. Alguns dos recursos de segurança de rede fornecidos por dispositivos de segurança de rede virtual incluem:

- Firewall
- Detecção de intrusos/prevenção de intrusos
- Gerenciamento de vulnerabilidades
- Controle do aplicativo
- Detecção de anomalias baseada em rede
- Filtragem de Web
- Antivírus
- Proteção de botnet

Se você solicitar um nível mais alto de segurança de rede que você pode obter com controles de nível de acesso de rede, é recomendável que você investigar e implantar dispositivos de segurança de rede virtual Azure.

Para saber sobre quais dispositivos de segurança de rede virtual Azure estão disponíveis e sobre seus recursos, visite o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure por "segurança" e "rede".

##<a name="deploy-dmzs-for-security-zoning"></a>Implantar DMZs para zoneamento de segurança
DMZ ou "rede de perímetro" é um segmento de rede física ou lógica que foi projetado para fornecer uma camada adicional de segurança entre seus ativos e a Internet. A intenção de DMZ é colocar dispositivos de controle de acesso de rede especializadas na borda da rede DMZ, para que somente tráfego desejado é permitido passar o dispositivo de segurança de rede e na sua rede Virtual do Azure.

DMZs são úteis porque você pode se concentrar o gerenciamento de controle de acesso de rede, monitoramento, log e relatório nos dispositivos na borda da sua rede Virtual do Azure. Aqui você faria normalmente habilitar prevenção de DDoS, sistemas de prevenção de detecção/intrusos intrusos (IDS/IPS), regras de firewall e políticas, filtragem da web, antimalware de rede e muito mais. Os dispositivos de segurança de rede estabelecidos entre a Internet e sua rede Virtual do Azure e tem uma interface em ambas as redes.

Embora esse seja o projeto básico de DMZ, há vários designs DMZ diferentes, como em frente e verso, hospedados tri, com diversas bases e outras pessoas.

É recomendável para todas as implantações de alto nível de segurança que você considere Implantando DMZ para aprimorar o nível de segurança de rede para os seus recursos Azure.

Para saber mais sobre DMZs e como implantá-los no Azure, leia o artigo de [segurança de rede e serviços de nuvem da Microsoft](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet com links WAN dedicados
Muitas organizações escolheu a rota de TI híbrida. Em TI híbridos, alguns dos ativos de informações da empresa estão em Azure, enquanto outros permanecem no local. Em muitos casos alguns componentes de um serviço serão executados no Azure enquanto outros componentes permanecem no local.

No cenário de TI híbrida, normalmente há algum tipo de conectividade entre locais. Isso entre locais conectividade permite que a empresa conectar suas redes locais a redes virtuais do Azure. Existem duas soluções de conectividade entre locais disponíveis:

- VPN to-site
- Rota expressa

[To-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa uma conexão privada virtual entre sua rede local e uma rede Virtual do Azure. Esta conexão ocorrerá pela Internet e permite que você "túnel" informações dentro de uma conexão criptografada entre sua rede e Azure. To-site VPN é uma tecnologia desenvolvida segura que tenha sido implantada por empresas de todos os portes por décadas. Criptografia de túnel é executada usando [o modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Enquanto-to-site VPN é uma tecnologia confiável, confiável e estabelecida, o tráfego dentro do túnel percorrer na Internet. Além disso, a largura de banda relativamente é restringida a um máximo de sobre 200Mbps.

Se você solicitar um nível excepcional de segurança ou desempenho para suas conexões entre locais, recomendamos que você use rota expressa do Azure para a conectividade entre locais. Rota expressa é uma WAN dedicada vínculo entre um provedor de hospedagem do Exchange ou seu local na. Como isso é uma conexão de telecomunicações, seus dados não viagem pela Internet e, portanto, não são expostos para os riscos potenciais de comunicações da Internet.

Para saber mais sobre como funciona a rota expressa do Azure e como implantar, leia o artigo de [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Confidencialidade, integridade e disponibilidade (CIA) compõem o triplo mais influente do hoje modelo de segurança. Confidencialidade é sobre criptografia e privacidade, integridade se lembrando-se de que dados não são alterados por pessoas não autorizadas e disponibilidade é sobre lembrando-se de que as pessoas autorizadas sejam capazes de acessar as informações que estão autorizados a acessar. Falha em qualquer uma dessas áreas representa uma violação potencial em segurança.

Disponibilidade pode ser considerada como sendo sobre atividade e desempenho. Se um serviço for pressionada, informações não podem ser acessadas. Se o desempenho for ruim assim como para tornar os dados inutilizável, podemos pode considerar os dados a serem inacessível. Portanto, de uma perspectiva de segurança, precisamos fazer tudo o que podemos para garantir que nossos serviços têm desempenho e tempo de atividade ideal.
Um método popular e eficaz usado para melhorar o desempenho e a disponibilidade é usar balanceamento de carga. Balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores web front-end como parte do seu serviço, você pode usar a balanceamento de carga para distribuir o tráfego em vários servidores web front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque se um dos servidores web ficar indisponível, o balanceador de carga interromperá enviando o tráfego para o servidor e redirecionar o tráfego para os servidores que estão ainda online. Também balanceamento de carga de ajuda o desempenho, porque o processador, rede e memória sobrecarga para servir solicitações é distribuída todas a carga equilibrada servidores.

Recomendamos que você utilizam balanceamento sempre que puder e conforme apropriado para seus serviços. Vamos abordá adequado nas seções a seguir.
No nível de rede Virtual do Azure, Azure fornece que carregar com três primário balanceamento opções:

- Balanceamento de carga de HTTP
- Balanceamento de carga de externa
- Balanceamento de carga de interna

## <a name="http-based-load-balancing"></a>Balanceamento de carga de HTTP
Balanceamento de carga baseada em HTTP baseia decisões sobre qual servidor para enviar as conexões usando características do protocolo HTTP. Azure tem um balanceador de carga HTTP que vai pelo nome do Gateway de aplicativo.

Recomendamos que você nos Azure Application Gateway quando:

- Aplicativos que exigem solicitações da sessão de usuário/cliente mesmo alcançar a mesma máquina virtual back-end. Exemplos de isso deve ser compras aplicativos do carrinho e servidores de email da web.
- Aplicativos que deseja liberar server farms da web de sobrecarga de encerramento SSL tirando proveito do recurso de [SSL descarregar](https://f5.com/glossary/ssl-offloading) do Gateway do aplicativo.
- Aplicativos, como uma rede de distribuição de conteúdo, que exigem várias solicitações HTTP sobre a conexão de TCP demorada mesmo ser circulados ou carregar equilibrada para diferentes servidores de back-end.

Para saber mais sobre como funciona o Gateway de aplicativo do Azure e como você pode usá-lo nas implantações, leia o artigo de [Visão geral de Gateway do aplicativo](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Balanceamento de carga de externa
Balanceamento de carga externa ocorrerá quando as conexões de entrada da Internet são carga equilibrada entre seus servidores localizados em uma rede Virtual do Azure. Balanceador de carga externo do Azure pode fornecer essa capacidade e recomendamos que você usá-lo quando você não exija que as sessões conjunta ou descarregar SSL.

Em contraste com balanceamento de carga baseado em HTTP, o balanceador de carga externos usa as informações nas camadas de rede e de transporte do modelo de rede OSI para tomar decisões no qual servidor carregar conexão de saldo para.

Recomendamos que você use balanceamento externo sempre que tiver [aplicativos sem estado](http://whatis.techtarget.com/definition/stateless-app) aceitar solicitações de entrada da Internet.

Para saber mais sobre como funciona o balanceador de carga externo do Azure e como você pode implantar, por favor leia o artigo [Get Started criando um balanceador de carga opostas do Internet no Gerenciador de recursos usando o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Balanceamento de carga de interna
Balanceamento de carga interna é semelhante ao balanceamento de carga externa e usa o mesmo mecanismo para carregar saldo conexões com os servidores atrás deles. A única diferença é que o balanceador de carga nesse caso está aceitando conexões de máquinas virtuais que não estão na Internet. Na maioria dos casos, as conexões que são aceitos para balanceamento de carga são iniciadas por dispositivos em uma rede Virtual do Azure.

Recomendamos que você use interna balanceamento de carga para cenários que irá se beneficiar com esse recurso, como quando você precisar carregar conexões de saldo para servidores SQL ou servidores internos da web.

Para saber mais sobre como funciona a balanceamento de carga interno do Azure e como você pode implantá-lo, leia o artigo [Get Started criando um balanceador de carga interno usando o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Usar balanceamento de carga global
Torna computação de nuvem pública possível implantar globalmente aplicativos distribuídos que têm componentes localizados na data centers em todo o mundo. Isso é possível no Microsoft Azure devido a presença do data center global do Azure. Em contraste com a tecnologias mencionadas anteriormente de balanceamento de carga, balanceamento de carga global possibilita disponibilizar serviços mesmo quando dos data centers inteiros poderão ficar indisponíveis.

Você pode obter esse tipo global de balanceamento de carga no Azure aproveitando [Gerenciador de tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Gerenciador de tráfego faz é possível carregar conexões de saldo para seus serviços baseados no local do usuário.

Por exemplo, se o usuário está fazendo uma solicitação de serviço da UE, a conexão é direcionado para seus serviços localizados em um data center da UE. Essa parte do tráfego Gerenciador global carregar balanceamento ajuda a melhorar o desempenho porque se conectando ao data center mais próximo é mais rápido que conectar-se dos data centers que são mais longe.

No lado disponibilidade, balanceamento de carga global garante que o seu serviço está disponível mesmo se um data center inteiro deverá ficar disponível.

Por exemplo, se um data center Azure deve ficar indisponível devido a motivos ambientais ou devido a interrupções (como falhas de rede regional), conexões ao serviço seriam redirecionados para o mais próximo data center online. Este balanceamento de carga global é realizado aproveitando de políticas DNS que você pode criar no Gerenciador de tráfego.

Recomendamos que você use o Gerenciador de tráfego para desenvolver qualquer solução de nuvem que tem um escopo amplamente distribuído entre várias regiões e requer o nível mais alto de atividade possível.

Para saber mais sobre o Gerenciador de tráfego do Azure e como implantá-lo, leia o artigo [o que é Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Desabilitar o acesso RDP/SSH ao Azure máquinas virtuais
É possível acessar máquinas virtuais do Azure usando o [Protocolo de área de trabalho remota](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e os protocolos [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Esses protocolos possibilitam a gerenciar máquinas virtuais de locais remotos e são padrão na computação do data center.

O problema de segurança potencial com o uso desses protocolos pela Internet é que ataques podem usar várias técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso a máquinas virtuais do Azure. Depois de acessar as ataques, podem usar sua máquina virtual como um ponto de partida para comprometer outros computadores na rede Virtual Azure ou até mesmo atacar dispositivos de rede fora do Azure.

Por isso, recomendamos que você desabilite acesso direto RDP e SSH para suas máquinas virtuais do Azure da Internet. Depois de acesso direto de RDP e SSH da Internet estiver desabilitado, você tem outras opções que você pode usar para acessar essas máquinas virtuais para gerenciamento remoto:

- Ponto-a-site VPN
- VPN to-site
- Rota expressa

[Ponto-a-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma conexão de cliente/servidor VPN de acesso remoto. Uma VPN de ponto-a-site permite que um único usuário para se conectar a uma rede Virtual do Azure pela Internet. Depois de estabelecer a conexão ponto-a-site, o usuário poderá usar RDP ou SSH para conectar-se a qualquer máquinas virtuais localizadas em uma rede Virtual Azure que o usuário conectado via VPN de ponto-a-site. Isso pressupõe que o usuário está autorizado a acessar essas máquinas virtuais.

Ponto-a-site VPN é mais seguro que conexões RDP ou SSH diretas porque o usuário tem que autenticar duas vezes antes de conectar a uma máquina virtual. Primeiro, o usuário precisa autenticar (e ser autorizada) para estabelecer a conexão de VPN ponto-a-site; segundo, o usuário precisa autenticar (e ser autorizada) para estabelecer a sessão RDP ou SSH.

Uma [VPN to-site](../vpn-gateway/vpn-gateway-site-to-site-create.md) se conecta a uma rede inteira para outra rede pela Internet. Você pode usar uma VPN to-site para conectar sua rede local a uma rede Virtual do Azure. Se você implantar uma VPN to-site, os usuários na sua rede local poderão se conectar a máquinas virtuais em sua rede Virtual do Azure usando o protocolo RDP ou SSH sobre a conexão de VPN-to-site e não exige que você permitir acesso direto de RDP ou SSH pela Internet.

Você também pode usar uma conexão WAN dedicada para fornecer funcionalidade similar à VPN to-site. As principais diferenças são 1. o link WAN dedicado não percorrer a Internet e 2. links da WAN dedicados normalmente são mais estável e desempenho. Azure fornece uma solução de conexão WAN dedicada na forma de [rota expressa](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Ativar o Centro de segurança do Azure
O Centro de segurança do Azure ajuda você a evitar, detectar e responder a ameaças e fornece que maior visibilidade e controle sobre, a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

O Centro de segurança do Azure ajuda você a otimizar e monitorar a segurança de rede por:

- Fornecendo recomendações de segurança de rede
- Monitorando o estado da configuração de segurança de rede
- Alerta sobre a rede baseada ameaças ambas nos níveis de ponto de extremidade e da rede

É altamente recomendável que você habilite o Centro de segurança do Azure para todas as implantações do Azure.

Para saber mais sobre o Centro de segurança do Azure e como ativá-lo para as implantações, leia o artigo [Introdução à Central de segurança do Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Estender com segurança seu data center no Azure
Muitos enterprise IT as organizações procuram para expandir para a nuvem em vez de crescimento seus centros de dados locais. Essa expansão representa uma extensão de infraestrutura existente para a nuvem pública. Aproveitando local entre opções de conectividade é possível tratar suas redes Virtual do Azure como apenas outra sub-rede na sua infraestrutura de rede local.

No entanto, há muitos problemas de planejamento e design que precisam ser abordados primeiro. Isso é especialmente importante na área de segurança de rede. Uma das melhores maneiras de entender como abordar tal um design é ver um exemplo.

Microsoft criou o [Diagrama de arquitetura de referência de extensão do data center](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e apoio para ajudá-lo a compreender como tal uma extensão de data center ficaria. Isso fornece um exemplo de implementação de referência que você pode usar para planejar e desenvolver uma extensão de data center corporativo seguro para a nuvem. Recomendamos que você leia este documento para ter uma ideia dos principais componentes de uma solução segura.

Para saber mais sobre como estender com segurança seu data center no Azure, veja o vídeo [Estendendo sua análise ao Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
